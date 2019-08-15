<template>
<div v-if="isValid()">
    <p>
        <strong>{{ condition.name }}</strong> 
        wordt uitgevoerd op <strong>{{ getDays(condition.days) }}</strong> 
        om <strong>{{ condition.startTime }}</strong>u 
        <span v-if="condition.conditions">, indien voldaan wordt aan de volgende condities: {{ getConditionString() }}</span>
        en is toepasbaar op de volgende groepen: <span v-for="(group, key) in condition.groups" v-bind:key="key">{{ group.id }}</span>
        </p>
</div>
</template>

<script>
export default {
    name: 'RouteInfo',
    props: ['condition'],
    data: function() {
        return {

        }
    },
    methods: {
        getDays(days) {

            var dutchDays = [
                'zondag',
                'maandag',
                'dinsdag',
                'woensdag',
                'donderdag',
                'vrijdag',
                'zaterdag',
            ];

            var dayStrings = [];
            days.forEach(day => {
                dayStrings.push(dutchDays[day]);
            });

            return dayStrings.join(', ');
        },
        getConditionString() {

            var conditions = [];

            if (!this.condition.conditions || 0 === this.condition.conditions.length || typeof this.condition.conditions == 'undefined') {
                return '';
            }

            this.condition.conditions.forEach(element => {
                var condition = element.element;
                switch(condition.type) {
                        case 'br':
                            conditions.push('In de afgelopen 24 uur minder dan ' + condition.mm + 'mm neerslag gevallen');
                            break;
                        case 'mt':
                            conditions.push('Op de actuele dag minder dan ' + condition.mm + 'mm neerslag verwacht');
                            break;    
                    }
            });

            var conditionType = this.condition.conditionType === 'and' ? 'EN' : 'OF';

            return conditions.join(' ' + conditionType + ' ');
        },
        isValid() {

            var required = [
                this.condition.name,
                this.condition.days,
                this.condition.startTime,
                this.condition.groups
            ]

            var valid = true;
            required.forEach(item => {
                if (isEmpty(item)) {
                    valid = false;
                }
            });


            return valid;

            function isEmpty(value) {
                return (!value || 0 === value.length);
            }
        }
    }
}
</script>

