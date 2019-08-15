<template>
<div>
    
    <v-stage :config="gardenArea">
    <v-layer>
        <v-shape :config="grass"/>
        <v-shape :config="border"/>
        <v-circle v-for="(item, key) in sprinklers" v-bind:key="key" :config="getSprinkler(item)" @click="handleSprinklerClick" @tap="handleSprinklerClick" @mouseenter="showMousePointer" @mouseleave="showMouseDefault"/>
    </v-layer>
  </v-stage>
      <v-overlay v-if="showActions">
      <p class="text-right" @click="hideActions" @tap="hideActions"><v-icon>mdi-close</v-icon></p>
  <GroupModal :id="currentGroupId"/>
      </v-overlay>

</div>
</template>

<script>

import Vue from 'vue';
import VueKonva from 'vue-konva'
import GroupModal from './GroupModal';
import axios from 'axios';

Vue.use(VueKonva)

export default {
    name: "Dashboard",
    components: {
        GroupModal
    },
    data() {
    return {
     group: 'Groep 1',
     currentGroupId: null,
     showActions: false,
      gardenArea: {
        width: 400,
        height: 452
      },
      grass: {
        sceneFunc: function(context, shape) {
          context.beginPath();
          context.moveTo(0, 0);
          context.lineTo(400, 0);
          context.lineTo(400, 340);
          context.lineTo(0, 452);
          context.closePath();

          // special Konva.js method
          context.fillStrokeShape(shape);
        },
        fill: '#526F35',
        stroke: 'black',
        strokeWidth: 0
      },
      border: {
        sceneFunc: function(context, shape) {
          context.beginPath();
          context.moveTo(0, 452);
          context.lineTo(0, 280);
          context.lineTo(80, 280);
          context.lineTo(80, 428);
          context.closePath();

          context.fillStrokeShape(shape);
        },
        fill: '#7c5e42',
        stroke: '#614933',
        strokeWidth: 1
      },
      sprinklers: [],
    };
  },
  mounted: function() {
          axios.get(process.env.VUE_APP_ROOT_API + "/group")
          .then(response => {
              response.data.forEach(group => {
                  group.sprinklers.forEach(sprinkler => {
                    this.sprinklers.push({
                      name: sprinkler.name,
                      group: group.name,
                      id: group.id,
                      x: sprinkler.x,
                      y: sprinkler.y,
                      active: false
                    });
                  })
              });
          });            
      },
   methods: {
      getSprinkler(sprinkler) {

          var strokeWidth = sprinkler.active ? 10 : 0;
          var strokeColor = sprinkler.active ? "lightblue" : "";
          var fillColor = sprinkler.active ? "#0077be" : "lightblue";

          return {
           x: sprinkler.x,
          y: sprinkler.y,
          id: sprinkler.id,
          width: 40,
          height: 40,
          strokeWidth: strokeWidth,
          stroke: strokeColor,
          fill: fillColor,
    
          }
      },
      handleSprinklerClick(event) {
        this.currentGroupId = event.target.attrs.id;
        this.showActions = true;
      },
      showMousePointer(event)
      {
          event.target.parent.parent.container().style.cursor = 'pointer'
      },
      showMouseDefault(event)
      {
        event.target.parent.parent.container().style.cursor = 'default'
      },
      hideActions()
        {
            this.showActions = false
        }
  }
}
</script>


