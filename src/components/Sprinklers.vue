<template>
<div>
  <v-alert type="success" v-if="sprinklerAdded">Sprinkler opgeslagen</v-alert>
  <v-form rel="form" v-if="activeForm">
    <v-text-field v-model="form.name" label="Naam sproeier" required />
    <v-select
        v-model="form.group"
        :items="groups"
        label="Groep"
        required
      ></v-select>
      <v-text-field v-model="form.x" label="X coördinaat" required />
      <v-text-field v-model="form.y" label="Y coördinaat" required />
  <v-btn
        color="success"
        class="mr-4"
        @click="saveSprinkler"
      >Opslaan</v-btn>
      <v-btn color="none" @click="clearForm" @tap="clearForm">x</v-btn>
      </v-form>
      <div v-else>
       <v-btn
        color=""
        class="mr-4"
        @click="openForm"
      >Voeg sprinkler toe</v-btn> 
  <v-data-table
    :headers="headers"
    :items="sprinklers"
    item-key="name"
    group-by="Groep"
      ></v-data-table>
      </div>
</div>
</template>

<script>
  import axios from 'axios';

  export default {
      name: 'Sprinklers',
    data () {
      return {
        activeForm: false,
        sprinklerAdded: false,
        groups: [],
        form: {},
        headers: [
          {
            text: 'Sprinkler',
            align: 'left',
            value: 'name',
          },
         { text: 'Positie x', value: 'x'},
          { text: 'Positie y', value: 'y' },
        ],
        sprinklers: []
      }
    },
    mounted: function() {
        axios.get(process.env.VUE_APP_ROOT_API + "/group")
        .then(response => {
            response.data.forEach(group => {
                this.groups.push({
                  value: group.id,
                  text: group.name,
                });

                group.sprinklers.forEach(sprinkler => {
                  this.sprinklers.push({
                    name: sprinkler.name,
                    Groep: group.name,
                    x: sprinkler.x,
                    y: sprinkler.y
                  });
                })
            });
        });            
    },
    methods: {
      saveSprinkler() {
        axios.post(process.env.VUE_APP_ROOT_API + '/sprinkler', {
          group_id: this.form.group,
          name: this.form.name,
          x: this.form.x,
          y: this.form.y
        }).then(response => {
          this.sprinklerAdded = true;
          this.clearForm();

          this.sprinklers.push({
            name: response.data.name,
            Groep: response.data.group_name,
            x: response.data.x,
            y: response.data.y,
          });
        });
      },
      openForm() {
        this.activeForm = true;
        this.sprinklerAdded = false;
      },
      clearForm() {
        this.form = {},
        this.activeForm = false;
      }
    }
  }
</script>

<style>
    .v-row-group__header button {
        display: none !important;
    }
</style>
