<template>
  <v-app dark>
    <GameComp
        v-bind:width="windowWidth"></GameComp>
  </v-app>
</template>

<script>
import GameComp from "./components/GameComp";

export default {
    name: 'App',
    components: {
      GameComp
    },
    data() {
        return {
            windowWidth: 0,
        }
    },
    mounted() {
        this.$nextTick(() => {
            window.addEventListener('resize', () => {
                this.windowWidth = window.innerWidth;
            });
        })
        this.windowWidth = window.innerWidth;
    },
}
</script>
