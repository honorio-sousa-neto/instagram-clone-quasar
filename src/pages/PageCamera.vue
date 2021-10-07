<template>
  <q-page class="constrain-more q-pa-md">
    <div class="camera-frame q-pa-md">
      <video
        v-show="!capturedImage"
        ref="video"
        class="full-width"
        autoplay
        playsinline
      />
      <canvas
        v-show="capturedImage"
        ref="canvas"
        class=full-width
        height="230"
      />
    </div>
    <div class="text-center q-mt-lg">
      <q-btn
        v-if="hasCameraSupport"
        @click="captureImage"
        :disabled="capturedImage"
        round
        color="grey-10"
        icon="eva-camera" 
      />
      <q-file
        v-else
        outlined
        label="Choose an image"
        accept="image/*"
        v-model="imageUpload"
        @input="captureImageFallback"
      >
        <template v-slot:prepend>
          <q-icon name="eva-attach-outline"/>
        </template>
      </q-file>
      <div class="row justify-center q-ma-md">
        <q-input
          v-model="post.caption"
          class="col col-sm-6"
          dense
          label="Caption" 
        />
      </div>
      <div class="row justify-center q-ma-md">
        <q-input
          :loading="loadingState"
          v-model="post.location"
          class="col col-sm-6"
          dense
          label="Location" 
        >
          <template v-slot:append>
            <q-btn
              v-if="!loadingState"
              @click="getLocation"
              round
              dense
              flat
              icon="eva-navigation-2-outline" />
          </template>
          </q-input>
      </div> 
       <div class="row justify-center q-mt-lg">
         <q-btn
           :disabled="!capturedImage && !post.caption"
           @click="addPost"
           unelevated
           rounded
           color="primary"
           label="Add Post" />
       </div>
    </div>
    
  </q-page>
</template>

<script>
import { defineComponent } from 'vue';
import { uid } from 'quasar'
import { useQuasar } from 'quasar'

export default defineComponent({
  name: 'PageCamera',
  data () {
    return {
      post: {
        id: uid(),
        caption: '',
        location: '',
        photo: null,
        date: Date.now()
      },
      capturedImage: false,
      imageUpload: [],
      hasCameraSupport: true,
      loadingState: false
    }
  },
  setup () {
    const $q = useQuasar()

    function alert () {
      $q.dialog({
        title: 'Alert',
        message: 'Some message'
      })
    }
  },
  methods: {
    initCamera(){
      navigator.mediaDevices.getUserMedia({
        video: true
      }).then(stream => {
        this.$refs.video.srcObject = stream
      }).catch(error => {
        this.hasCameraSupport = false
      })
    },
    captureImage(){
      let video = this.$refs.video
      let canvas = this.$refs.canvas

      video.width = video.getBoundingClientRect().width
      video.height = video.getBoundingClientRect().height

      let context = canvas.getContext('2d')
      context.drawImage(video,0,0,video.width,video.height)

      this.capturedImage = true
      this.post.photo = this.dataURItoBlob(canvas.toDataURL())
      this.turnOffCam()
    },
    turnOffCam(){
      this.$refs.video.srcObject.getVideoTracks().forEach(track => {
        track.stop()
      })
    },
    captureImageFallback(file){
      
      this.post.photo = file.path[0].files[0]
      let canvas = this.$refs.canvas
      let context = canvas.getContext('2d')


      var reader = new FileReader()
      reader.onload = event => {
        var img = new Image()
        img.onload = () => {
          canvas.width = img.width
          canvas.height = img.height
          context.drawImage(img,0,0)
          this.capturedImage=true
        }
        img.src = event.target.result
      }
      reader.readAsDataURL(file.path[0].files[0])
    },
    getLocation(){
      this.loadingState = true
      navigator.geolocation.getCurrentPosition(position => {
        this.getCityAndCountry(position)
      }, err => {
        alert()
      }, { timeout: 10000 })
    },
    getCityAndCountry(position){
      let apiUrl = `https://geocode.xyz/${position.coords.latitude},${position.coords.longitude}?json=1`
      this.$axios.get(apiUrl).then(response => {
        this.post.location = response.data.city
        if (response.data.country)
          this.post.location += `, ${response.data.country}`
        this.loadingState = false
      }).catch(err=>{
        alert()
      })
    },
    dataURItoBlob(dataURI) {
      // convert base64 to raw binary data held in a string
      // doesn't handle URLEncoded DataURIs - see SO answer #6850276 for code that does this
      var byteString = atob(dataURI.split(',')[1]);

      // separate out the mime component
      var mimeString = dataURI.split(',')[0].split(':')[1].split(';')[0]

      // write the bytes of the string to an ArrayBuffer
      var ab = new ArrayBuffer(byteString.length);

      // create a view into the buffer
      var ia = new Uint8Array(ab);

      // set the bytes of the buffer to the correct values
      for (var i = 0; i < byteString.length; i++) {
          ia[i] = byteString.charCodeAt(i);
      }

      // write the ArrayBuffer to a blob, and you're done
      var blob = new Blob([ab], {type: mimeString});
      return blob;

    },
    addPost() {

      let formData = new FormData()
      formData.append('id', this.post.id)
      formData.append('caption', this.post.caption)
      formData.append('location', this.post.location)
      formData.append('date', this.post.date)
      formData.append('file', this.post.photo, this.post.id + '.png')

      this.$axios.post(`${ process.env.API }/createPost`, formData).then(response => {
        console.log(response)
        this.$router.push('/')
      }).catch(error => {
        console.log(error)
      })
    }
  },
  mounted () {
    this.initCamera()
  },
  beforeUnmount(){
    if(this.hasCameraSupport) 
      this.turnOffCam()
  }
})
</script>

<style lang="scss">
  .camera-frame {
    border: 2px solid $grey-10;
    border-radius: 10px;
  }
</style>
