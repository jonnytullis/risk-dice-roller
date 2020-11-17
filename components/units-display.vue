<template>
  <v-row justify="center" align="center">
    <v-chip
      :color="attacker ? 'error' : 'black'"
      class="display-1 px-4"
      large
      dark
    >
      <v-icon v-if="attacker" large class="mr-4">mdi-sword</v-icon>
      <v-icon v-else-if="defender" large class="mr-4">mdi-shield</v-icon>
      {{ numUnits }}
    </v-chip>
  </v-row>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "nuxt-property-decorator"

@Component
export default class UnitsDisplay extends Vue {
  @Prop() defender!: boolean
  @Prop() attacker!: boolean
  @Prop({ required: true }) numUnits!: number
}
</script>
