<template>
    <v-card>
        <v-alert v-if="successMessage"
      type="info"
    >
      {{ successMessage }}
    </v-alert>
        <v-card-title>{{ group.name }}</v-card-title>
    <v-card-text>
        <p>'{{ group.name }}' staat <span v-if="group.active">aan</span><span v-else>uit</span><br>Onderstaande apparaten vallen binnen deze groep</p>
        <ul>
        <li v-for="(item, key) in group.sprinklers" v-bind:key="key">{{ item.name }}</li>
        </ul>    
        </v-card-text>
    
    <v-card-actions>
        <v-switch v-model="active" color="primary" class="mx-4" @change="changeStatus"></v-switch>
    
    </v-card-actions>
  </v-card>    

</template>

<script>
import axios from 'axios';

export default {
    name: 'GroupModal',
    props: {
        id: Number
    },
    data: function() {
        return {
            active: true,
            successMessage: null,
            groups: [],
            group: {
                id: null,
                name: null,
                active: false,
                sprinklers: [],
            }
        }
    },
    watch: {
        $props: {
            async handler(value) {
                this.successMessage = null;
                this.group = await getGroupData(value);
                this.active = this.group.active;

                async function getGroupData(value) {
                    const response = await axios.get(process.env.VUE_APP_ROOT_API + "/group/" + value.id)

                    return {
                        id: response.data.external_id,
                        name: response.data.name,
                        active: response.data.active,
                        updatedAt: '2019-01-08 12:27:01',
                        sprinklers: response.data.sprinklers,                        
                    }
                }
            },
            deep: true,
            immediate: true,
        }
    },
    methods: {
        changeStatus() {
            if (this.active) {
                axios.put(process.env.VUE_APP_ROOT_API + "/hue/devices/" + this.group.id + "/on");
                this.successMessage = this.group.name + ' wordt aangezet.';
                return;
            }
            axios.put(process.env.VUE_APP_ROOT_API + "/hue/devices/" + this.group.id + "/off");
            this.successMessage = this.group.name + ' wordt uitgezet.';
        }
    }
}
</script>

