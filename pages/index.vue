<template>
  <v-container>
    <v-row justify="center" class="mb-4">
      <v-card width="100%" max-width="700px">
        <selector-units />
      </v-card>
    </v-row>
    <v-row justify="center" class="mb-4">
      <v-card class="pb-4" width="100%"  max-width="700px">
        <dice-view
          ref="attackerDice"
          num-dice="3"
        />
        <dice-view
          ref="defenderDice"
          num-dice="2"
          color="black"
        />
        <v-row justify="center">
          <v-btn @click="rollDice" color="primary">
            Roll
          </v-btn>
        </v-row>
      </v-card>
    </v-row>
  </v-container>
</template>

<script lang="ts">
import { Component, Vue } from "nuxt-property-decorator"
import SelectorUnits from "~/components/selector-units.vue"
import DiceView from "~/components/dice-view.vue"

@Component({
  components: { DiceView, SelectorUnits }
})
export default class Index extends Vue {
  rollDice() {
    // @ts-ignore
    this.$refs.attackerDice.roll()
    // @ts-ignore
    this.$refs.defenderDice.roll()
  }
}
</script>
