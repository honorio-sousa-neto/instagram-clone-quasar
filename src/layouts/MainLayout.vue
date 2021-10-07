<template>
  <q-layout view="lHh Lpr lFf">
    <q-header
      class="bg-white text-grey-10"
    >
      <q-toolbar dense class="constrain">
        <q-btn
          to="/camera"
          class="large-screen-only q-mr-sm"
          icon="eva-camera-outline"
          flat
          size="18px"
          round
          dense
        />

        <q-separator vertical spaced class="large-screen-only"/>
        <q-toolbar-title class="text-grand-hotel text-bold"> 
          InstagramClone
        </q-toolbar-title>

        <q-btn
          to="/"
          class="large-screen-only"
          icon="eva-home-outline"
          flat
          round
          size="18px"
          dense
        />
      </q-toolbar>
    </q-header>


    <q-page-container class="bg-grey-1">
      <router-view />
    </q-page-container>

    <q-footer
      class="bg-white small-screen-only"
      bordered
      >
          
      <q-tabs
        class="text-grey-10"
        active-color="primary"
        indicate-color="transparent"
        >
        <q-route-tab
          to="/"
          icon="eva-home-outline" 
        />
        <q-route-tab
          to="/camera"
          icon="eva-camera-outline"
        />
      </q-tabs>
    
    </q-footer>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from "vue";


export default defineComponent({
  name: "MainLayout"
  
});
</script>

<style lang="scss">
  
  .q-toolbar {
    @media (min-width: $breakpoint-sm-max){
      height: 77px;
    }
  }
  .q-toolbar__title {
    font-size: 30px;
    @media (max-width: $breakpoint-xs-max){
      text-align: center;
    }
  }
    
  .q-footer {
    .q-tab__icon{
      font-size: 30px;
    }
  }
    
</style>
