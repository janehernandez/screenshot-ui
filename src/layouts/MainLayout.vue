<template>
  <q-layout view="hHh lpR fFf">
    <q-header elevated class="bg-white text-primary bg-header" height-hint="98">
      <q-toolbar>
        <q-toolbar-title>
          <img src="~assets/header.png" style="width: 100%; margin-top: 10px" />
        </q-toolbar-title>
      </q-toolbar>

      <q-tabs align="left">
        <q-route-tab to="/" label="Print Coaching Page" />
        <q-route-tab to="/images" label="Modify Image" />
        <q-route-tab to="#" label="Agent Response Form" @click="downloadARF" />
      </q-tabs>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>

    <q-footer elevated class="bg-grey-8 text-white">
      <q-toolbar class="justify-center">
        <small>
          Copyright © {{ year }} by {{ author }}. All Rights Reserved.
        </small>
      </q-toolbar>
    </q-footer>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from "vue";

export default defineComponent({
  name: "MainLayout",

  setup() {
    const year = ref(new Date().getFullYear());

    // please do not modify //
    const author = ref("SA - RA Dan");
    // please do not modify //

    const downloadARF = () => {
      window.open(
        "https://res.cloudinary.com/dqlbxmeez/image/upload/v1688480662/ARF_vrao8j.png"
      );
    };

    return {
      downloadARF,
      year,
      author,
    };
  },
});
</script>
