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
    <div class="ms-6">
      <v-slide-x-transition>
        <v-row v-if="isWinner" justify="end" align="center" class="winner title px-2 mt-n6">
          <v-icon color="warning" large class="mr-2">mdi-sword-cross</v-icon>
          Victory!
        </v-row>
      </v-slide-x-transition>
      <v-slide-x-transition>
        <v-row v-if="isLoser" justify="end" align="center" class="loser title px-2 mt-n6">
          <v-icon color="black" large class="mr-2">mdi-emoticon-dead-outline</v-icon>
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
      if (this.numAttackingUnits <= 0 && this.numDefendingUnits > 0) {
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
  .winner {
    background-image: url(../static/confetti.gif);
    background-size: cover;
    height: 50px;
    position: absolute;
  }

  .loser {
    height: 50px;
    position: absolute;
  }
</style>
