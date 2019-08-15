<template>
<div class="routineGroup">
    <v-select v-model="form.group" :items="groups" ref="group" label="Sprinkler groep" :value="value.group" @input="updateValues()"/>
    <v-text-field type="number" pattern="\d" v-model="form.minutes" label="Aantal minuten aan" :value="value.minutes" @input="updateValues()" />
</div>
</template>

<script>
import axios from 'axios';

export default {
    name: 'RoutineGroup',
    props: ['value'],
    data: function() {
        return {
            form: {
                group: null,
                minutes: null,
            },
            groups: [
                {
                    text: 'Groep 1',
                    value: 15,
                },
                {
                    text: 'Groep 2',
                    value: 16,
                }
            ],
        }
    },
    methods: {
        updateValues: function() {
            this.$emit('input', {
                group: this.form.group,
                minutes: this.form.minutes,
            })
        }
    },
    mounted: function() {

        this.form.group = this.value.group;
        this.form.minutes = this.value.minutes;

        axios.get(process.env.VUE_APP_ROOT_API + "/group")
          .then(response => {
              response.data.forEach(group => {
                  this.groups.push({
                      text: group.name,
                      value: group.id,
                  })
              });
          })
    }
}
</script>

<style>
    div.routineGroup {
        background:#a2c4fc;
        border: 1px solid black;
        margin: 1em 0;
        padding: 1em;
            }
</style>


