<template>
  <v-container>
    <v-row no-gutters justify="center" class="mb-2">
      <div
        v-for="die in dice"
        @click="dieClicked(die)"
      >
        <v-icon
          size="60"
          :color="die.active ? color : 'grey lighten-2'"
        >
          mdi-dice-{{ die.value }}
        </v-icon>
      </div>
    </v-row>
  </v-container>
</template>

<script lang="ts">
import { Component, Prop, Watch, Vue } from "nuxt-property-decorator"
import { Die } from "types/types"

const NUM_SIDES = 6

@Component
export default class DiceView extends Vue {
  @Prop({ default: 3 }) numDice!: number
  @Prop({ default: 'red darken-2' }) color!: string

  dice: Die[] = []

  @Watch('numDice', { immediate: true })
  init() {
    this.dice = []
    for (let i = 0; i < this.numDice; i++) {
      this.dice.push({ value: NUM_SIDES, active: true })
    }
  }

  get numActiveDice() {
    return this.dice.filter(die => die.active).length
  }

  dieClicked(die: Die) {
    // Must have at least one active die
    if (die.active) {
      if (this.numActiveDice > 1) {
        die.active = false
      }
    } else {
      die.active = true
    }
  }

  // Called in index.vue
  public roll() {
    for (const die of this.dice) {
      if (die.active) {
        die.value = Math.floor(Math.random() * NUM_SIDES) + 1
      }
    }
  }
}
</script>
