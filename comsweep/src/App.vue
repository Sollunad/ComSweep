<template>
  <v-app dark>
    <GameComp></GameComp>
  </v-app>
</template>

<script>
import GameComp from "./components/GameComp";

export default {
  name: 'App',
  components: {
    GameComp
  },
  data () {
    return {
      //
    }
  }
}
</script>
