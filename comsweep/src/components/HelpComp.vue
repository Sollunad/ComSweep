<template>
    <div class="help">
        <h2>Desktop</h2>
        (Q) Doppelklick - Feld aufdecken
        <p></p>
        (W) Rechtsklick - Flag setzen
        <p></p>
        (E) Klick auf Zahl - 3*3 Reveal
        <p></p>
        <h2>Mobile:</h2>
        Doppelklick - Feld aufdecken
        <p></p>
        Lange drücken - Flag setzen
        <p></p>
        Klick auf Zahl - 3*3 Reveal
        <v-img :src="'https://media.giphy.com/media/befaYZCgtZfZm/giphy.gif'"></v-img>
    </div>
</template>

<script>
    export default {
        name: "HelpComp"
    }
</script>

<style scoped>
    .help {
        background-color: #444444;
        padding: 10px;
    }
</style>