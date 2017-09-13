<template>
  <div style="min-width:400px; max-width:300px; display:inline-block; vertical-align:top">
    <ul class="w3-ul w3-card-4">
      <li><h2><strong>Vuelos</strong></h2></li>
      <li style="overflow: hidden; text-overflow: ellipsis" class="w3-hover-blue" v-for="Vuelo in Vuelos" @click="vueloSelected(Vuelo.Id)">
        {{Vuelo.CodigoVuelo}}</li>
    </ul>
  </div>
</template>

<script>
import EventBus from './event-bus.js'
import AppIcon from './App-icon.vue'
import appConfig from './config.js'

var httpURL = appConfig.URLVuelo;

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
          _this.Vuelos = data;
        })
        .fail(function(data) {
          let mensaje = 'No se pudo cargar la lista. Revise su conexión a Internet.';
          EventBus.$emit('showMessage', mensaje);
        });
      },

      vueloSelected: function(id){
        EventBus.$emit('vueloSelected', id);
      }
    },

    data: function () {
      return {
        Vuelos: [],
      }
    },

    mounted: function() {
      this.loadList();

      EventBus.$on('updateListVuelo', function() {
        this.loadList();
      }.bind(this));
    }
  }

  </script>
