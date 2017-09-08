<template>
  <div class="w3-container w3-card-4" style="min-width:300px; max-width:300px; display:inline-block;">
    <transition name="slide-fade">
      <p style="text-align: center; vertical-align: middle; line-height: 30px" v-if="show"> {{mensaje}}</p>
    </transition>
  </div>
</template>

<script>
import EventBus from './event-bus.js'

export default {
  data: function() {
    return {
      show: false,
      mensaje: ''
    }
  },

  methods: {
    showMessage: function(message){
      this.mensaje = message;
      this.show = true;
    },
    hideMessage: function(){
      this.show = false;
    }
  },

  mounted: function() {
    EventBus.$on('showMessage', function(message) {
      this.showMessage(message);
      setTimeout(this.hideMessage, 1500)
    }.bind(this));
  }

}

</script>

<style>

.slide-fade-enter-active {
  transition: all .3s ease;
}
.slide-fade-leave-active {
  transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-fade-enter, .slide-fade-leave-to
{
  transform: translateX(10px);
  opacity: 0;
}
</style>
