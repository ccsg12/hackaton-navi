<template>
  <v-app>
    <v-app-bar
        app
        color="secondary"
        dark
        dense
        hide-on-scroll
    >
      <div class="d-flex align-center">
        <router-link to="#">
          <v-toolbar-title>Clagedis</v-toolbar-title>
        </router-link>
      </div>
    </v-app-bar>

    <v-main>
      <router-view/>
    </v-main>
  </v-app>
</template>

<script>
import "./styles"

export default {
  name: 'App',
};
</script>
