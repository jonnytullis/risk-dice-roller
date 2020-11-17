<template>
  <div id="wrapper">
    <v-container>
      <v-row justify="center" class="my-4">
        <v-img src="risk-logo.png" :max-width="this.$vuetify.breakpoint.xsOnly ? '300' : '450'" />
      </v-row>
      <v-row justify="center" class="mb-6">
        <v-card class="pa-4" width="90%"  max-width="600px">
          <v-row>
            <v-col>
              <v-text-field
                ref="attackingUnitsInput"
                v-model="numAttackersStarting"
                autofocus
                outlined
                hide-details
                dense
                type="number"
                min="0"
                maxLength="4"
                label="Attacking Units"
                :rules="numberRules"
              />
            </v-col>
            <v-col>
              <v-text-field
                ref="defendingUnitsInput"
                v-model="numDefendersStarting"
                outlined
                hide-details
                dense
                type="number"
                min="0"
                maxLength="4"
                label="Defending Units"
                :rules="numberRules"
              />
            </v-col>
            <v-col cols="12" xl="3" lg="3" md="3" sm="3" style="text-align: center">
              <v-btn @click="resetNumUnitsLeft" :disabled="disableUnitUpdate">
                Reset
              </v-btn>
            </v-col>
          </v-row>
          <v-row class="mb-6" align="center">
            <v-col>
              <div class="unitsLeft">{{ numAttackersLeft }}</div>
            </v-col>
            <v-col cols="12" xl="8" lg="8" md="8" sm="8">
              <dice-view
                ref="attackerDice"
                :key="diceResetkey"
                class="dice"
                num-dice="3"
                :max-active="numAttackersLeft"
              />
            </v-col>
          </v-row>
          <v-row align="center" :class="this.$vuetify.breakpoint.xsOnly ? 'flex-column-reverse' : ''">
            <v-col>
              <div class="unitsLeft">{{ numDefendersLeft }}</div>
            </v-col>
            <v-col cols="12" xl="8" lg="8" md="8" sm="8">
              <dice-view
                ref="defenderDice"
                :key="diceResetkey"
                class="dice"
                num-dice="2"
                color="black"
                :max-active="numDefendersLeft"
              />
            </v-col>
          </v-row>
          <v-row>
            <v-spacer />
<!--            <v-btn color="secondary" :disabled="!numDefendersLeft || !numAttackersLeft" class="mr-2">-->
<!--              Resolve Battle-->
<!--            </v-btn>-->
            <v-btn @click="rollDice" :disabled="!numDefendersLeft || !numAttackersLeft" color="primary">
              Roll Dice
            </v-btn>
            <v-spacer />
          </v-row>
        </v-card>
      </v-row>
    </v-container>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "nuxt-property-decorator"
import Dice from "~/components/dice.vue"
import { Die } from "~/types/types"

@Component({
  components: { DiceView: Dice }
})
export default class Main extends Vue {
  numAttackersStarting: number | string = 3
  numDefendersStarting: number | string = 2

  diceResetkey: number = 0

  numAttackersLeft: number = 3
  numDefendersLeft: number = 2

  numberRules = [
    (v: string) => !isNaN(Number.parseInt(v)) || 'Invalid number',
    (v: string) => Number.parseInt(v) >= 0 || 'Must be positive'
  ]

  get disableUnitUpdate() {
    Vue.nextTick(() => {
      console.log('attackingUnitsInput:', this.$refs.attackingUnitsInput)
      console.log('defendingUnitsInput:', this.$refs.defendingUnitsInput)
    })
    return !!this.$refs.attackingUnitsInput
  }

  resetNumUnitsLeft() {
    const attackers = Number.parseInt(this.numAttackersStarting.toString())
    const defenders = Number.parseInt(this.numDefendersStarting.toString())
    if (!isNaN(attackers)) {
      this.numAttackersLeft = attackers
    }
    if (!isNaN(defenders)) {
      this.numDefendersLeft = defenders
    }
    this.diceResetkey++
  }

  rollDice() {
    // Must cast ref items to avoid ts errors
    const attackerDice = (this.$refs.attackerDice as Vue & { roll: () => Die[] }).roll();
    const defenderDice = (this.$refs.defenderDice as Vue & { roll: () => Die[] }).roll();
    const losses = this.computeRollResults(attackerDice, defenderDice)
    if (this.numAttackersLeft) {
      this.numAttackersLeft -= losses[0]
    }
    if (this.numDefendersLeft) {
      this.numDefendersLeft -= losses[1]
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
 .v-text-field__slot input {
   font-size: 20px;
 }

 .unitsLeft {
   font-size: 30px;
   text-align: center;
 }

 #wrapper {
   background-image: url(../static/map.jpg);
   background-size: cover;
   height: 100vh;
 }
</style>
