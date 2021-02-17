<template>
  <q-layout view="lHh Lpr lFf">
    <q-header bordered>
      <q-toolbar>
        <q-btn 
        to="/camera"
        size="18px"
        icon="eva-camera-outline"
        flat round dense
         />
         <q-separator vertical spaced />
        <q-toolbar-title class="text-center">
          Quasagram
        </q-toolbar-title>

      </q-toolbar>
    </q-header>

    <q-footer class="bg-white">
      <q-tabs class="text-grey-10"
      active-color="primary"
      indicator-color="transparent"> 
        <q-route-tab
          icon="eva-home-outline"
          to="/"
          label="Home"
          exact
        />
        <q-route-tab
          icon="eva-camera-outline"
          to="/camera"
          label="Camera"
          exact
        />
      </q-tabs>
    </q-footer>

    <q-page-container class="bg-grey-1">
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>

export default {
  name: 'MainLayout',
  data () {
    return {
      leftDrawerOpen: false,
    }
  }
}
</script>
