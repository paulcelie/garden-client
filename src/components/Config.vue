<template>
<div>
    <v-card>
        <v-card-title>Hue bridge</v-card-title>
        <v-card-text>
            <p class="text-center" v-if="pushButton">
                <img :src="getImgUrl('bridge.png')" :height="100" /><br>
                {{ helpText }}<br>
                <v-progress-circular
      indeterminate
      color="primary"
    ></v-progress-circular>
            </p>
            <span v-else-if="bridge">
                <v-alert type="success" v-if="bridgesConnected">Bridge succesvol verbonden</v-alert>
                {{ bridge }}
            </span>
            <span v-else>
                <p  v-for="(item, key) in foundBridges" v-bind:key="key">
                <v-btn @click="connectBridge(item)" @tap="connectBridge(item)">Verbind bridge {{ item.internalipaddress }}</v-btn>
                </p>
                <v-alert type="error" v-if="foundBridges.length == 0">Geen Hue bridges gevonden</v-alert>
            </span>
        </v-card-text>
    </v-card>
    <v-card v-for="(item, key) in devices" v-bind:key="key" :color="getActiveColor(item.connected)">
        <v-card-title>{{ item.name }}</v-card-title>
        <v-card-text>
            <p>{{ item.id }}</p>
            <p v-if="item.connected">
                <strong>{{ item.group.name}}</strong><br>
            </p>    
        </v-card-text>
        <v-card-actions v-if="item.changeName">
            <v-text-field
            placeholder="Groep naam"
            solo
            :autofocus="true"
            v-model="item.group.name"
            
          ></v-text-field>
          <v-btn color="info" @click="save(item)" @tap="save(item)">Opslaan</v-btn>
          <v-btn color="none" @click="close(item)" @tap="close(item)">x</v-btn>
        </v-card-actions>
        <v-card-actions v-else-if="item.connected">
            <v-btn @click="connect(item)" @tap="connect(item)">Wijzig naam</v-btn>
            <v-btn color="error" @click="decoupleDialog(item)" @tap="decoupleDialog(item)">Ontkoppel</v-btn>
        </v-card-actions>
        <v-card-actions v-else>
             <v-btn color="success" @click="connect(item)" @tap="connect(item)">Koppel</v-btn> 
        </v-card-actions>
    </v-card>
</div>
</template>

<script>

import axios from 'axios';
import { setTimeout } from 'timers';

export default {
    name: 'Config',
    data: function() {
        return {
            pushButton: false,
            bridgesConnected: false,
            helpText: null,
            bridge: null,
            foundBridges: [],
            devices: []
        }
    },
    mounted: function() {
            var parent = this;

            axios.get(process.env.VUE_APP_ROOT_API + '/hue/bridge')
            .then(response => {
                if (response.data.lenth == 0) {
                    axios.get('https://discovery.meethue.com/')
                    .then(response => {
                        parent.foundBridges = response.data;
                    });
                } else {response.data.forEach(element => {
                        this.bridge = element.ip
                    });
                }
            });

            axios.get(process.env.VUE_APP_ROOT_API + '/hue/devices')
            .then(response => {
                response.data.forEach(device => {
                    
                    var myDevice = {
                        id: device.external_id,
                        name: device.name,
                        connected: false,
                        changeName: false,
                        group: {}
                    };

                    if (device.hasOwnProperty('group')) {
                        myDevice.connected = true;
                        myDevice.group = {
                            id: device.group.id,
                            name: device.group.name
                        }
                    }

                    this.devices.push(myDevice);
                });
            });

            
    },
    methods: {
        getImgUrl(pic) {
      return require("@/assets/" + pic);
    },
        getActiveColor(active) {
            if (active) {
                return 'success';
            }

            return 'white';
        },
        connect(item) {
            item.changeName = true;
        },
        save(item) {
            if (typeof item.group.name == 'undefined' || item.group.name == '') {
                alert('Vul een naam in');
                return;
            }
            
            item.changeName = false;

            if (item.hasOwnProperty('group') && item.group.hasOwnProperty('id')) {
                axios.put(process.env.VUE_APP_ROOT_API + '/group/' + item.group.id, {
                    external_id: item.id,
                    name: item.group.name
                }).then(response => {
                    item.connected = true;
                    item.group.id = response.data.id
                });
            } else {
                axios.post(process.env.VUE_APP_ROOT_API + '/group', {
                    external_id: item.id,
                    name: item.group.name
                }).then(response => {
                    item.connected = true;
                    item.group.id = response.data.id
                });
            }
        },
        close(item) {
            item.changeName = false;
        },
        decoupleDialog(item) {
            var shouldConfirm = confirm('Weet je zeker dat je ' + item.name + ' wilt ontkoppelen, alle sproeiers verbonden aan deze groep worden daarmee ook verwijderd.');
            if (!shouldConfirm) {
                return;
            }

            if (item.hasOwnProperty('group') && item.group.hasOwnProperty('id')) {
                axios.delete(process.env.VUE_APP_ROOT_API + '/group/' + item.group.id)
                .then(response => {
                    item.connected = false;
                    item.group = {};
                });
            }
        },
        connectBridge(item) {

            var parent = this;
            registerDevice(item);

            function registerDevice(item) {
            axios.post(process.env.VUE_APP_ROOT_API + '/hue/bridge', {
                ip: item.internalipaddress,
                username: 'paulcelie#gardenwater'
            })
            .then(response => {
                
                if (!response.data.id) {
                    parent.pushButton = true;
                    parent.helpText = 'Druk op de knop van Hue Bridge ' + response.data.ip;

                     setTimeout(registerDevice(item), 500);
                } else {
                    parent.bridge = response.data.ip;
                    parent.bridgesConnected = true;
                    parent.pushButton = false;
                    parent.helpText = null;
                }
            })
            }

            
        }
    }
}
</script>

<style>
    .v-card {
        margin-bottom: 10px;
    }

    .v-text-field {
        height: 50px;
        margin-right: 10px !important;
    }
</style>


