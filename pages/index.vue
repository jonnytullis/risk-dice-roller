<template>
  <v-container>
    <v-row justify="center" class="mb-4">
      <v-img src="risk-logo.png" max-width="400" />
    </v-row>
    <v-row justify="center" class="mb-4">
      <v-card class="pa-4" width="100%"  max-width="500px">
        <v-row class="mb-6">
          <v-col>
            <v-text-field
              :value="numAttackers"
              autofocus
              outlined
              type="number"
              min="0"
              maxLength="4"
              label="Attacking Units"
              :rules="numberRules"
            />
          </v-col>
          <v-col cols="12" xl="8" lg="8" md="8" sm="8">
            <dice-view
              ref="attackerDice"
              class="dice"
              num-dice="3"
              :max-active="this.numAttackers"
            />
          </v-col>
        </v-row>
        <v-row :class="this.$vuetify.breakpoint.xsOnly ? 'flex-column-reverse' : ''">
          <v-col>
            <v-text-field
              :value="numDefenders"
              outlined
              type="number"
              min="0"
              maxLength="4"
              label="Defending Units"
              :rules="numberRules"
            />
          </v-col>
          <v-col cols="12" xl="8" lg="8" md="8" sm="8">
            <dice-view
              ref="defenderDice"
              class="dice"
              num-dice="2"
              color="black"
              :max-active="this.numDefenders"
            />
          </v-col>
        </v-row>
        <v-row>
          <v-spacer />
          <v-btn color="secondary" class="mr-2">
            Resolve Battle
          </v-btn>
          <v-btn @click="rollDice" color="primary">
            Roll Dice
          </v-btn>
          <v-spacer />
        </v-row>
      </v-card>
    </v-row>
  </v-container>
</template>

<script lang="ts">
import { Component, Vue } from "nuxt-property-decorator"
import Dice from "~/components/dice.vue"
import { Die } from "~/types/types"

@Component({
  components: { DiceView: Dice }
})
export default class Main extends Vue {
  numAttackers: number = 3
  numDefenders: number = 2

  numberRules = [
    (v: string) => !isNaN(Number.parseInt(v)) || 'Invalid number',
    (v: string) => Number.parseInt(v) >= 0 || 'Must be positive'
  ]

  rollDice() {
    // Must cast ref items to avoid ts errors
    const attackerDice = (this.$refs.attackerDice as Vue & { roll: () => Die[] }).roll();
    const defenderDice = (this.$refs.defenderDice as Vue & { roll: () => Die[] }).roll();
    const losses = this.computeRollResults(attackerDice, defenderDice)
    if (this.numAttackers) {
      this.numAttackers -= losses[0]
    }
    if (this.numDefenders) {
      this.numDefenders -= losses[1]
    }
  }

  // Returns array with two elements:
  //    0: number of attacker losses
  //    1: number of defender losses
  computeRollResults(attackerDice: Die[], defenderDice: Die[]) {
    attackerDice.sort((a, b) => a.value > b.value ? -1 : 1 )
    defenderDice.sort((a, b) => a.value > b.value ? -1 : 1 )

    let smallestLength = attackerDice.length
    if (defenderDice.length < smallestLength) {
      smallestLength = defenderDice.length
    }

    const winColor = '#2edb2b'
    const loseColor = '#ff0000'

    const losses = [0, 0]
    for (let i = 0; i < smallestLength; i++) {
      if (attackerDice[i].value > defenderDice[i].value) {
        attackerDice[i].borderColor = winColor
        defenderDice[i].borderColor = loseColor
        losses[1]++
      } else {
        defenderDice[i].borderColor = winColor
        attackerDice[i].borderColor = loseColor
        losses[0]++
      }
    }

    return losses
  }
}
</script>
<style>
 .dice {
   margin-top: -32px;
 }

 .v-text-field__slot input {
   font-size: 30px;
 }
</style>
