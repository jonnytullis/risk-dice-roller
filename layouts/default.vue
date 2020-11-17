<template>
  <v-app light>
    <v-app-bar
      fixed
      app
      hide-on-scroll
    >
      <v-spacer v-if="$vuetify.breakpoint.xsOnly" />
      <v-toolbar-items>
        <v-btn depressed href="https://jonnytullis.com">
          <v-avatar size="40" class="mr-2">
            <v-img src="jonny-avatar.png" />
          </v-avatar>
          Meet the creator
        </v-btn>
      </v-toolbar-items>
      <v-spacer />
    </v-app-bar>
    <v-main>
      <v-container fluid class="pa-0">
        <nuxt />
      </v-container>
    </v-main>
    <v-footer app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
}
</script>
