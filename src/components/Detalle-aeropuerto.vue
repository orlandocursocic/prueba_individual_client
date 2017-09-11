<template>
  <div class="w3-container w3-card-4" style="min-width:300px; display:inline-block; vertical-align:top">
    <div>
      <h3 style="overflow: hidden; text-overflow: ellipsis; max-width:300px"><strong>Aeropuerto: </strong>{{Aeropuerto.Nombre}}</h3>
      <label class="w3-text" for="nombre"> Nombre </label>
      <textarea class="w3-input w3-border" rows="2" style="background: white; resize: none; overflow: auto; text-overflow: ellipsis" type="string"
      name="nombre" value="Nombre" :disabled="!editing && !addingNew" v-model="Aeropuerto.Nombre"></textarea>
      <br>
      <label class="w3-text" for="desc"> Número de Terminales </label>
      <textarea class="w3-input w3-border" rows="2" style="background: white; resize: none; overflow: auto; text-overflow: ellipsis" type="numeric"
      name="desc" value="numTerminales" :disabled="!editing && !addingNew" v-model="Aeropuerto.numTerminales"></textarea>
      <br>
      <label class="w3-text" for="desc"> Ciudad </label>
      <textarea class="w3-input w3-border" rows="2" style="background: white; resize: none; overflow: auto; text-overflow: ellipsis" type="string"
      name="desc" value="Ciudad" :disabled="!editing && !addingNew" v-model="Aeropuerto.Ciudad"></textarea>
      <br>
      <label class="w3-text" for="desc"> País </label>
      <textarea class="w3-input w3-border" rows="2" style="background: white; resize: none; overflow: auto; text-overflow: ellipsis" type="string"
      name="desc" value="Pais" :disabled="!editing && !addingNew" v-model="Aeropuerto.Pais"></textarea>
      <br>
    </div>

    <br>

    <div>
      <template v-if="!addingNew">
        <div style="float: left">
          <button type="button" class="btn btn-default btn-sm" title="Nuevo" @click="editNew" :disabled="editing">
            <app-icon img="plus"></app-icon>
          </button>
        </div>
      </template>

      <template v-else>
        <div style="float: left">
          <button type="button" class="btn btn-default btn-sm" title="Confirmar" @click="validateNew">
            <app-icon img="ok"></app-icon>
          </button>
          <button type="button" class="btn btn-default btn-sm" title="Descartar" @click="discardNew">
            <app-icon img="remove"></app-icon>
          </button>
        </div>
      </template>

      <template v-if="!editing">
        <div style="float: right">
          <button type="button" class="btn btn-default btn-sm" title="Editar" @click="validateIdUpdate" :disabled="addingNew">
            <app-icon img="edit"></app-icon>
          </button>
          <button type="button" class="btn btn-default btn-sm" title="Eliminar" @click="validateIdDelete" :disabled="addingNew">
            <app-icon img="trash"></app-icon>
          </button>
        </div>
      </template>

      <template v-else>
        <div style="float: right">
          <button type="button" class="btn btn-default btn-sm" title="Confirmar" @click="validateUpdate">
            <app-icon img="ok"></app-icon>
          </button>
          <button type="button" class="btn btn-default btn-sm" title="Descartar" @click="discard">
            <app-icon img="remove"></app-icon>
          </button>
        </div>
      </template>
    </div>

    <div>
      <p style="visibility: hidden">separador</p>
    </div>
    <br>
  </div>
</template>

<script>
import EventBus from './event-bus.js'
import AppIcon from './App-icon.vue'
import appConfig from './config.js'
import InfoMessage from './InfoMessage.vue'

var httpURL = appConfig.URLAeropuerto;
var maxInt =  2147483647;

export default {
  components: {
    'app-icon' : AppIcon,
    'infomessage' : InfoMessage
  },

  methods: {
    validateNew: function() {
      let mensaje ='';
      if(this.Aeropuerto.Nombre == '') {
        mensaje = 'El nombre del aeuropuerto no puede estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.isNumeric(this.Aeropuerto.numTerminales) || this.Aeropuerto.numTerminales < 1) {
        mensaje = 'El número de terminales del aeropuerto no puede ser menor que 0.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Aeropuerto.Ciudad == '') {
        mensaje = 'La ciudad en la que se encuentra el aeropuerto no puede estar vacía.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Aeropuerto.Pais == '') {
        mensaje = 'El país en el que se encuentra el aeropeurto no puede estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        this.create();
      }
    },
    validateIdUpdate: function() {
      let mensaje ='';
      if(this.Aeropuerto.Id =='' || this.Aeropuerto.Id < 0) {
        mensaje = 'Seleccione un aeropuerto de la lista.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        this.edit();
      }
    },
    validateIdDelete: function() {
      let mensaje ='';
      if(this.Aeropuerto.Id =='' || this.Aeropuerto.Id < 0) {
        mensaje = 'Seleccione un aeropuerto de la lista.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        this.remove();
      }
    },
    validateUpdate: function() {
      let mensaje ='';
      if(this.Aeropuerto.Nombre == '') {
        mensaje = 'El nombre del aeuropuerto no puede estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else if(!this.isInt(this.Aeropuerto.numTerminales) || this.Aeropuerto.numTerminales < 1) {
        mensaje = 'El número de terminales del aeropuerto no puede ser menor que 0.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Aeropuerto.Ciudad == '') {
        mensaje = 'La ciudad en la que se encuentra el aeropuerto no puede estar vacía.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Aeropuerto.Pais == '') {
        mensaje = 'El país en el que se encuentra el aeropeurto no puede estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        this.update();
      }
    },
    isNumeric: function(n) {
      return !isNaN(parseFloat(n)) && isFinite(n);
    },
    isInt: function(n) {
      return n % 1 === 0;
    },
    editNew: function () {
      this.AeropuertoCopia.Nombre = this.Aeropuerto.Nombre;
      this.AeropuertoCopia.numTerminales = this.Aeropuerto.numTerminales;
      this.AeropuertoCopia.Ciudad = this.Aeropuerto.Ciudad;
      this.AeropuertoCopia.Pais = this.Aeropuerto.Pais;

      this.Aeropuerto.Nombre = '';
      this.Aeropuerto.numTerminales = '';
      this.Aeropuerto.Ciudad = '';
      this.Aeropuerto.Pais = '';
      this.addingNew = true;
    },
    discardNew: function () {
      this.Aeropuerto.Nombre = this.AeropuertoCopia.Nombre;
      this.Aeropuerto.numTerminales = this.AeropuertoCopia.numTerminales;
      this.Aeropuerto.Ciudad = this.AeropuertoCopia.Ciudad;
      this.Aeropuerto.Pais = this.AeropuertoCopia.Pais;
      this.addingNew = false;
    },
    edit: function () {
       this.AeropuertoCopia.Nombre = this.Aeropuerto.Nombre;
      this.AeropuertoCopia.numTerminales = this.Aeropuerto.numTerminales;
      this.AeropuertoCopia.Ciudad = this.Aeropuerto.Ciudad;
      this.AeropuertoCopia.Pais = this.Aeropuerto.Pais;
      this.editing = true;
    },
    discard: function () {
      this.Aeropuerto.Nombre = this.AeropuertoCopia.Nombre;
      this.Aeropuerto.numTerminales = this.AeropuertoCopia.numTerminales;
      this.Aeropuerto.Ciudad = this.AeropuertoCopia.Ciudad;
      this.Aeropuerto.Pais = this.AeropuertoCopia.Pais;
      this.editing = false;
    },
    cleanForm: function() {
      this.Aeropuerto.Id = '';
      this.Aeropuerto.Nombre = '';
      this.Aeropuerto.numTerminales = '';
      this.Aeropuerto.Ciudad = '';
      this.Aeropuerto.Pais = '';
    },
    create: function () {
      var _this = this;
      $.ajax(
        {
          url : httpURL,
          type: "POST",
          data: {
            Nombre: this.Aeropuerto.Nombre,
            numTerminales: this.Aeropuerto.numTerminales,
            Ciudad: this.Aeropuerto.Ciudad,
            Pais: this.Aeropuerto.Pais
          }

        })
        .done(function(data) {
          EventBus.$emit('updateListAeropuerto');
          let mensaje ='Aeropuerto añadido con éxito.';
          EventBus.$emit('showMessage', mensaje);
          _this.addingNew = false;
        })
        .fail(function(data) {
          let mensaje = 'No se pudo crear el aeropuerto. Revise su conexión a Internet.';
          EventBus.$emit('showMessage', mensaje);
        });
      },
      update: function () {
        var _this = this;
        $.ajax(
          {
            url : httpURL + this.Aeropuerto.Id,
            type: "PUT",
            data: {
              Id: this.Aeropuerto.Id,
              Nombre: this.Aeropuerto.Nombre,
              numTerminales: this.Aeropuerto.numTerminales,
              Ciudad: this.Aeropuerto.Ciudad,
              Pais: this.Aeropuerto.Pais
            }
          })
          .done(function(data) {
            EventBus.$emit('updateListAeropuerto');
            let mensaje ='Aeropuerto actualizado con éxito.';
            EventBus.$emit('showMessage', mensaje);
            _this.cleanForm();
            _this.editing = false;
          })
          .fail(function(data) {
            let mensaje = 'No se pudo actualizar el aeropuerto. Revise su conexión a Internet.';
            EventBus.$emit('showMessage', mensaje);
          });
        },
        remove: function () {
          var _this = this;
          $.ajax(
            {
              url : httpURL + this.Aeropuerto.Id,
              type: "DELETE",
              data: {Id: this.Aeropuerto.Id}
            })
            .done(function(data) {
              EventBus.$emit('updateListAeropuerto');
              _this.cleanForm();
              let mensaje ='Aeropuerto eliminado con éxito.';
              EventBus.$emit('showMessage', mensaje);
              _this.editing = false;
            })
            .fail(function(data) {
              let mensaje = 'No se pudo eliminar el aeropuerto. Revise su conexión a Internet.';
              EventBus.$emit('showMessage', mensaje);
            });
          },
          load: function(id){
            var _this = this;
            $.ajax(
              {
                url : httpURL + id,
                type: "GET",
              })
              .done(function(data) {

                _this.Aeropuerto = data;
              })
              .fail(function(data) {
                let mensaje = 'No se pudo cargar los aeropuertos. Revise su conexión a Internet.';
                EventBus.$emit('showMessage', mensaje);
              });
            },
          },

          data: function () {
            return {
              editing: false,
              addingNew: false,
              Aeropuerto: {
                Id: '',
                Nombre: '',
                numTerminales: '',
                Ciudad: '',
                Pais: ''
              },
              AeropuertoCopia: {
                Id: '',
                Nombre: '',
                numTerminales: '',
                Ciudad: '',
                Pais: ''
              }
            }
          },

          mounted: function() {
            EventBus.$on('aeropuertoSelected', function(id) {
              this.load(id);
              this.editing = false;
              this.addingNew = false;
            }.bind(this));
          }
        }

        </script>
