<template>
  <div :class="`d-flex flex-row justify-${justify}`">
    <div
      v-for="die in dice"
      class="mx-2 clickable"
      @click="dieClicked(die)"
    >
      <v-icon
        size="75"
        :color="die.active ? color : 'grey lighten-2'"
        :style="`border: 3px ${die.borderColor || 'transparent'} solid; border-radius: 10px;`"
      >
        mdi-dice-{{ die.value }}
      </v-icon>
<!--      <v-icon color="grey" size="110" style="margin: -14px 0 0 -96px; position: absolute;">mdi-slash-forward</v-icon>-->
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Watch, Vue } from "nuxt-property-decorator"
import { Die } from "types/types"

const NUM_SIDES = 6

@Component
export default class Dice extends Vue {
  @Prop({ default: 3 }) numDice!: number
  @Prop({ default: 'red darken-2' }) color!: string
  @Prop({ default: 3 }) maxActive!: number
  @Prop({ default: 'center' }) justify!: string

  dice: Die[] = []

  @Watch('numDice', { immediate: true })
  init() {
    this.dice = []
    for (let i = 0; i < this.numDice; i++) {
      this.dice.push({ value: NUM_SIDES, active: true, borderColor: '' })
    }
    this.enforceDiceActive()
  }

  enforceDiceActive() {
    for (let i = this.dice.length - 1; i >= 0; i--) {
      if (this.numActiveDice > this.maxActive) {
        this.dice[i].active = false
      }
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
    } else if (this.numActiveDice < this.maxActive) {
      die.active = true
    }
  }

  public roll(): Die[] {
    this.enforceDiceActive()
    for (const die of this.dice) {
      die.borderColor = ''
      if (die.active) {
        die.value = Math.floor(Math.random() * NUM_SIDES) + 1
      }
    }
    this.dice.sort((a, b) => {
      let result = -1
      result = a.active && !b.active ? -1 : 1
      if (a.active && b.active) {
        result = a.value > b.value ? -1 : 1
      }
      return result
    })
    return this.dice.filter((die: Die) => die.active)
  }
}
</script>
<style>
  .clickable:hover {
    cursor: pointer;
  }
</style>
