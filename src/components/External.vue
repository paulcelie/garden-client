<template>
    <div>
        <v-card>
            <v-card-title>Meteo configuratie</v-card-title>
            <v-card-text><Meteo /></v-card-text>
        </v-card>
        <v-card>
            <v-card-title>Buienradar configuratie</v-card-title>
            <v-card-text><Buienradar /></v-card-text>
        </v-card>
    </div>
</template>

<script>
import Meteo from './form/Meteo';
import Buienradar from './form/Buienradar';

export default {
    name: 'External',
    components: {
        Meteo,
        Buienradar,
    },
    data: function() {
        return {

        }
    }
}
</script>

