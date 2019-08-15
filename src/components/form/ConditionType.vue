<template>
<div class="conditionForm">
    <v-select v-model="form.type" :items="conditionTypes" ref="condition" label="Verleden / Voorspelling" :value="value.type" @input="updateValues()"/>
    <div v-if="form.type == 'br'">
        <v-text-field label="Milimeter neerslag gevallen in de afgelopen 24 uur" ref="mm" v-model="form.mm" type="number" :value="value.mm" @input="updateValues()" pattern="\d"/>
    </div>
    <div v-else-if="form.type == 'mt'">
        <v-text-field label="Milimeter neerslag verwacht op de actuele dag" ref="mm" v-model="form.mm" type="number" :value="value.mm" @input="updateValues()" pattern="\d"/>
    </div>
</div>
</template>

<script>
export default {
    name: 'ConditionType',
    props: ['value'],
    data: function() {
        return {
            form: {
                type: null,
                mm: null,
            },
            conditionTypes: [
                {
                    text: 'Neerslag afgelopen 24 uur',
                    value: 'br',
                },
                {
                    text: 'Neerslag voorspeld op actuele dag',
                    value: 'mt',
                }
            ],
        }
    },
    methods: {
        updateValues: function() {
            this.$emit('input', {
                type: this.form.type,
                mm: this.form.mm,
            })
        }
    },
    mounted: function() {
        this.form.type = this.value.type;
        this.form.mm = this.value.mm;
    }
}
</script>

<style>
    div.conditionForm {
        background:#c4ffdf;
        border: 1px solid black;
        margin: 1em 0;
        padding: 1em;
            }
</style>


