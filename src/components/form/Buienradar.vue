<template>
    <v-form v-if="!loading">
        <div v-if="showForm">
        <v-alert type="success" v-if="successMessage">{{ successMessage }}</v-alert>
        <v-select v-model="form.location" :items="locations" label="Selecteer weerstation" />
        <v-btn color="primary" @click="save" @tap="save">Opslaan</v-btn>
        </div>
        <v-btn v-else color="none" @click="showForm = true" @tap="showForm = true">Wijzig buienradar</v-btn>
    </v-form>

    <v-progress-circular v-else
      indeterminate
      color="primary"
    ></v-progress-circular>
</template>

<script>

import axios from 'axios';

export default {
    name: 'Buienradar',
    data: function() {
        return {
            successMessage: null,
            showForm: false,
            loading: true,
            form: {
                id: null,
                location: null,
            },
            locations: [],

        }
    },
    mounted: function() {
        axios.get('https://data.buienradar.nl/2.0/feed/json')
        .then(response => {
            response.data.actual.stationmeasurements.forEach(station => {
                this.locations.push({
                    text: station.stationname,
                    value: station.stationid,
                })
            });
        });

        axios.get(process.env.VUE_APP_ROOT_API + "/buienradar")
        .then(response => {
            if (response.data.length > 0) {
                this.form.id = response.data[0].id;
                this.form.location = response.data[0].location;
            } else {
                this.showForm = true;
            }

            this.loading = false;
        })
    },
    methods: {
        save() {
            var data = {
                location: this.form.location,
            }

            if (this.form.id) {
                axios.put(process.env.VUE_APP_ROOT_API + "/buienradar/" + this.form.id, data)
                .then(response => {
                    this.successMessage = 'Buienradar configuratie is bijgewerkt';
                    this.form.id = response.data.id;
                    this.form.location = response.data.location;
                    this.showForm = false;
                });
            } else {
                axios.post(process.env.VUE_APP_ROOT_API + "/buienradar", data)
                .then(response => {
                    this.successMessage = 'Buienradar configuratie is opgeslagens';
                    this.form.id = response.data.id;
                    this.form.location = response.data.location;
                    this.showForm = false;
                });
            }
        }
    }
}
</script>

