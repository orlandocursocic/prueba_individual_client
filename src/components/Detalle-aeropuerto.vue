<template>
  <div class="w3-container w3-card-4" style="min-width:500px; display:inline-block; vertical-align:top">
    <div>
      <h3 style="overflow: hidden; text-overflow: ellipsis; max-width:300px"><strong>Aeropuerto: </strong>{{Aeropuerto.Nombre}}</h3>
      <div :class="nombreValidationClasses">
        <label class="control-label" for="nombre"> Nombre </label>
        <textarea class="form-control" rows="1" style="resize: none; overflow: auto; text-overflow: ellipsis"
        name="nombre" value="Nombre" :disabled="!editing && !addingNew" v-model="Aeropuerto.Nombre"></textarea>
        <p v-if="nombreError != ''" class="help-block"> {{nombreError}} </p>
      </div>
      <div :class="numTermValidationClasses">
        <label class="control-label" for="desc"> Número de Terminales </label>
        <textarea class="form-control" rows="1" style="resize: none; overflow: auto; text-overflow: ellipsis"
        name="desc" value="numTerminales" :disabled="!editing && !addingNew" v-model="Aeropuerto.numTerminales"></textarea>
        <p v-if="numTermError != ''" class="help-block"> {{numTermError}} </p>
      </div>
      <div :class="ciudadValidationClasses">
        <label class="control-label" for="desc"> Ciudad </label>
        <textarea class="form-control" rows="1" style="resize: none; overflow: auto; text-overflow: ellipsis"
        name="desc" value="Ciudad" :disabled="!editing && !addingNew" v-model="Aeropuerto.Ciudad"></textarea>
        <p v-if="ciudadError != ''" class="help-block"> {{ciudadError}} </p>
      </div>
      <div :class="paisValidationClasses">
        <label class="control-label" for="desc"> País </label>
        <textarea class="form-control" rows="1" style="resize: none; overflow: auto; text-overflow: ellipsis"
        name="desc" value="Pais" :disabled="!editing && !addingNew" v-model="Aeropuerto.Pais"></textarea>
        <p v-if="paisError != ''" class="help-block"> {{paisError}} </p>
      </div>
    </div>

    <div>
      <template v-if="!addingNew">
        <div style="float: left">
          <button type="button" class="btn btn-default btn-sm" title="Nuevo" @click="editNew" :disabled="editing">
            <app-icon img="plus"></app-icon> Nuevo
          </button>
        </div>
      </template>

      <template v-else>
        <div style="float: left">
          <button type="button" class="btn btn-default btn-sm" title="Confirmar" @click="validateNew" :disabled="!allModified">
            <app-icon img="ok"></app-icon> Confirmar
          </button>
          <button type="button" class="btn btn-default btn-sm" title="Descartar" @click="discardNew">
            <app-icon img="remove"></app-icon> Cancelar
          </button>
        </div>
      </template>

      <template v-if="!editing">
        <div style="float: right">
          <button type="button" class="btn btn-default btn-sm" title="Editar" @click="validateIdUpdate" :disabled="addingNew">
            <app-icon img="edit"></app-icon> Editar
          </button>
          <button type="button" class="btn btn-default btn-sm" title="Eliminar" @click="validateIdDelete" :disabled="addingNew">
            <app-icon img="trash"></app-icon> Eliminar
          </button>
        </div>
      </template>

      <template v-else>
        <div style="float: right">
          <button type="button" class="btn btn-default btn-sm" title="Confirmar" @click="validateUpdate" :disabled="!anyModified">
            <app-icon img="ok"></app-icon> Confirmar
          </button>
          <button type="button" class="btn btn-default btn-sm" title="Descartar" @click="discard">
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

var httpURL = appConfig.URLAeropuerto;
var maxInt =  2147483647;

export default {
  components: {
    'app-icon' : AppIcon,
    'infomessage' : InfoMessage
  },
  computed: {
    anyModified: function() {
      if (this.modified.Nombre || this.modified.numTerminales || this.modified.Ciudad || this.modified.Pais) {
        return true;
      }
      return false;
    },
    allModified: function() {
      if (this.modified.Nombre && this.modified.numTerminales && this.modified.Ciudad && this.modified.Pais) {
        return true;
      }
      return false;
    },
    nombreError: function(){
      var val = this.Aeropuerto.Nombre.trim();

      if(this.Aeropuerto.Nombre != '' && this.Aeropuerto.Nombre != this.AeropuertoCopia.Nombre){
        this.modified.Nombre = true;
      }

      if(this.modified.Nombre) {
        if (val == ''){
          this.validated.Nombre = false;
          return 'El campo es obligatorio'
        }
        this.validated.Nombre = true;
      }
      return '';
    },
    nombreValidationClasses: function(){
      return [
        'form-group',
        {'has-error': this.nombreError}
      ];
    },
    numTermError: function(){
      var val = this.Aeropuerto.numTerminales;

      if(this.Aeropuerto.numTerminales != '' && this.Aeropuerto.numTerminales != this.AeropuertoCopia.numTerminales){
        this.modified.numTerminales = true;
      }

      if(this.modified.numTerminales) {
        if (val == ''){
          this.validated.numTerminales = false;
          return 'El campo es obligatorio'
        }

        if(!this.isInt(val)) {
          this.validated.numTerminales = false;
          return 'El campo debe ser un número entero'
        }

        if(val < 1) {
          this.validated.numTerminales = false;
          return 'El campo debe ser mayor que 1.'
        }
        this.validated.numTerminales = true;
      }
      return '';
    },
    numTermValidationClasses: function(){
      return [
        'form-group',
        {'has-error': this.numTermError}
      ];
    },
    ciudadError: function(){
      var val = this.Aeropuerto.Ciudad.trim();

      if(this.Aeropuerto.Ciudad != '' && this.Aeropuerto.Ciudad != this.AeropuertoCopia.Ciudad){
        this.modified.Ciudad = true;
      }

      if(this.modified.Ciudad) {
        if (val == ''){
          this.validated.Ciudad = false;
          return 'El campo es obligatorio'
        }
        this.validated.Ciudad = true;
      }
      return '';
    },
    ciudadValidationClasses: function(){
      return [
        'form-group',
        {'has-error': this.ciudadError}
      ];
    },
    paisError: function(){
      var val = this.Aeropuerto.Pais.trim();

      if(this.Aeropuerto.Pais != '' && this.Aeropuerto.Pais != this.AeropuertoCopia.Pais){
        this.modified.Pais = true;
      }

      if(this.modified.Pais) {
        if (val == ''){
          this.validated.Pais = false;
          return 'El campo es obligatorio'
        }
        this.validated.Pais = true;
      }
      return '';
    },
    paisValidationClasses: function(){
      return [
        'form-group',
        {'has-error': this.paisError}
      ];
    },
  },
  methods: {
    validatedAll: function() {
      if (this.validated.Nombre && this.validated.numTerminales && this.validated.Ciudad && this.validated.Pais) {
        return true;
      }
      return false;
    },
    validateNew: function() {
      let mensaje = '';
      if (this.validatedAll()) {
        this.create();
      } else {
        mensaje = 'Hay campos con valores inválidos. Por favor, introdúzcalos correctamente';
        EventBus.$emit('showMessage', mensaje);
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
      if (this.validatedAll()) {
        this.update();
      } else {
        mensaje = 'Hay campos con valores inválidos. Por favor, introdúzcalos correctamente';
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
      this.AeropuertoCopia.Nombre = this.Aeropuerto.Nombre;
      this.AeropuertoCopia.numTerminales = this.Aeropuerto.numTerminales;
      this.AeropuertoCopia.Ciudad = this.Aeropuerto.Ciudad;
      this.AeropuertoCopia.Pais = this.Aeropuerto.Pais;

      this.Aeropuerto.Nombre = '';
      this.Aeropuerto.numTerminales = '';
      this.Aeropuerto.Ciudad = '';
      this.Aeropuerto.Pais = '';

      this.cleanModified();
      this.cleanValidated();
      this.addingNew = true;
    },
    discardNew: function () {
      this.Aeropuerto.Nombre = this.AeropuertoCopia.Nombre;
      this.Aeropuerto.numTerminales = this.AeropuertoCopia.numTerminales;
      this.Aeropuerto.Ciudad = this.AeropuertoCopia.Ciudad;
      this.Aeropuerto.Pais = this.AeropuertoCopia.Pais;

      this.cleanCopy();
      this.addingNew = false;
      this.cleanModified();
      this.cleanValidated();
    },
    edit: function () {
      this.AeropuertoCopia.Nombre = this.Aeropuerto.Nombre;
      this.AeropuertoCopia.numTerminales = this.Aeropuerto.numTerminales;
      this.AeropuertoCopia.Ciudad = this.Aeropuerto.Ciudad;
      this.AeropuertoCopia.Pais = this.Aeropuerto.Pais;

      this.cleanModified();
      this.editing = true;
    },
    discard: function () {
      this.Aeropuerto.Nombre = this.AeropuertoCopia.Nombre;
      this.Aeropuerto.numTerminales = this.AeropuertoCopia.numTerminales;
      this.Aeropuerto.Ciudad = this.AeropuertoCopia.Ciudad;
      this.Aeropuerto.Pais = this.AeropuertoCopia.Pais;

      this.cleanCopy();
      this.editing = false;
    },
    cleanForm: function() {
      this.Aeropuerto.Id = '';
      this.Aeropuerto.Nombre = '';
      this.Aeropuerto.numTerminales = '';
      this.Aeropuerto.Ciudad = '';
      this.Aeropuerto.Pais = '';

      this.AeropuertoCopia.Id = '';
      this.AeropuertoCopia.Nombre = '';
      this.AeropuertoCopia.numTerminales = '';
      this.AeropuertoCopia.Ciudad = '';
      this.AeropuertoCopia.Pais = '';
      this.cleanModified();
      this.cleanValidated();
    },
    cleanCopy: function() {
      this.AeropuertoCopia.Id = '';
      this.AeropuertoCopia.Nombre = '';
      this.AeropuertoCopia.numTerminales = '';
      this.AeropuertoCopia.Ciudad = '';
      this.AeropuertoCopia.Pais = '';
    },
    cleanModified: function() {
      this.modified.Nombre = false;
      this.modified.numTerminales = false;
      this.modified.Ciudad = false;
      this.modified.Pais = false;
    },
    cleanValidated: function() {
      this.validated.Nombre = false;
      this.validated.numTerminales = false;
      this.validated.Ciudad = false;
      this.validated.Pais = false;
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
          _this.cleanForm();
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
            _this.editing = false;
            _this.cleanForm();
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
              _this.cleanForm();
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
              validated: {
                Nombre: false,
                numTerminales: false,
                Ciudad: false,
                Pais: false
              },
              anyModification: false,
              modified: {
                Nombre: false,
                numTerminales: false,
                Ciudad: false,
                Pais: false
              },
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
