<template>
    <v-form v-if="!loading">
        <v-alert type="error" v-if="errorMessage">{{ errorMessage }}</v-alert>
        <v-alert type="success" v-if="successMessage">{{ successMessage }}</v-alert>
        <div v-if="showForm">
        <v-text-field v-model="apiKey" label="Api key" required />
        <p>Vraag een developer API key aan op <a href="https://meteoserver.nl/weer-API-voor-developers.php" target="_blank">MeteoServer</a></p>
        <v-text-field v-model="longitude" label="Lengtegraad" required />
        <v-text-field v-model="latitude" label="Breedtegraad" required />
        <v-btn color="primary" @click="save" @tap="save">Opslaan</v-btn>
        </div>
        <v-btn v-else color="none" @click="showForm = true" @tap="showForm = true">Wijzig Meteo</v-btn>
    </v-form>
    <v-progress-circular v-else
      indeterminate
      color="primary"
    ></v-progress-circular>
</template>

<script>
import axios from 'axios';

export default {
    name: 'Meteo',
    data: function() {
        return {
            loading: true,
            showForm: false,
            id: null,
            apiKey: null,
            longitude: null,
            latitude: null,
            errorMessage: null,
            successMessage: null,
        }
    },
    methods: {
        save: function() {
            
            var data = {
                api_key: this.apiKey,
                longitude: this.longitude,
                latitude: this.latitude,
            }

            var parent  = this;


            if (this.id) {
                axios.put(process.env.VUE_APP_ROOT_API + "/meteo/" + this.id, data)
                .then(response => {
                    parent.id = response.data.id;
                    parent.successMessage = 'Meteo gegevens succesvol bijgewerkt';
                    this.showForm = false;
                });
            } else {
                axios.post(process.env.VUE_APP_ROOT_API + "/meteo", data)
                .then(respresponsense => {
                    parent.id = response.data.id;
                    parent.successMessage = 'Meteo gegevens succesvol opgeslagens';
                    this.showForm = false;
                });
            }
        }
    },
    mounted: function() {

        axios.get(process.env.VUE_APP_ROOT_API + "/meteo")
        .then(response => {
            
            if (response.data.length > 0) {
                var meteo = response.data[0];

                this.id = meteo.id,
                this.apiKey = meteo.api_key;
                this.longitude = meteo.longitude;
                this.latitude = meteo.latitude;
            } else {
                this.showForm = true;
            }

            this.loading = false;
        });

        navigator.geolocation.getCurrentPosition( success, fail );

            var parent = this;

            function success(position)
            {
                parent.longitude = position.coords.longitude;
                parent.latitude = position.coords.latitude;
            }

            function fail() {
                this.errorMessage = 'Deze browser ondersteunt het ophalen van locaties niet';
            }
    }
}
</script>
