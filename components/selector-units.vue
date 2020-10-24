<template>
  <v-container>
    <v-row no-gutters justify="center">
      <v-text-field
        v-model="numAttackers"
        autofocus
        dense
        outlined
        type="number"
        min="0"
        maxLength="4"
        label="Attacking Units"
      />
    </v-row>
    <v-row>
      <v-col>
        <v-text-field
          v-model="numDefenders"
          dense
          outlined
          type="number"
          min="0"
          maxLength="4"
          label="Defending Units"
        />
      </v-col>
    </v-row>
  </v-container>
</template>

<script lang="ts">
import { Component, Vue } from "nuxt-property-decorator"
import Dice from "~/components/dice-view.vue";
@Component({
  components: {Dice}
})
export default class SelectorUnits extends Vue {
  attackingDice = [
    { value: 1, active: true },
    { value: 2, active: true },
    { value: 3, active: true }
  ]
  defendingDice = [
    { value: 1, active: true },
    { value: 2, active: true }
  ]
  numAttackers: number = 3
  numDefenders: number = 2
}
</script>
