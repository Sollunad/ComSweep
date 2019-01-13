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
                    v-on:flag="flag(rIndex,tIndex)"
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
            feld: [],
            running: false,
            firstReveal: false,
            rows: 16,
            tiles: 16,
            mines: 40
        }),
        methods: {
            reveal: function(row, tile) {
                const opened = this.feld[row][tile];
                if (!opened.revealed && !opened.flagged && this.running) {
                    opened.revealed = true;
                    if (!this.firstReveal) {
                        this.firstReveal = true;
                        this.generateMines();
                        this.countNeighbours();
                    }
                    if (opened.neighbours === 0) {
                        for (let dRow = -1; dRow <= 1; dRow++) {
                            for (let dTile = -1; dTile <= 1; dTile++) {
                                const nRow = row + dRow;
                                const nTile = tile + dTile;
                                if(nRow >= 0 && nTile >= 0
                                    && nRow < this.feld.length && nTile < this.feld[0].length) {
                                    this.reveal(nRow, nTile);
                                }
                            }
                        }
                    } else if (opened.mine) {
                        this.endGame();
                    }
                }
            },
            flag: function(row, tile) {
                let opened = this.feld[row][tile];
                if (!opened.revealed) {
                    opened.flagged = !opened.flagged;
                }
            },
            startGame: function() {
                this.generateFeld();
                this.running = true;
            },
            endGame: function() {
                this.running = false;
                this.feld.forEach((row) => {
                    row.forEach((tile) => {
                        if (!tile.flagged || !tile.mine) {
                            tile.revealed = true;
                        }
                    });
                });
            },
            generateFeld: function() {
                this.feld = [];
                for (let row = 0; row < this.rows; row++) {
                    this.feld.push([]);
                    for (let tile = 0; tile < this.tiles; tile++) {
                        const tileObj = { revealed: false, mine: false, flagged: false, neighbours: 0 };
                        this.feld[row].push(tileObj);
                    }
                }
            },
            generateMines: function() {
                let mines = this.mines;
                while (mines > 0) {
                    const randomRow = Math.floor(Math.random() * this.rows);
                    const randomTile = Math.floor(Math.random() * this.tiles);
                    if (!this.feld[randomRow][randomTile].mine && !this.feld[randomRow][randomTile].revealed) {
                        this.feld[randomRow][randomTile].mine = true;
                        mines--;
                    }
                }
            },
            countNeighbours: function() {
                this.feld.forEach((row, rowIndex) => {
                    row.forEach((tile, tileIndex) => {
                        let mines = 0;
                        for (let dRow = -1; dRow <= 1; dRow++) {
                            for (let dTile = -1; dTile <= 1; dTile++) {
                                const nRow = rowIndex + dRow;
                                const nTile = tileIndex + dTile;
                                if(nRow >= 0 && nTile >= 0
                                    && nRow < this.feld.length && nTile < this.feld[0].length
                                    && this.feld[nRow][nTile].mine) {
                                    mines++;
                                }
                            }
                        }
                        this.feld[rowIndex][tileIndex].neighbours = mines;
                    });
                });
            }
        },
        created: function() {
            this.startGame();
        }
    }
</script>

<style scoped>

</style>