<template>
  <div id="wrapper" :style="`min-height:${window.innerHeight - 100}px;`">
    <v-container>
      <v-row justify="center" class="my-4">
        <transition name="fade">
          <v-img v-if="isMounted" src="risk-logo.png" :max-width="$vuetify.breakpoint.xsOnly ? '300' : '450'" />
        </transition>
      </v-row>
      <v-row justify="center" class="mb-6">
        <transition name="slide-fade">
          <v-card v-show="isMounted" class="pa-4" width="90%">
            <dice-roller />
          </v-card>
        </transition>
      </v-row>
    </v-container>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'nuxt-property-decorator'
import DiceRoller from '~/components/dice-roller.vue'

@Component({
  components: { DiceRoller }
})
export default class Main extends Vue {
  isMounted: boolean = false
  mounted() {
    this.isMounted = true
  }

  get window() {
    return window
  }
}
</script>
<style>
  .slide-fade-enter-active {
    transition: 2000ms ease;
    transition-delay: 1500ms;
  }
  .slide-fade-enter {
    transform: translateY(30px);
    opacity: 0;
  }

  .fade-enter-active {
    transition: 4s ease;
  }
  .fade-enter {
    opacity: 0;
  }

 .v-text-field__slot input {
   font-size: 20px;
 }

 #wrapper {
   background-image: url(../static/map.jpg);
   background-size: cover;
 }
</style>
