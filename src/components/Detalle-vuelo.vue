<template>
  <div class="w3-container w3-card-4 teal lighten-5" style="min-width:700px; max-width:700px; display:inline-block; vertical-align:top">
    <div>
      <h3 class="teal-text" style="overflow: hidden; text-overflow: ellipsis; max-width:700px"><strong>Vuelo: </strong>{{Vuelo.CodigoVuelo}}</h3>
      <div :class="codigoVueloValidationClasses">
        <label class="teal-text control-label" for="codVuelo"> Código de Vuelo </label>
        <textarea class="form-control" rows="1" style="resize: none; overflow: auto; text-overflow: ellipsis" type="string"
        name="codVuelo" value="codVuelo" :disabled="!editing && !addingNew" v-model="Vuelo.CodigoVuelo"></textarea>
        <p v-if="codigoVueloError != ''" class="help-block"> {{codigoVueloError}} </p>
      </div>
      <div :class="companyiaValidationClasses">
        <label class="teal-text control-label" for="companyia"> Compañía </label>
        <textarea class="form-control" rows="1" style="resize: none; overflow: auto; text-overflow: ellipsis" type="string"
        name="companyia" value="companyia" :disabled="!editing && !addingNew" v-model="Vuelo.Companyia"></textarea>
        <p v-if="companyiaError != ''" class="help-block"> {{companyiaError}} </p>
      </div>
      <div :class="origenValidationClasses">
        <label class="teal-text control-label" for="origin"> Aeropuerto de origen </label>
        <textarea class="form-control" rows="1" style="resize: none; overflow: auto; text-overflow: ellipsis" type="string"
        name="origin" value="origin" :disabled="!editing && !addingNew" v-model="Vuelo.Origen"></textarea>
        <p v-if="origenError != ''" class="help-block"> {{origenError}} </p>
      </div>
      <div :class="destinoValidationClasses">
        <label class="teal-text control-label" for="destination"> Aeropuerto de destino </label>
        <textarea class="form-control" rows="1" style="resize: none; overflow: auto; text-overflow: ellipsis" type="string"
        name="destination" value="destination" :disabled="!editing && !addingNew" v-model="Vuelo.Destino"></textarea>
        <p v-if="destinoError != ''" class="help-block"> {{destinoError}} </p>
      </div>
      <div :class="fechaSalidaValidationClasses">
        <label class="teal-text control-label" for="fechaSalida"> Fecha y hora de Salida </label>
        <input class="form-control" style="overflow: hidden; text-overflow: ellipsis" type="datetime-local"
        name="fechaSalida" value="fechaSalida" :disabled="!editing && !addingNew" v-model="Vuelo.FechaSalida">
        <p v-if="fechaSalidaError != ''" class="help-block"> {{fechaSalidaError}} </p>
      </div>
      <div :class="fechaLlegadaValidationClasses">
        <label class="teal-text control-label" for="fechaLlegada"> Fecha y hora de Llegada</label>
        <input class="form-control" style="overflow: hidden; text-overflow: ellipsis" type="datetime-local"
        name="fechaLlegada" value="fechaLlegada" :disabled="!editing && !addingNew" v-model="Vuelo.FechaLlegada">
        <p v-if="fechaLlegadaError != ''" class="help-block"> {{fechaLlegadaError}} </p>
      </div>
    </div>

    <div>
      <template v-if="!addingNew">
        <div style="float: left">
          <button type="button" class="waves-effect waves-light btn btn-default btn-sm" title="Nuevo" @click="editNew" :disabled="editing">
            <app-icon img="plus"></app-icon> Nuevo
          </button>
        </div>
      </template>

      <template v-else>
        <div style="float: left">
          <button type="button" class="waves-effect waves-light btn btn-default btn-sm" title="Confirmar" @click="validateNew" :disabled="!allModified">
            <app-icon img="ok"></app-icon> Confirmar
          </button>
          <button type="button" class="waves-effect waves-light btn btn-default btn-sm" title="Descartar" @click="discardNew">
            <app-icon img="remove"></app-icon> Cancelar
          </button>
        </div>
      </template>

      <template v-if="!editing">
        <div style="float: right">
          <button type="button" class="waves-effect waves-light btn btn-default btn-sm" title="Editar" @click="validateIdUpdate" :disabled="addingNew">
            <app-icon img="edit"></app-icon> Editar
          </button>
          <button type="button" class="waves-effect waves-light btn btn-default btn-sm" title="Eliminar" @click="validateIdDelete" :disabled="addingNew">
            <app-icon img="trash"></app-icon> Eliminar
          </button>
        </div>
      </template>

      <template v-else>
        <div style="float: right">
          <button type="button" class="waves-effect waves-light btn btn-default btn-sm" title="Confirmar" @click="validateUpdate" :disabled="!anyModified">
            <app-icon img="ok"></app-icon> Confirmar
          </button>
          <button type="button" class="waves-effect waves-light btn btn-default btn-sm" title="Descartar" @click="discard">
            <app-icon img="remove"></app-icon> Cancelar
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
  computed: {
    anyModified: function() {
      if (this.modified.CodigoVuelo || this.modified.Companyia || this.modified.Origen || this.modified.Destino || this.modified.FechaSalida || this.modified.FechaLlegada) {
        return true;
      }
      return false;
    },
    allModified: function() {
      if (this.modified.CodigoVuelo && this.modified.Companyia && this.modified.Origen && this.modified.Destino && this.modified.FechaSalida && this.modified.FechaLlegada) {
        return true;
      }
      return false;
    },
    codigoVueloError: function(){
      var val = this.Vuelo.CodigoVuelo.trim();

      if(this.Vuelo.CodigoVuelo != '' && this.Vuelo.CodigoVuelo != this.VueloCopia.CodigoVuelo){
        this.modified.CodigoVuelo = true;
      }

      if(this.modified.CodigoVuelo) {
        if (val == ''){
          this.validated.CodigoVuelo = false;
          return 'El campo es obligatorio'
        }
        this.validated.CodigoVuelo = true;
      }
      return '';
    },
    codigoVueloValidationClasses: function(){
      return [
        'form-group',
        {'has-error': this.codigoVueloError}
      ];
    },
    companyiaError: function(){
      var val = this.Vuelo.Companyia.trim();

      if(this.Vuelo.Companyia != '' && this.Vuelo.Companyia != this.VueloCopia.Companyia){
        this.modified.Companyia = true;
      }

      if(this.modified.Companyia) {
        if (val == ''){
          this.validated.Companyia = false;
          return 'El campo es obligatorio'
        }
        this.validated.Companyia = true;
      }
      return '';
    },
    companyiaValidationClasses: function(){
      return [
        'form-group',
        {'has-error': this.companyiaError}
      ];
    },
    origenError: function(){
      var val = this.Vuelo.Origen.trim();

      if(this.Vuelo.Origen != '' && this.Vuelo.Origen != this.VueloCopia.Origen){
        this.modified.Origen = true;
      }

      if(this.modified.Origen) {
        if (val == ''){
          this.validated.Origen = false;
          return 'El campo es obligatorio'
        }
        this.validated.Origen = true;
      }
      return '';
    },
    origenValidationClasses: function(){
      return [
        'form-group',
        {'has-error': this.origenError}
      ];
    },
    destinoError: function(){
      var val = this.Vuelo.Destino.trim();

      if(this.Vuelo.Destino != '' && this.Vuelo.Destino != this.VueloCopia.Destino){
        this.modified.Destino = true;
      }

      if(this.modified.Destino) {
        if (val == ''){
          this.validated.Destino = false;
          return 'El campo es obligatorio'
        }
        this.validated.Destino = true;
      }
      return '';
    },
    destinoValidationClasses: function(){
      return [
        'form-group',
        {'has-error': this.destinoError}
      ];
    },
    fechaSalidaError: function(){
      var val = this.Vuelo.FechaSalida.trim();

      if(this.Vuelo.FechaSalida != '' && this.Vuelo.FechaSalida != this.VueloCopia.FechaSalida){
        this.modified.FechaSalida = true;
      }

      if(this.modified.FechaSalida) {
        if (val == ''){
          this.validated.FechaSalida = false;
          return 'El campo es obligatorio'
        }
        this.validated.FechaSalida = true;
      }
      return '';
    },
    fechaSalidaValidationClasses: function(){
      return [
        'form-group',
        {'has-error': this.fechaSalidaError}
      ];
    },
    fechaLlegadaError: function(){
      var val = this.Vuelo.FechaLlegada.trim();

      if(this.Vuelo.FechaLlegada != '' && this.Vuelo.FechaLlegada != this.VueloCopia.FechaLlegada){
        this.modified.FechaLlegada = true;
      }

      if(this.modified.FechaLlegada) {
        if (val == ''){
          this.validated.FechaLlegada = false;
          return 'El campo es obligatorio'
        }
        this.validated.FechaLlegada = true;
      }
      return '';
    },
    fechaLlegadaValidationClasses: function(){
      return [
        'form-group',
        {'has-error': this.fechaLlegadaError}
      ];
    },
  },
  methods: {
    validatedAll: function() {
      if (this.validated.CodigoVuelo && this.validated.Companyia && this.validated.Origen && this.validated.Destino && this.validated.FechaSalida && this.validated.FechaLlegada) {
        return true;
      }
      return false;
    },
    validateNew: function() {
      let mensaje = {
        modalTitle: '',
        modalBody: ''
      };
      if(this.validatedAll()){
        this.create();
      } else {
        mensaje.modalTitle = 'Error en la validación.'
        mensaje.modalBody = 'Hay campos con valores no válidos. Por favor, asegúrese de introducirlos correctamente.';
        EventBus.$emit('showMessage', mensaje);
      }
    },
    validateIdUpdate: function() {
      let mensaje = {
        modalTitle: '',
        modalBody: ''
      };
      if(this.Vuelo.Id =='' || this.Vuelo.Id < 0) {
        mensaje.modalTitle = 'Formulario vacío.'
        mensaje.modalBody = 'Seleccione un vuelo de la lista.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        this.edit();
      }
    },
    validateIdDelete: function() {
      let mensaje = {
        modalTitle: '',
        modalBody: ''
      };
      if(this.Vuelo.Id =='' || this.Vuelo.Id < 0) {
        mensaje.modalTitle = 'Formulario vacío.'
        mensaje.modalBody = 'Seleccione un vuelo de la lista.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        this.remove();
      }
    },
    validateUpdate: function() {
      let mensaje = {
        modalTitle: '',
        modalBody: ''
      };
      if (this.validatedAll()) {
        this.update();
      } else {
        mensaje.modalTitle = 'Error en la validación.'
        mensaje.modalBody = 'Hay campos con valores no válidos. Por favor, asegúrese de introducirlos correctamente.';
        EventBus.$emit('showMessage', mensaje);
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

      this.cleanModified();
      this.cleanValidated();
      this.addingNew = true;
    },
    discardNew: function () {
      this.Vuelo.CodigoVuelo = this.VueloCopia.CodigoVuelo;
      this.Vuelo.Companyia = this.VueloCopia.Companyia;
      this.Vuelo.Origen = this.VueloCopia.Origen;
      this.Vuelo.Destino = this.VueloCopia.Destino;
      this.Vuelo.FechaSalida = this.VueloCopia.FechaSalida;
      this.Vuelo.FechaLlegada = this.VueloCopia.FechaLlegada;

      this.cleanCopy();
      this.addingNew = false;
      this.cleanModified();
      this.cleanValidated();
    },
    edit: function () {
      this.VueloCopia.CodigoVuelo = this.Vuelo.CodigoVuelo;
      this.VueloCopia.Companyia = this.Vuelo.Companyia;
      this.VueloCopia.Origen = this.Vuelo.Origen;
      this.VueloCopia.Destino = this.Vuelo.Destino;
      this.VueloCopia.FechaSalida = this.Vuelo.FechaSalida;
      this.VueloCopia.FechaLlegada = this.Vuelo.FechaLlegada;

      this.cleanModified();
      this.editing = true;
    },
    discard: function () {
      this.Vuelo.CodigoVuelo = this.VueloCopia.CodigoVuelo;
      this.Vuelo.Companyia = this.VueloCopia.Companyia;
      this.Vuelo.Origen = this.VueloCopia.Origen;
      this.Vuelo.Destino = this.VueloCopia.Destino;
      this.Vuelo.FechaSalida = this.VueloCopia.FechaSalida;
      this.Vuelo.FechaLlegada = this.VueloCopia.FechaLlegada;

      this.cleanCopy();
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

      this.VueloCopia.Id = '';
      this.VueloCopia.CodigoVuelo = '';
      this.VueloCopia.Companyia = '';
      this.VueloCopia.Origen = '';
      this.VueloCopia.Destino = '';
      this.VueloCopia.FechaSalida = '';
      this.VueloCopia.FechaLlegada = '';
      this.cleanModified();
      this.cleanValidated();
    },
    cleanCopy: function() {
      this.VueloCopia.Id = '';
      this.VueloCopia.CodigoVuelo = '';
      this.VueloCopia.Companyia = '';
      this.VueloCopia.Origen = '';
      this.VueloCopia.Destino = '';
      this.VueloCopia.FechaSalida = '';
      this.VueloCopia.FechaLlegada = '';
    },
    cleanModified: function() {
      this.modified.CodigoVuelo = false;
      this.modified.Companyia = false;
      this.modified.Origen = false;
      this.modified.Destino = false;
      this.modified.FechaSalida = false;
      this.modified.FechaLlegada = false;
    },
    cleanValidated: function() {
      this.validated.CodigoVuelo = false;
      this.validated.Companyia = false;
      this.validated.Origen = false;
      this.validated.Destino = false;
      this.validated.FechaSalida = false;
      this.validated.FechaLlegada = false;
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
          let mensaje = {
            modalTitle: '',
            modalBody: ''
          };
          mensaje.modalTitle = 'Vuelo añadido.'
          mensaje.modalBody = 'Vuelo añadido con éxito.';
          EventBus.$emit('showMessage', mensaje);
          _this.addingNew = false;
          _this.cleanForm();
        })
        .fail(function(data) {
          let mensaje = {
            modalTitle: '',
            modalBody: ''
          };
          mensaje.modalTitle = 'Acción incompleta.'
          mensaje.modalBody = 'No se pudo crear el vuelo. Revise su conexión a Internet.';
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
            let mensaje = {
              modalTitle: '',
              modalBody: ''
            };
            mensaje.modalTitle = 'Vuelo actualizado.'
            mensaje.modalBody ='Vuelo actualizado con éxito.';
            EventBus.$emit('showMessage', mensaje);
            _this.editing = false;
            _this.cleanForm();
          })
          .fail(function(data) {
            let mensaje = {
              modalTitle: '',
              modalBody: ''
            };
            mensaje.modalTitle = 'Acción incompleta.';
            mensaje.modalBody = 'No se pudo actualizar el vuelo. Revise su conexión a Internet.';
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
              let mensaje = {
                modalTitle: '',
                modalBody: ''
              };
              mensaje.modalTitle = 'Vuelo eliminado.';
              mensaje.modalBody ='Vuelo eliminado con éxito.';
              EventBus.$emit('showMessage', mensaje);
              _this.editing = false;
              _this.cleanForm();
            })
            .fail(function(data) {
              let mensaje = {
                modalTitle: '',
                modalBody: ''
              };
              mensaje.modalTitle = 'Acción incompleta';
              mensaje.modalBody = 'No se pudo eliminar el aeropuerto. Revise su conexión a Internet.';
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
                let mensaje = {
                  modalTitle: '',
                  modalBody: ''
                };
                mensaje.modalTitle = 'Acción incompleta.'
                mensaje.modalBody = 'No se pudo cargar los aeropuertos. Revise su conexión a Internet.';
                EventBus.$emit('showMessage', mensaje);
              });
            },
          },
          data: function () {
            return {
              editing: false,
              addingNew: false,
              validated: {
                CodigoVuelo: false,
                Companyia: false,
                Origen: false,
                Destino: false,
                FechaSalida: false,
                FechaLlegada: false
              },
              modified: {
                CodigoVuelo: false,
                Companyia: false,
                Origen: false,
                Destino: false,
                FechaSalida: false,
                FechaLlegada: false
              },
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
