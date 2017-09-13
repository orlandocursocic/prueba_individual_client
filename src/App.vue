<template>
  <div id="app">
    <chooser></chooser>

    <h2 v-show="opcion == 'aeropuertos'"><strong>{{ title.Aeropuertos }}</strong></h2>
    <maestro-aeropuerto v-show="opcion == 'aeropuertos'"></maestro-aeropuerto>
    <detalle-aeropuerto v-show="opcion == 'aeropuertos'"></detalle-aeropuerto>

    <h2 v-show="opcion == 'vuelos'"><strong>{{ title.Vuelos }}</strong></h2>
    <maestro-vuelo v-show="opcion == 'vuelos'"></maestro-vuelo>
    <detalle-vuelo v-show="opcion == 'vuelos'"></detalle-vuelo>

    <modalmessage></modalmessage>
  </div>
</template>

<script>
import EventBus from './components/event-bus.js'
import Chooser from './components/Chooser.vue'
import MaestroAeropuerto from './components/maestro-aeropuerto.vue'
import MaestroVuelo from './components/maestro-vuelo.vue'
import DetalleAeropuerto from './components/Detalle-aeropuerto.vue'
import DetalleVuelo from './components/Detalle-vuelo.vue'
import ModalMessage from './components/ModalMessage.vue'

export default {
  components: {
    'chooser' : Chooser,
    'maestro-aeropuerto' : MaestroAeropuerto,
    'maestro-vuelo' : MaestroVuelo,
    'detalle-aeropuerto' : DetalleAeropuerto,
    'detalle-vuelo' : DetalleVuelo,
    'modalmessage' : ModalMessage
  },

  data: function() {
    return {
      title: {
        Aeropuertos: 'Administrador de Aeropuertos',
        Vuelos: 'Administrador de Vuelos'
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
