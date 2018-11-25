<template>
    <div class="logger-window">
        <v-layout row v-for="(action, index) in log" :key="index">
            <v-flex xs12 sm8>
                {{ action}}
            </v-flex>
        </v-layout>
    </div>
</template>

<script>
    export default {
        props: {
            log: {type: Array, required: true}
        }
    }
</script>

<style scoped>
    .logger-window {
        max-height: 550px;
        width: 100%;
        overflow-y: auto;
        padding-left: 30px;
        padding-top: 10px;
    }
</style>