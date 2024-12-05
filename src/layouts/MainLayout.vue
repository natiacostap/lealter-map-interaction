<template>
  <q-layout view="hHh lpR fFf">
    <q-header elevated class="color-navbar">
      <q-toolbar>
        <div class="row items-center q-gutter-x-md">
          <img
            alt="logo-entel"
            src="src/assets/logo-entel-digital-blanco.png"
            class="logo-entel"
          />
          <img
            alt="logo-entel"
            src="src/assets/logo_js_conf.png"
            class="logo-jsconf"
          />
        </div>
        <q-toolbar-title>
          <span class="q-px-sm"> | </span>Población Flotante Maipú
        </q-toolbar-title>
      </q-toolbar>
    </q-header>
    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent } from "vue";

export default defineComponent({
  name: "MainLayout",
  setup() {
    return {};
  },
});
</script>

<style lang="scss" scope>
.color-navbar {
  background-color: #001884;
}
.logo-entel {
  width: 100%;
  max-width: 100px;
}
.logo-jsconf {
  width: 80%;
  max-width: 30px;
}
</style>
