<template>
<div>
    <div v-if="!activeForm">
    <v-btn color="primary" @click="addRoutine" @tap="addRoutine">Voeg routine toe</v-btn>
    <v-card v-for="(routine, key) in routines" v-bind:key="key" @click="editRoutine(routine)" @tap="editRoutine(routine)">
        <v-card-title>{{ routine.name }}</v-card-title>
        <v-card-text>
            <RoutineInfo :condition="routine" />
        </v-card-text>
    </v-card>
    </div>
    <Routine v-else v-model="routines" :routine="routine" :active="activeForm" @update="onRoutineFormUpdate"/>
</div>
</template>

<script>
import axios from 'axios';

import Routine from './form/Routine'
import RoutineInfo from './info/RoutineInfo';

export default {
    components: {
        Routine,
        RoutineInfo,
    },
    name: 'Routines',

    data: function() {
        return {
            routines: [],
            routine: null,
            activeForm: false,
        }
    },
    mounted: function() {
        axios.get(process.env.VUE_APP_ROOT_API + '/routine')
        .then(response => {
            response.data.forEach(routine => {
                
                var groups = [];
                routine.groups.forEach(group => {
                    groups.push({
                        element: {
                            group: group.group_id,
                            minutes: group.minutes,
                        }
                    })
                });

                var conditions = [];
                routine.conditions.forEach(condition => {
                    conditions.push({
                        element: {
                            type: condition.type,
                            mm: condition.mm,
                        }
                    })
                });

                var data = {
                    id: routine.id,
                    name: routine.name,
                    days: routine.days,
                    startTime: routine.start_time,
                    conditionType: routine.condition_type,
                    groups: groups,
                    conditions: conditions,
                }

                this.routines.push(data);
            })
        });
    },
    methods: {
        addRoutine() {
            this.routine = {
                id: null,
                name: null,
                days: null,
                startTime: null,
                conditionType: null,
                conditions: [],
                groups: [],
            }

            this.activeForm = true;
        },
        editRoutine(routine) {
            console.log(routine);
            this.routine = routine;
            this.activeForm = true;
        },
        onRoutineFormUpdate(value) {
            this.activeForm = value;
        }
    }
}
</script>

<style>
    .v-card {
        margin-top: 10px;
        cursor: pointer;
    }

</style>


