<template>
  <v-row justify="center" align="center">
    <v-chip
      :color="attacker ? 'error' : 'black'"
      class="display-1 px-4"
      large
      dark
    >
      <div v-if="attacker">
        <v-icon large class="mr-4">mdi-sword</v-icon>
        {{ numAttackingUnits }}
      </div>
      <div v-if="defender">
        <v-icon large class="mr-4">mdi-shield</v-icon>
        {{ numDefendingUnits }}
      </div>
    </v-chip>
    <div class="mt-4" style="position: absolute; right: 30px; height: 50px; width: 50px;">
      <v-slide-x-transition>
        <v-row v-if="isWinner" class="confetti pa-3 mt-n6">
          <v-icon color="warning" x-large>mdi-trophy</v-icon>
        </v-row>
      </v-slide-x-transition>
      <v-slide-x-transition>
        <v-row v-if="isLoser" class="title pa-3 mt-n6">
          <v-icon color="black" x-large>mdi-emoticon-dead-outline</v-icon>
        </v-row>
      </v-slide-x-transition>
    </div>
  </v-row>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "nuxt-property-decorator"

@Component
export default class UnitsDisplay extends Vue {
  @Prop() defender!: boolean
  @Prop() attacker!: boolean
  @Prop({ required: true }) numDefendingUnits!: number
  @Prop({ required: true }) numAttackingUnits!: number

  get isWinner() {
    if (this.attacker) {
      if (this.numDefendingUnits <= 0 && this.numAttackingUnits > 0) {
        return true
      }
    } else if (this.defender) {
      if (this.numAttackingUnits <= 1 && this.numDefendingUnits > 0) {
        return true
      }
    }
    return false
  }

  get isLoser() {
    if (this.attacker) {
      if (this.numAttackingUnits <= 0 && this.numDefendingUnits > 0) {
        return true
      }
    } else if (this.defender) {
      if (this.numDefendingUnits <= 0 && this.numAttackingUnits > 0) {
        return true
      }
    }
    return false
  }
}
</script>
<style scoped>
  .confetti {
    background-image: url(../static/confetti.gif);
    background-size: cover;
  }
</style>
