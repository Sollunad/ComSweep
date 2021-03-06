<template>
    <div class="row">
        <div
            v-for="(reihe, rIndex) in feld"
            v-bind:key="rIndex"
        >
            <TileComp
                    v-for="(tile, tIndex) in reihe"
                    v-bind:tile="tile"
                    v-bind:width="tileWidth"
                    v-bind:key="`${rIndex} ${tIndex}`"
                    v-on:reveal="reveal(rIndex,tIndex)"
                    v-on:massReveal="massReveal(rIndex,tIndex)"
                    v-on:flag="flag(rIndex,tIndex)"
                    class="tile"
            >
            </TileComp>
        </div>
        <v-btn @click="newGame">Neues Spiel</v-btn>
        <v-btn @click="setDefault">Default</v-btn>
        <v-btn @click="help">Hilfe</v-btn>
        <div class="einstellungen">
            <v-slider
                    v-model="rows"
                    :min="2"
                    :max="40"
                    label="Höhe"
                    thumb-label
            ></v-slider>
            <v-slider
                    v-model="tiles"
                    :min="2"
                    :max="40"
                    label="Breite"
                    thumb-label
            ></v-slider>
            <v-slider
                    v-model="mines"
                    :min="1"
                    :max="maxMines"
                    label="Minen"
                    thumb-label
            ></v-slider>
        </div>
        <v-dialog
            v-model="dialog"
            width="300">
            <v-card>
                <v-card-text>
                    {{dialogText}}
                </v-card-text>
                <v-img :src="gifLink"></v-img>
            </v-card>
        </v-dialog>
        <v-dialog
                v-model="helpDialog"
                width="300">
            <HelpComp></HelpComp>
        </v-dialog>
    </div>
</template>

<script>

    import TileComp from "./TileComp";
    import HelpComp from "./HelpComp";
    export default {
        name: "GameComp",
        components: {HelpComp, TileComp},
        props: ['width'],
        data: () => ({
            feld: [],
            running: false,
            firstReveal: false,
            rows: 16,
            tiles: 12,
            mines: 30,
            dialog: false,
            dialogText: '',
            helpDialog: false,
            gifLink: ''
        }),
        computed: {
            maxMines: function() {
                return this.rows * this.tiles - 1;
            },
            isWon: function() {
                let won = true;
                this.feld.forEach(row => {
                    row.forEach(tile => {
                        if (!tile.revealed && !tile.mine) won = false;
                    });
                });
                return won;
            },
            tileWidth: function() {
                if (this.width > 1000) {
                    return 40;
                } else if (this.width > 600) {
                    return 30;
                } else {
                    return 25;
                }

            }
        },
        methods: {
            reveal: function(row, tile) {
                const opened = this.feld[row][tile];
                if (!opened.revealed && !opened.flagged && this.running) {
                    opened.revealed = true;
                    if (!this.firstReveal) {
                        this.firstReveal = true;
                        for (let dRow = -1; dRow <= 1; dRow++) {
                            for (let dTile = -1; dTile <= 1; dTile++) {
                                const nRow = row + dRow;
                                const nTile = tile + dTile;
                                if(nRow >= 0 && nTile >= 0
                                    && nRow < this.feld.length && nTile < this.feld[0].length) {
                                    this.feld[nRow][nTile].blocked = true;
                                }
                            }
                        }
                        this.generateMines();
                        this.countNeighbours();
                    }
                    if (this.isWon) {
                        this.dialogText = 'Du hast die Kapitalisten besiegt!';
                        this.gifLink = 'https://media.giphy.com/media/l378z1NPLzCEAVCVy/giphy.gif';
                        this.dialog = true;
                        this.endGame();
                    }  else if (opened.mine) {
                        this.dialogText = 'Die Kapitalisten haben gewonnen :c';
                        this.gifLink = 'https://media.giphy.com/media/3oEjHQmvkaHgKaXhWE/giphy.gif';
                        this.dialog = true;
                        this.endGame();
                    } else if (opened.neighbours === 0) {
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
                    }
                }
            },
            massReveal: function(row, tile) {
                const opened = this.feld[row][tile];
                if (opened.revealed && this.running) {
                    let flaggedNeighbours = 0;
                    for (let dRow = -1; dRow <= 1; dRow++) {
                        for (let dTile = -1; dTile <= 1; dTile++) {
                            const nRow = row + dRow;
                            const nTile = tile + dTile;
                            if (nRow >= 0 && nTile >= 0
                                && nRow < this.feld.length && nTile < this.feld[0].length
                                && this.feld[nRow][nTile].flagged) {
                                flaggedNeighbours++;
                            }
                        }
                    }
                    if (opened.neighbours === flaggedNeighbours && opened.neighbours > 0) {
                        for (let dRow = -1; dRow <= 1; dRow++) {
                            for (let dTile = -1; dTile <= 1; dTile++) {
                                const nRow = row + dRow;
                                const nTile = tile + dTile;
                                if (nRow >= 0 && nTile >= 0
                                    && nRow < this.feld.length && nTile < this.feld[0].length
                                    && !this.feld[nRow][nTile].revealed) {
                                    this.reveal(nRow, nTile);
                                }
                            }
                        }
                    }
                }
            },
            flag: function(row, tile) {
                let opened = this.feld[row][tile];
                if (!opened.revealed && this.running) {
                    opened.flagged = !opened.flagged;
                }
            },
            startGame: function() {
                this.generateFeld();
                this.firstReveal = false;
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
                    if (!this.feld[randomRow][randomTile].mine && !this.feld[randomRow][randomTile].revealed && !this.feld[randomRow][randomTile].blocked) {
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
            },
            newGame: function() {
                this.startGame();
            },
            setDefault: function() {
                this.rows = 16;
                this.tiles = 12;
                this.mines = 30;
            },
            help: function() {
                this.helpDialog = true;
            }
        },
        created: function() {
            this.startGame();
        }
    }
</script>

<style scoped>
    .einstellungen {
        margin: 0 20% 0 16px;
        -webkit-touch-callout: none; /* iOS Safari */
        -webkit-user-select: none; /* Safari */
        -moz-user-select: none; /* Firefox */
        -ms-user-select: none; /* Internet Explorer/Edge */
        user-select: none; /* Non-prefixed version, currently
                                  supported by Chrome and Opera */
    }

    .row {
        overflow-x: -moz-scrollbars-none;
        overflow-y: hidden;
        white-space: nowrap;

        .tile {
            display: inline-block;
        }
    }

    ::-webkit-scrollbar {
        display: none;
    }
</style>