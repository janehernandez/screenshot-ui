<template>
  <q-layout view="hHh lpR fFf">

    <q-header elevated class="bg-white text-primary bg-header" height-hint="98">

      <q-toolbar>
        <q-toolbar-title>
          <img src="~assets/header.png" style="width: 100%; margin-top:10px">
        </q-toolbar-title>
      </q-toolbar>

      <q-tabs align="left">
        <q-route-tab to="/" label="Print Page" />
        <q-route-tab to="#" label="Agent Response Form" @click="downloadARF" />
      </q-tabs>
    </q-header>


    <q-page-container>
      <router-view />
    </q-page-container>

    <q-footer elevated class="bg-grey-8 text-white">
      <q-toolbar class="justify-center">
        <small>
          Copyright © 2023 by JD Team. All Rights Reserved.
        </small>
      </q-toolbar>
    </q-footer>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from 'vue'
// import EssentialLink from 'components/EssentialLink.vue'


export default defineComponent({
  name: 'MainLayout',

  setup() {
    const downloadARF = () => {
      window.open("https://res.cloudinary.com/dqlbxmeez/image/upload/v1688480662/ARF_vrao8j.png")
    }

    return {
      downloadARF
    }
  }
})
</script>
