<template>
  <v-app dark>
    <v-app-bar :clipped-left="clipped" fixed app>
      <v-avatar color="primary" size="30">
        <v-icon dark> mdi-account-circle </v-icon>
      </v-avatar>
      <v-toolbar-title class="ml-4" v-text="title" />
      <v-spacer />
    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-footer :absolute="!fixed" app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      miniVariant: false,
      title: 'Farjistik',
    }
  },
  methods: {
    getLocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(this.showPosition)
      } else {
        x.innerHTML = 'Geolocation is not supported by this browser.'
      }
    },

    showPosition(position) {
      localStorage.setItem(
        'latlong',
        position.coords.latitude + ',' + position.coords.longitude
      )
    },
  },
  mounted() {
    this.getLocation()
  },
}
</script>
