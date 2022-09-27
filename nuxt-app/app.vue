<template>
  <v-app>
    <v-navigation-drawer app>
    </v-navigation-drawer>

    <v-app-bar app color="blue">
      <v-app-bar-title class="text-white">
        いつどこで誰が何をした Stable Diffusion デモ
      </v-app-bar-title>
    </v-app-bar>

    <v-main>

      <v-container fluid>
        <router-view></router-view>
      </v-container>
    </v-main>

    <v-footer app>
      <!-- -->
    </v-footer>
  </v-app>
</template>