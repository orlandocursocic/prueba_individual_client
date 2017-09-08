<template>
  <div id="app">
    <chooser></chooser>

    <h2 v-show="opcion == 'aeropuertos'"><strong>{{ title.Aeropuertos }}</strong></h2>
    <maestro-aeropuerto v-show="opcion == 'aeropuertos'"></maestro-aeropuerto>

    <h2 v-show="opcion == 'vuelos'"><strong>{{ title.Vuelos }}</strong></h2>
    <maestro-vuelo v-show="opcion == 'vuelos'"></maestro-vuelo>

    <infomessage style="clear:both"></infomessage>
  </div>
</template>

<script>
import EventBus from './components/event-bus.js'
import Chooser from './components/Chooser.vue'
import MaestroAeropuerto from './components/maestro-aeropuerto.vue'
import MaestroVuelo from './components/maestro-vuelo.vue'
import InfoMessage from './components/InfoMessage.vue'

export default {
  components: {
    'chooser' : Chooser,
    'maestro-aeropuerto' : MaestroAeropuerto,
    'maestro-vuelo' : MaestroVuelo,
    'infomessage' : InfoMessage
  },

  data: function() {
    return {
      title: {
        Aeropuertos: 'Administrador de Aeropuerto',
        Vuelos: 'Administrador de Vuelo'
      },
      //Opción por defecto
      opcion: 'aeropuertos'
    }
  },

  mounted: function(){
    EventBus.$on('chooseOption', function(opcion) {
      this.opcion = opcion;
    }.bind(this));
  }
}
</script>
