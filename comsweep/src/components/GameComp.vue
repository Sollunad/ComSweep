<template>
    <div>
        <div
            v-for="(reihe, rIndex) in feld"
            v-bind:key="rIndex"
        >
            <TileComp
                    v-for="(tile, tIndex) in reihe"
                    v-bind:tile="tile"
                    v-bind:key="`${rIndex} ${tIndex}`"
                    v-on:click="reveal(rIndex,tIndex)"
            >
            </TileComp>
        </div>
    </div>
</template>

<script>

    import TileComp from "./TileComp";
    export default {
        name: "GameComp",
        components: {TileComp},
        data: () => ({
            feld: []
        }),
        methods: {
            reveal: function(row, tile) {
                console.log(row + " " + tile);
            },
            generateFeld: function(rows, tiles, mines) {
                let feld = [];
                for (let row = 0; row < rows; row++) {
                    feld.push([]);
                    for (let tile = 0; tile < tiles; tile++) {
                        const tileObj = { revealed: false, mine: false };
                        feld[row].push(tileObj);
                    }
                }
                while (mines > 0) {
                    const randomRow = Math.floor(Math.random() * rows);
                    const randomTile = Math.floor(Math.random() * tiles);
                    if (!feld[randomRow][randomTile].mine) {
                        feld[randomRow][randomTile].mine = true;
                        mines--;
                    }
                }
                console.log(feld);
                return feld;
            },
        },
        created: function() {
            this.feld = this.generateFeld(10,10,20);
        }
    }
</script>

<style scoped>

</style>