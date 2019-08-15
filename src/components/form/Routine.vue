<template>
    <v-form v-if="active">
        <h2>Algemene instellingen</h2>
        <v-text-field v-model="form.name" label="Naam routine" />
        <v-select v-model="form.days" label="Dagen" :items="days" attach multiple chils/>
        <v-text-field v-model="form.startTime" type="time" label="Starttijd" />
        <h2>Acties</h2>
        <v-btn color="none" @click="addGroup" @tap="addGroup">Voeg groep toe</v-btn>
        <RoutineGroup v-for="(group, key) in form.groups" v-bind:key="key" v-model="group.element"/>
        <h2>Condities</h2>
        
        <v-select v-model="form.conditionType" :items="andOr" label="En / Of" />
        <div v-if="form.startTime && form.conditionType">
            <p v-if="form.conditionType === 'and'">Deze routine wordt uitgevoerd op {{ form.time }}, wanneer aan álle onderstaande condities wordt voldaan</p>
            <p v-else>Deze routine wordt uitgevoerd op {{ form.time }}, wanneer aan één van onderstaande condities wordt voldaan</p>
        </div>

        <v-btn color="none" @click="addCondition" @tap="addCondition">Voeg conditie toe</v-btn>
        <ConditionType v-for="(condition, key) in form.conditions" v-bind:key="key" v-model="condition.element"/>

        <RoutineInfo :condition="this.form" />
        <br><br>
        <v-btn color="primary" @click="save" @tap="save">Opslaan</v-btn><br><br>
        <v-btn color="error" @click="remove" @tap="remove" v-if="form.id">Verwijderen</v-btn>
    </v-form>
</template>

<script>
import axios from 'axios';
import ConditionType from './ConditionType';
import RoutineInfo from '../info/RoutineInfo';
import RoutineGroup from './RoutineGroup';

export default {
    name: 'Routine',
    components: {
        ConditionType,
        RoutineInfo,
        RoutineGroup,
    },
    props: ['value', 'routine', 'active'],
    data: function() {
        return {
            days: [
                { value: 1, text: 'Maandag'},
                { value: 2, text: 'Dinsdag'},
                { value: 3, text: 'Woensdag'},
                { value: 4, text: 'Donderdag'},
                { value: 5, text: 'Vrijdag'},
                { value: 6, text: 'Zaterdag'},
                { value: 0, text: 'Zondag'},
            ],
            andOr: [
                {
                    text: 'En',
                    value: 'and',
                },
                {
                    text: 'Of',
                    value: 'or',
                }
            ],
            form: {
                id: this.routine.id,
                name: this.routine.name,
                days: this.routine.days,
                startTime: this.routine.startTime,
                conditionType: this.routine.conditionType,
                conditions: this.routine.conditions ? this.routine.conditions : [],
                groups: this.routine.groups ? this.routine.groups : [] ,
            },
        }
    },
    methods: {
        save() {

            if (isEmpty(this.form.name)) {
                alert('Vul een naam in');
                return;
            }

            if (isEmpty(this.form.days)) {
                alert('Selecter minimaal 1 dag');
                return;
            }

            if (isEmpty(this.form.startTime)) {
                alert('Vul een starttijd in');
                return;
            }

            if (isEmpty(this.form.groups)) {
                alert('Voeg minimaal 1 groep toe');
                return;
            }

            var conditions = [];
            this.form.conditions.forEach(condition => {
                conditions.push({
                    type:  condition.element.type,
                    mm: condition.element.mm,
                });
            });

            var groups = [];
            var order = 0;
            this.form.groups.forEach(condition => {
                groups.push({
                    group_id:  condition.element.group,
                    minutes: condition.element.minutes,
                    order: order++,
                });
            });

            var data = {
                name: this.form.name,
                days: this.form.days,
                start_time: this.form.startTime,
                condition_type: this.form.conditionType,
                conditions: conditions,
                groups: groups,
            }

            if (this.form.id) {
                axios.put(process.env.VUE_APP_ROOT_API + '/routine/' + this.form.id, data)
                .then(response => {
                    this.form.id = response.data.id
                    this.active = false;
                    updateParent(this.form);
                });
            } else {
                axios.post(process.env.VUE_APP_ROOT_API + '/routine', data)
                .then(response => {
                    this.form.id = response.data.id
                    this.active = false;
                    updateParent(this.form);
                });
            }

            var parent = this;

            function updateParent(form) {
                parent.$emit('input', parent.value.concat(form));
                parent.$emit('update', false);
            }
            

            function isEmpty(value) {
                return (!value || 0 === value.length);
            }
        },
        remove() {
            var valid = confirm('Weet je zeker dat je ' + this.form.name + ' wilt verwijderen?');
            if (valid) {
            axios.delete(process.env.VUE_APP_ROOT_API + '/routine/' + this.form.id)
                .then(response => {
                    this.active = false;
                    this.$emit('update', false);
                });
            }
        },
        addCondition() {
            this.form.conditions.push({
                element: {
                    type: null,
                    mm: null,
                }
            });
        },
        addGroup() {
            this.form.groups.push({
                element: {
                    group: null,
                    minutes: null,
                }
            })
        }
    }
}
</script>

<style>
    h2 {
        margin-top: 2em;
    }
</style>


