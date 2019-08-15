<template>
    <div>
        <v-navigation-drawer v-model="drawer" clipped fixed app dark>
            <v-list-item>
                <v-list-item-content>
                <v-list-item-title class="title">
                    Mijn tuin
                </v-list-item-title>
                <v-list-item-subtitle>
                    beregening
                </v-list-item-subtitle>
                </v-list-item-content>
            </v-list-item>
            <v-list
                dense
                nav
            >
                <v-list-item
                v-for="item in items"
                :key="item.title"
                link @click="navigate(item.component)"
                >
                <v-list-item-icon>
                    <v-icon>{{ item.icon }}</v-icon>
                </v-list-item-icon>

                <v-list-item-content>
                    <v-list-item-title>{{ item.title }}</v-list-item-title>
                </v-list-item-content>
                </v-list-item>
            </v-list>
        </v-navigation-drawer>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
    </div>
</template>

<script>
export default {
    data() {
        return {
        items: [
          { title: 'Tuin', icon: 'mdi-flower', component: 'Dashboard' },
          { title: 'Sproeiers', icon: 'mdi-weather-pouring', component: 'Sprinklers' },
          { title: 'Routines', icon: 'mdi-camera-timer', component: 'Routines' },
          { title: 'Koppeling Hue', icon: 'mdi-lamp', component: 'Config' },
          { title: 'Externe services', icon: 'mdi-settings', component: 'External' },
        ],
        right: true,
        drawer: null
      }
    },
    methods: {
    navigate(component) {
      this.drawer = null;
      this.$emit("navigate", component);
    }
  }
}
</script>

