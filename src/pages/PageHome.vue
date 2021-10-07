<template>
  <q-page class="constrain q-pa-md">
    <div class="row q-col-gutter-md">
      <div class="col-12 col-sm-8">

        <q-card
          v-for="post in posts"
          :key="post.id"
          class="my-card q-mb-sm"
          flat
          bordered
          
          >
          <q-item>
            <q-item-section avatar>
              <q-avatar>
                <img src="https://cdn.quasar.dev/img/boy-avatar.png">
              </q-avatar>
            </q-item-section>

            <q-item-section>
              <q-item-label>
                {{ post.caption }}
              </q-item-label>
              <q-item-label caption>
                {{ post.location }}
              </q-item-label>
            </q-item-section>
          </q-item>

          <q-separator />

          <q-img :src="post.imageUrl" />

          <q-card-section>
            <div>{{ post.caption }}</div>
            <div class="text-caption text-grey">{{ post.date }}</div>
          </q-card-section>

        
        </q-card>
      </div>
      <div class="col-4">
        <q-item class="fixed">
            <q-item-section avatar>
              <q-avatar>
                <img src="https://cdn.quasar.dev/img/boy-avatar.png">
              </q-avatar>
            </q-item-section>

            <q-item-section>
              <q-item-label>
                Honoriuz
              </q-item-label>
              <q-item-label caption>
                Cazenga, Luanda
              </q-item-label>
            </q-item-section>
          </q-item>
      </div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent } from 'vue';
import { date } from 'quasar'

export default defineComponent({
  name: 'PageHome',
  data () {
    return {
      posts: []
    }
  },
  computed: {
    getData(value){
      return date.formatDate(value, 'YYYY-MM-DDTHH:mm:ss.SSSZ')
    }
  },
  methods: {
    getPosts() {
      this.$axios.get(`${ process.env.API }/posts`).then(response => {
        this.posts = response.data
      })
    }
  },
  created() {
    this.getPosts()
  }
})
</script>

<style lang="scss">
  .my-card {
    .q-img{
      min-height: 200px;
    }
  }
</style>
