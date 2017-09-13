<template>
  <div class="w3-container w3-card-4" style="min-width:500px; display:inline-block; vertical-align:top">
    <div>
      <h3 style="overflow: hidden; text-overflow: ellipsis; max-width:300px"><strong>Vuelo: </strong>{{Vuelo.CodigoVuelo}}</h3>
      <div :class="codigoVueloValidationClasses">
        <label class="control-label" for="codVuelo"> Código de Vuelo </label>
        <textarea class="form-control" rows="1" style="resize: none; overflow: auto; text-overflow: ellipsis" type="string"
        name="codVuelo" value="codVuelo" :disabled="!editing && !addingNew" v-model="Vuelo.CodigoVuelo"></textarea>
        <p v-if="codigoVueloError != ''" class="help-block"> {{codigoVueloError}} </p>
      </div>
      <div :class="companyiaValidationClasses">
        <label class="control-label" for="companyia"> Compañía </label>
        <textarea class="form-control" rows="1" style="resize: none; overflow: auto; text-overflow: ellipsis" type="string"
        name="companyia" value="companyia" :disabled="!editing && !addingNew" v-model="Vuelo.Companyia"></textarea>
        <p v-if="companyiaError != ''" class="help-block"> {{companyiaError}} </p>
      </div>
      <div :class="origenValidationClasses">
        <label class="control-label" for="origin"> Aeropuerto de origen </label>
        <textarea class="form-control" rows="1" style="resize: none; overflow: auto; text-overflow: ellipsis" type="string"
        name="origin" value="origin" :disabled="!editing && !addingNew" v-model="Vuelo.Origen"></textarea>
        <p v-if="origenError != ''" class="help-block"> {{origenError}} </p>
      </div>
      <div :class="destinoValidationClasses">
        <label class="control-label" for="destination"> Aeropuerto de destino </label>
        <textarea class="form-control" rows="1" style="resize: none; overflow: auto; text-overflow: ellipsis" type="string"
        name="destination" value="destination" :disabled="!editing && !addingNew" v-model="Vuelo.Destino"></textarea>
        <p v-if="destinoError != ''" class="help-block"> {{destinoError}} </p>
      </div>
      <label class="w3-text" for="fechaSalida"> Fecha y hora de Salida </label>
      <input class="w3-input w3-border" style="background: white; overflow: hidden; text-overflow: ellipsis" type="datetime-local"
      name="fechaSalida" value="fechaSalida" :disabled="!editing && !addingNew" v-model="Vuelo.FechaSalida">
      <br>
      <label class="w3-text" for="fechaLlegada"> Fecha y hora de Llegada</label>
      <input class="w3-input w3-border" style="background: white; overflow: hidden; text-overflow: ellipsis" type="datetime-local"
      name="fechaLlegada" value="fechaLlegada" :disabled="!editing && !addingNew" v-model="Vuelo.FechaLlegada">
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

var httpURL = appConfig.URLVuelo;
var maxInt =  2147483647;

export default {
  components: {
    'app-icon' : AppIcon,
    'infomessage' : InfoMessage
  },

  methods: {
    validateNew: function() {
      let mensaje ='';
      if(this.Vuelo.CodigoVuelo == '') {
        mensaje = 'El código identificador del vuelo no puede estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Vuelo.Companyia == '') {
        mensaje = 'El nombre de la compañia del vuelo no puede estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Vuelo.Origen == '') {
        mensaje = 'El aeropuerto de origen del vuelo no puede estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Vuelo.Destino == '') {
        mensaje = 'El aeropuerto de destino del vuelo no puede estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Vuelo.FechaSalida == '') {
        mensaje = 'La fecha de salida del vuelo no puede estar vacía.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Vuelo.FechaLlegada == '') {
        mensaje = 'La fecha y hora de llegada del vuelo no pueden estar vacíos.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        this.create();
      }
    },
    validateIdUpdate: function() {
      let mensaje ='';
      if(this.Vuelo.Id =='' || this.Vuelo.Id < 0) {
        mensaje = 'Seleccione un vuelo de la lista.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        this.edit();
      }
    },
    validateIdDelete: function() {
      let mensaje ='';
      if(this.Vuelo.Id =='' || this.Vuelo.Id < 0) {
        mensaje = 'Seleccione un vuelo de la lista.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        this.remove();
      }
    },
    validateUpdate: function() {
      let mensaje ='';
      if(this.Vuelo.CodigoVuelo == '') {
        mensaje = 'El código identificador del vuelo no puede estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Vuelo.Companyia == '') {
        mensaje = 'El nombre de la compañia del vuelo no puede estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Vuelo.Origen == '') {
        mensaje = 'El aeropuerto de origen del vuelo no puede estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Vuelo.Destino == '') {
        mensaje = 'El aeropuerto de destino del vuelo no puede estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Vuelo.FechaSalida == '') {
        mensaje = 'La fecha de salida del vuelo no puede estar vacía.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Vuelo.FechaLlegada == '') {
        mensaje = 'La fecha y hora de llegada del vuelo no pueden estar vacíos.';
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
      this.VueloCopia.CodigoVuelo = this.Vuelo.CodigoVuelo;
      this.VueloCopia.Companyia = this.Vuelo.Companyia;
      this.VueloCopia.Origen = this.Vuelo.Origen;
      this.VueloCopia.Destino = this.Vuelo.Destino;
      this.VueloCopia.FechaSalida = this.Vuelo.FechaSalida;
      this.VueloCopia.FechaLlegada = this.Vuelo.FechaLlegada;

      this.Vuelo.CodigoVuelo = '';
      this.Vuelo.Companyia = '';
      this.Vuelo.Origen = '';
      this.Vuelo.Destino = '';
      this.Vuelo.FechaSalida = '';
      this.Vuelo.FechaLlegada = '';
      this.addingNew = true;
    },
    discardNew: function () {
      this.Vuelo.CodigoVuelo = this.VueloCopia.CodigoVuelo;
      this.Vuelo.Companyia = this.VueloCopia.Companyia;
      this.Vuelo.Origen = this.VueloCopia.Origen;
      this.Vuelo.Destino = this.VueloCopia.Destino;
      this.Vuelo.FechaSalida = this.VueloCopia.FechaSalida;
      this.Vuelo.FechaLlegada = this.VueloCopia.FechaLlegada;
      this.addingNew = false;
    },
    edit: function () {
      this.VueloCopia.CodigoVuelo = this.Vuelo.CodigoVuelo;
      this.VueloCopia.Companyia = this.Vuelo.Companyia;
      this.VueloCopia.Origen = this.Vuelo.Origen;
      this.VueloCopia.Destino = this.Vuelo.Destino;
      this.VueloCopia.FechaSalida = this.Vuelo.FechaSalida;
      this.VueloCopia.FechaLlegada = this.Vuelo.FechaLlegada;
      this.editing = true;
    },
    discard: function () {
      this.Vuelo.CodigoVuelo = this.VueloCopia.CodigoVuelo;
      this.Vuelo.Companyia = this.VueloCopia.Companyia;
      this.Vuelo.Origen = this.VueloCopia.Origen;
      this.Vuelo.Destino = this.VueloCopia.Destino;
      this.Vuelo.FechaSalida = this.VueloCopia.FechaSalida;
      this.Vuelo.FechaLlegada = this.VueloCopia.FechaLlegada;
      this.editing = false;
    },
    cleanForm: function() {
      this.Vuelo.Id = '';
      this.Vuelo.CodigoVuelo = '';
      this.Vuelo.Companyia = '';
      this.Vuelo.Origen = '';
      this.Vuelo.Destino = '';
      this.Vuelo.FechaSalida = '';
      this.Vuelo.FechaLlegada = '';
    },
    create: function () {
      var _this = this;
      $.ajax(
        {
          url : httpURL,
          type: "POST",
          data: {
            CodigoVuelo: this.Vuelo.CodigoVuelo,
            Companyia: this.Vuelo.Companyia,
            Origen: this.Vuelo.Origen,
            Destino: this.Vuelo.Destino,
            FechaSalida: this.Vuelo.FechaSalida,
            FechaLlegada: this.Vuelo.FechaLlegada
          }
        })
        .done(function(data) {
          EventBus.$emit('updateListVuelo');
          let mensaje ='Vuelo añadido con éxito.';
          EventBus.$emit('showMessage', mensaje);
          _this.addingNew = false;
        })
        .fail(function(data) {
          let mensaje = 'No se pudo crear el vuelo. Revise su conexión a Internet.';
          EventBus.$emit('showMessage', mensaje);
        });
      },
      update: function () {
        var _this = this;
        $.ajax(
          {
            url : httpURL + this.Vuelo.Id,
            type: "PUT",
            data: {
              Id: this.Vuelo.Id,
              CodigoVuelo: this.Vuelo.CodigoVuelo,
              Companyia: this.Vuelo.Companyia,
              Origen: this.Vuelo.Origen,
              Destino: this.Vuelo.Destino,
              FechaSalida: this.Vuelo.FechaSalida,
              FechaLlegada: this.Vuelo.FechaLlegada
            }
          })
          .done(function(data) {
            EventBus.$emit('updateListVuelo');
            let mensaje ='Vuelo actualizado con éxito.';
            EventBus.$emit('showMessage', mensaje);
            _this.cleanForm();
            _this.editing = false;
          })
          .fail(function(data) {
            let mensaje = 'No se pudo actualizar el vuelo. Revise su conexión a Internet.';
            EventBus.$emit('showMessage', mensaje);
          });
        },
        remove: function () {
          var _this = this;
          $.ajax(
            {
              url : httpURL + this.Vuelo.Id,
              type: "DELETE",
              data: {Id: this.Vuelo.Id}
            })
            .done(function(data) {
              EventBus.$emit('updateListVuelo');
              _this.cleanForm();
              let mensaje ='Vuelo eliminado con éxito.';
              EventBus.$emit('showMessage', mensaje);
              _this.editing = false;
            })
            .fail(function(data) {
              let mensaje = 'No se pudo eliminar el vuelo. Revise su conexión a Internet.';
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

                _this.Vuelo = data;
              })
              .fail(function(data) {
                let mensaje = 'No se pudo cargar los vuelos. Revise su conexión a Internet.';
                EventBus.$emit('showMessage', mensaje);
              });
            },
          },

          data: function () {
            return {
              editing: false,
              addingNew: false,
              Vuelo: {
                Id: '',
                CodigoVuelo: '',
                Companyia: '',
                Origen: '',
                Destino: '',
                FechaSalida: '',
                FechaLlegada: ''
              },
              VueloCopia: {
                Id: '',
                CodigoVuelo: '',
                Companyia: '',
                Origen: '',
                Destino: '',
                FechaSalida: '',
                FechaLlegada: ''
              }
            }
          },

          mounted: function() {
            EventBus.$on('vueloSelected', function(id) {
              this.load(id);
              this.editing = false;
              this.addingNew = false;
            }.bind(this));
          }
        }

        </script>
