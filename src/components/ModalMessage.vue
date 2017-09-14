<template>
    <div v-if="showModal">
      <transition name="modal">
        <div class="modal-mask">
          <div class="modal-wrapper">
            <div class="modal-dialog">
              <div class="modal-content">
                <div class="modal-header">
                  <button type="button" class="close" @click="showModal=false">
                    <span aria-hidden="true">&times;</span>
                  </button>
                  <h3 class="teal-text modal-title"><strong>{{this.modalTitle}}</strong></h3>
                </div>
                <div class="teal-text modal-body">
                  <h4>{{this.modalBody}}</h4>
                </div>
              </div>
            </div>
          </div>
        </div>
      </transition>
   </div>
</template>
<script>
import EventBus from './event-bus.js'

export default {
  data: function () {
    return {
      modalTitle: '',
      modalBody: '',
      showModal: false,
    }
  },
  mounted: function() {
    EventBus.$on('showMessage', function(message) {
      this.modalTitle = message.modalTitle;
      this.modalBody = message.modalBody;
      this.showModal = true;
    }.bind(this));
  }
}
</script>
<style>
.modal-mask {
    position: fixed;
    z-index: 9998;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, .5);
    display: table;
    transition: opacity .3s ease;
  }

.modal-wrapper {
    display: table-cell;
    vertical-align: middle;
  }
</style>
