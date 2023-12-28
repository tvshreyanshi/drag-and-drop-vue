<template>
    <div v-if="isOpenDeleteModal" class="modal-mask">
      <div class="modal-wrapper">
        <div class="modal-container rounded-3" ref="target">
          <div class="modal-body">
            <slot name="content">{{ deleteMessage }}</slot>
          </div>
          <div class="modal-footer">
            <button class="btn" @click="$emit('confirm-delete')">Confirm</button>
            <button class="btn" @click="$emit('modal-close')">Close</button>
          </div>
        </div>
      </div>
    </div>
  </template>
  <script setup>
  import { defineProps, defineEmits, ref } from "vue";
  import {onClickOutside} from '@vueuse/core'
  
  const props = defineProps({
    isOpenDeleteModal: {
      type: Boolean,
      default: false,
    },
    deleteMessage: {
        type: String,
    }
  });
  const emit = defineEmits(["modal-close"], ["confirm-delete"]);
  
  const target = ref(null);
  
  onClickOutside(target, ()=> {
    // console.log('isopen', props.isOpen);
    if (props.isOpenDeleteModal) {
      emit('modal-close');
    }
  
  });
  </script>
  
  <style scoped>
  .modal-mask {
    position: fixed;
    z-index: 9998;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
  }
  .modal-container {
    width: 300px;
    margin: 150px auto;
    padding: 20px 30px;
    background-color: #fff;
    border-radius: 2px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  }
  
  </style>
  