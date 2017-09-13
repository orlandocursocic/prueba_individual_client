<template>
  <div>
    <button type="button" class="btn btn-default" title="Aeroperto" @click="chooseOption(options.Aeropuertos)">
      <app-icon img="th-list"></app-icon> Aeropuertos
    </button>
    <button type="button" class="btn btn-default" title="Vuelo" @click="chooseOption(options.Vuelos)">
     <app-icon img="th-list"></app-icon> Vuelos
    </button>
  </div>
</template>

<script>
import EventBus from './event-bus.js'
import AppIcon from './App-icon.vue'

export default {
  components: {
    'app-icon' : AppIcon
  },

  data: function() {
    return {
      options: {
        Aeropuertos: 'aeropuertos',
        Vuelos: 'vuelos'
      }
    }
  },
  methods: {
    chooseOption: function(opcion){
      EventBus.$emit('chooseOption', opcion);
    }
  }
}

</script>
