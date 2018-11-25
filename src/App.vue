<template>
    <v-app>
        <v-container>
            <v-content>
                <multiplier/>
            </v-content>
        </v-container>
    </v-app>
</template>
<script>
    import Multiplier from "./views/Multiplier"

    export default {
        components: {
            Multiplier
        }
    }
</script>