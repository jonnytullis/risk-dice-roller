<template>
  <div>
    <v-row class="mb-2">
      <v-col>
        <v-text-field
          ref="attackingUnitsInput"
          v-model="numAttackersStarting"
          autofocus
          outlined
          dense
          type="number"
          :min="minUnits"
          :max="maxUnits"
          label="Attacking Units"
          :rules="numberRules"
          class="mb-n5"
        />
      </v-col>
      <v-col>
        <v-text-field
          ref="defendingUnitsInput"
          v-model="numDefendersStarting"
          outlined
          dense
          type="number"
          :min="minUnits"
          :max="maxUnits"
          label="Defending Units"
          :rules="numberRules"
          class="mb-n5"
        />
      </v-col>
      <v-col cols="12" xl="3" lg="3" md="3" sm="3" style="text-align: center">
        <v-btn @click="setNumUnitsLeft" :disabled="disableSetUnits">
          Set Units
        </v-btn>
      </v-col>
    </v-row>
    <v-expand-transition>
      <div v-if="showDiceRoller">
        <v-row align="center">
          <v-col>
            <units-display
              :attacker="true"
              :num-attacking-units="numAttackersLeft"
              :num-defending-units="numDefendersLeft"
            />
          </v-col>
          <v-col cols="12" xl="7" lg="7" md="7" sm="7">
            <dice
              ref="attackerDice"
              :key="diceResetKey"
              class="dice"
              num-dice="3"
              :max-active="numAttackersLeft"
            />
          </v-col>
        </v-row>
        <v-row align="center" :class="this.$vuetify.breakpoint.xsOnly ? 'flex-column-reverse' : ''">
          <v-col>
            <units-display
              :defender="true"
              :num-attacking-units="numAttackersLeft"
              :num-defending-units="numDefendersLeft"
            />
          </v-col>
          <v-col cols="12" xl="7" lg="7" md="7" sm="7">
            <dice
              ref="defenderDice"
              :key="diceResetKey"
              class="dice"
              num-dice="2"
              color="black"
              :max-active="numDefendersLeft"
            />
          </v-col>
        </v-row>
        <v-row class="mt-4">
          <v-spacer />
          <!--            <v-btn color="secondary" :disabled="!numDefendersLeft || !numAttackersLeft" class="mr-2">-->
          <!--              Resolve Battle-->
          <!--            </v-btn>-->
          <v-btn @click="rollDice" :disabled="numAttackersLeft <= 0 || numDefendersLeft <= 0" color="primary">
            Roll Dice
          </v-btn>
          <v-spacer />
        </v-row>
      </div>
    </v-expand-transition>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'nuxt-property-decorator'
import Dice from '~/components/dice.vue'
import { Die } from 'types/types'
import UnitsDisplay from '~/components/units-display.vue'

@Component({
  components: { UnitsDisplay, Dice }
})
export default class DiceRoller extends Vue {
  minUnits = 1
  maxUnits = 200

  numAttackersStarting: number | string = 3
  numDefendersStarting: number | string = 2

  showDiceRoller: boolean = false
  diceResetKey: number = 0

  numAttackersLeft: number = 3
  numDefendersLeft: number = 2

  numberRules = [
    (v: string) => !isNaN(Number.parseInt(v)) || 'Required',
    (v: string) => Number.parseInt(v) >= this.minUnits || `Must be ${this.minUnits} or more`,
    (v: string) => Number.parseInt(v) <= this.maxUnits || `Must be ${this.maxUnits} or less`
  ]

  get disableSetUnits() {
    // @ts-ignore
    if (this.$refs.attackingUnitsInput && this.$refs.attackingUnitsInput.hasError) {
      return true
    }
    // @ts-ignore
    if (this.$refs.defendingUnitsInput && this.$refs.defendingUnitsInput.hasError) {
      return true
    }
    return Number.parseInt(this.numDefendersStarting.toString()) < this.minUnits ||
      Number.parseInt(this.numDefendersStarting.toString()) > this.maxUnits ||
      Number.parseInt(this.numAttackersStarting.toString()) < this.minUnits ||
      Number.parseInt(this.numAttackersStarting.toString()) > this.maxUnits
  }

  setNumUnitsLeft() {
    this.showDiceRoller = true
    const attackers = Number.parseInt(this.numAttackersStarting.toString())
    const defenders = Number.parseInt(this.numDefendersStarting.toString())
    if (!isNaN(attackers)) {
      this.numAttackersLeft = attackers
    }
    if (!isNaN(defenders)) {
      this.numDefendersLeft = defenders
    }
    this.diceResetKey++
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
        defenderDice[i].borderColor = loseColor
        attackerDice[i].borderColor = winColor
        losses[1]++
      } else {
        attackerDice[i].borderColor = loseColor
        defenderDice[i].borderColor = winColor
        losses[0]++
      }
    }

    return losses
  }
}
</script>
