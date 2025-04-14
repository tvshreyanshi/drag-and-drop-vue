<template>
  <li
    class="tvd__card__item"
    :draggable="true"
    @dragstart="handleDragStart"
    @dragover.prevent="handleDragOver"
    @dragleave="handleDragLeave"
    @drop.prevent="handleDrop"
  >
    <!-- Visual drag indicator that appears during drag -->
    <div class="tvd__card__drag-handle" title="Drag to move">
      <i class="fa-solid fa-grip-lines"></i>
    </div>
    
    <div class="tvd__card__field">
      <!-- Card title -->
      <span
        v-if="card && card.title"
        v-html="card.title"
        class="tvd__card__tag"
        @click="$emit('open-edit-modal', false)"
      ></span>
      
      <!-- Card actions -->
      <div>
        <button
          @click="$emit('open-edit-modal', true)"
          class="tvd__edit_card"
        >
          <i class="fa-solid fa-pen-to-square tvd__icons"></i>
        </button>
        <button
          @click="$emit('open-delete-modal')"
          class="tvd__delete_card"
        >
          <i class="tvd__icons fa-solid fa-trash-can"></i>
        </button>
      </div>
    </div>
    
    <!-- Card deadline -->
    <div class="tvd__card__field" v-if="card && card.deadlineDate">
      <span>
        <i class="fa-regular fa-calendar-alt"></i>
        {{ card.deadlineDate }}
      </span>
    </div>
    
    <!-- Card description -->
    <div class="tvd__card__field" v-if="card && card.description">
      <span class="tvd__description" v-html="card.description"></span>
    </div>
    
    <!-- Card footer -->
    <div class="tvd__card__actions">
      <li class="tvd__card__actions--wrapper">
        <div class="tvd__card__avatars">
          <li class="tvd__card__avatars--item">
            <!-- Avatar could be added here if needed -->
          </li>
        </div>
      </li>
    </div>
  </li>
</template>

<script setup>
import { defineProps, defineEmits } from "vue";

const props = defineProps({
  card: {
    type: Object,
    required: true
  },
  cardIndex: {
    type: Number,
    required: true
  },
  sectionIndex: {
    type: Number,
    required: true
  }
});

const emit = defineEmits([
  'drag-start',
  'drag-over',
  'drag-leave',
  'internal-drop',
  'open-edit-modal',
  'open-delete-modal'
]);

const handleDragStart = (event) => {
  // Set data transfer for drag operation
  if (event.dataTransfer) {
    event.dataTransfer.effectAllowed = 'move';
    // Store card data in data transfer
    event.dataTransfer.setData('text/plain', JSON.stringify({
      cardId: props.card.title,
      sectionIndex: props.sectionIndex,
      cardIndex: props.cardIndex
    }));
  }
  emit('drag-start', event);
};

const handleDragOver = (event) => {
  event.preventDefault();
  // Add visual cue when dragging over this card
  event.currentTarget.classList.add('card-drag-over');
  emit('drag-over', event);
};

const handleDragLeave = (event) => {
  // Remove visual cue when dragging moves out
  event.currentTarget.classList.remove('card-drag-over');
  emit('drag-leave', event);
};

const handleDrop = (event) => {
  event.preventDefault();
  // Remove the visual cue
  event.currentTarget.classList.remove('card-drag-over');
  emit('internal-drop', event);
};
</script>

<style>
.tvd__card__item {
  position: relative;
  transition: all 0.2s ease;
}

.tvd__card__drag-handle {
  position: absolute;
  top: 8px;
  left: 8px;
  opacity: 0.4;
  cursor: grab;
  font-size: 12px;
}

.tvd__card__item:hover .tvd__card__drag-handle {
  opacity: 1;
}

.tvd__card__item.dragging {
  box-shadow: 0 5px 15px rgba(0,0,0,0.15);
  transform: rotate(2deg) scale(1.02);
}

.tvd__card__item.card-drag-over {
  box-shadow: 0 0 10px rgba(0,0,0,0.2);
  transform: translateY(5px);
  border: 2px dashed #4a9eff;
  padding-top: 10px;
  padding-bottom: 10px;
}
</style>