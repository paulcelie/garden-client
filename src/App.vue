<template>
  <v-app>
    <Menu @navigate="swapComponent"/>
    <v-content>
      <v-container grid-list-md fluid>
        <v-layout row wrap>
          <v-flex xs12>
            <div id="content">
              <div :is="currentComponent"></div>
            </div>
          </v-flex>
        </v-layout>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
import Menu from './components/Menu';
import Dashboard from './components/Dashboard';
import Sprinklers from './components/Sprinklers';
import Config from './components/Config';
import Routines from './components/Routines';
import External from './components/External';

import dotenv from 'dotenv';
dotenv.config();

export default {
  name: 'App',
  components: {
    Menu,
    Dashboard,
    Sprinklers,
    Config,
    Routines,
    External,
  },
  data: () => ({
    currentComponent: "Dashboard",
  }),
  methods: {
    swapComponent: function(component) {
      this.currentComponent = component;
    }
  }
};
</script>
