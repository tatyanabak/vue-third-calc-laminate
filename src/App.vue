<template>
  <v-app>
    <v-main style="width: 100%; display: flex; flex-direction row; justify-content: center;">
      <v-card width="600" class="px-3">
        <v-card-title class="mb-5">
          <h1>Калькулятор ламината</h1>
        </v-card-title>

        <v-card_text>
          <CalcLaminate />
        </v-card_text>
      </v-card>
    </v-main>
  </v-app>
</template>

<script>
import CalcLaminate from './components/CalcLaminate.vue';

export default {
  name: 'App',

  components: {
    CalcLaminate
  },

  data: () => ({
    //
  }),
}
</script>
