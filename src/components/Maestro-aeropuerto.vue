<template>
  <div class="teal lighten-5" style="min-width:300px; max-width:300px; display:inline-block; vertical-align:top">
    <ul class="w3-ul w3-card-4">
      <li class="teal-text"><h3><strong>Aeropuertos</strong></h3></li>
      <li style="overflow: hidden; text-overflow: ellipsis" class="teal-text w3-hover-teal" v-for="Aeropuerto in Aeropuertos" @click="aeropuertoSelected(Aeropuerto.Id)">
        {{Aeropuerto.Nombre}}</li>
    </ul>
  </div>
</template>

<script>
import EventBus from './event-bus.js'
import AppIcon from './App-icon.vue'
import appConfig from './config.js'

var httpURL = appConfig.URLAeropuerto;

export default {
  components: {
    'app-icon' : AppIcon
  },

  methods: {

    loadList: function(){
      var _this = this;
      $.ajax(
        {
          url : httpURL,
          type: "GET",
        })
        .done(function(data) {
          _this.Aeropuertos = data;
        })
        .fail(function(data) {
          let mensaje = 'No se pudo cargar la lista. Revise su conexión a Internet.';
          EventBus.$emit('showMessage', mensaje);
        });
      },

      aeropuertoSelected: function(id){
        EventBus.$emit('aeropuertoSelected', id);
      }
    },

    data: function () {
      return {
        Aeropuertos: [],
      }
    },

    mounted: function() {
      this.loadList();

      EventBus.$on('updateListAeropuerto', function() {
        this.loadList();
      }.bind(this));
    }
  }

  </script>
