<template>
    <v-avatar color="grey" @click="click" class="tile" tile>
        <v-icon v-if="tile.mine">attach_money</v-icon>
    </v-avatar>
</template>

<script>
    export default {
        name: "TileComp",
        props: ['tile'],
        methods: {
            click: function() {
                this.$emit('click');
            }
        }
    }
</script>

<style scoped>
    .tile {
        margin: 5px;
    }
</style>