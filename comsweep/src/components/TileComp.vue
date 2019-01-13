<template>
    <v-avatar :color="backgroundColor" :size="width" class="tile unselectable" tile
        @click="click"
        @contextmenu.prevent="flag">
        <template v-if="tile.revealed">
            <v-icon v-if="tile.mine" color="yellow">attach_money</v-icon>
            <span v-else-if="tile.neighbours > 0" class="number"
                  v-bind:style="{ color: textColor }">{{tile.neighbours}}</span>
        </template>
        <template v-else-if="tile.flagged">
            <v-icon color="red">flag</v-icon>
        </template>
    </v-avatar>
</template>

<script>
    export default {
        name: "TileComp",
        props: ['tile', 'width'],
        computed: {
            textColor: function() {
                switch(this.tile.neighbours) {
                    case 1: return 'blue';
                    case 2: return 'green';
                    case 3: return 'red';
                    case 4: return 'darkblue';
                    case 5: return 'brown';
                    case 6: return 'cyan';
                    case 7: return 'black';
                    case 8: return 'dimgray';
                    default: return '';
                }
            },
            backgroundColor: function() {
                if (this.tile.revealed && !this.tile.mine) return 'grey';
                else return 'blue-grey';
            }
        },
        methods: {
            click: function() {
                this.$emit('click');
            },
            flag: function() {
                this.$emit('flag');
            }
        }
    }
</script>

<style scoped>
    .tile {
        margin: 1px;
    }

    .unselectable {
        -webkit-touch-callout: none; /* iOS Safari */
        -webkit-user-select: none; /* Safari */
        -moz-user-select: none; /* Firefox */
        -ms-user-select: none; /* Internet Explorer/Edge */
        user-select: none; /* Non-prefixed version, currently
                                  supported by Chrome and Opera */
    }
</style>