<template>
  <li class="tvd__column__item" 
      :class="{ 'section-drag-over': isDragOver }"
      @dragover.prevent="handleDragOver"
      @dragleave="handleDragLeave"
      @drop.prevent="handleDrop">
    <!-- Section header -->
    <SectionHeader 
      :title="section.title" 
      @show-add-card="$emit('show-add-card', sectionIndex)" 
    />
    
    <!-- Cards container -->
    <ul
      class="tvd__card__list"
      @dragover.prevent="handleDragOver"
      @dragleave="handleDragLeave"
      @drop.prevent="handleDrop"
    >
      <!-- Card items -->
      <BoardCard
        v-for="(item, itemIndex) in section.data"
        :key="itemIndex"
        :card="item"
        :card-index="itemIndex"
        :section-index="sectionIndex"
        @drag-start="onDragStart($event, sectionIndex, itemIndex)"
        @drag-over="onDragOver($event, sectionIndex, itemIndex)"
        @drag-leave="onDragLeave"
        @internal-drop="onInternalDrop($event, sectionIndex, itemIndex)"
        @open-edit-modal="$emit('open-edit-modal', item, sectionIndex, itemIndex, $event)"
        @open-delete-modal="$emit('open-delete-modal', sectionIndex, itemIndex)"
        @add-comment="$emit('add-comment', $event)"
        @add-tag="$emit('add-tag', $event)"
        @remove-tag="$emit('remove-tag', $event)"
        @add-attachment="$emit('add-attachment', $event)"
        @remove-attachment="$emit('remove-attachment', $event)"
      />
      
      <EmptyState v-if="section.data.length === 0" />
      
      <!-- Add card form -->
      <NewCardForm
        v-if="isAddingCard"
        :section-index="sectionIndex"
        :add-card-title="addCardTitle"
        @close-card="$emit('close-card')"
        @add-card="$emit('add-card', $event[0], $event[1])"
      />
    </ul>
    
    <!-- Add card button -->
    <button class="tvd__add-btn" @click.prevent="$emit('show-add-card', sectionIndex)">
      <h6>{{ addCardTitle }}</h6>
    </button>
  </li>
</template>

<script setup>
import { ref, defineProps, defineEmits } from "vue";
import BoardCard from "./BoardCard.vue";
import SectionHeader from "./section/SectionHeader.vue";
import EmptyState from "./section/EmptyState.vue";
import NewCardForm from "./section/NewCardForm.vue";

const props = defineProps({
  section: {
    type: Object,
    required: true
  },
  sectionIndex: {
    type: Number,
    required: true
  },
  addCardTitle: {
    type: String,
    required: true
  },
  isAddingCard: {
    type: Boolean,
    default: false
  }
});

const emit = defineEmits([
  'show-add-card',
  'close-card',
  'add-card',
  'drop',
  'drag-start',
  'drag-over',
  'drag-leave',
  'internal-drop',
  'open-edit-modal',
  'open-delete-modal',
  'add-comment',
  'add-tag',
  'remove-tag',
  'add-attachment',
  'remove-attachment'
]);

const isDragOver = ref(false);

const onDragStart = (event, sectionIndex, itemIndex) => {
  emit('drag-start', sectionIndex, itemIndex, event);
};

const handleDragOver = (event) => {
  event.preventDefault();
  isDragOver.value = true;
  // Find position in list
  emit('drag-over', props.sectionIndex, null, event);
};

const onDragOver = (event, sectionIndex, itemIndex) => {
  emit('drag-over', sectionIndex, itemIndex, event);
};

const handleDragLeave = (event) => {
  // Check if we're actually leaving the section or just entering a child
  if (!event.currentTarget.contains(event.relatedTarget)) {
    isDragOver.value = false;
    emit('drag-leave', event);
  }
};

const onDragLeave = (event) => {
  emit('drag-leave', event);
};

const handleDrop = (event) => {
  event.preventDefault();
  isDragOver.value = false;
  emit('drop', props.sectionIndex, event);
};

const onInternalDrop = (event, sectionIndex, itemIndex) => {
  emit('internal-drop', sectionIndex, itemIndex);
};

</script>

<style>
.tvd__column__item {
  position: relative;
  transition: all 0.2s ease;
}

.section-drag-over {
  background-color: #e9efff;
  box-shadow: 0 0 10px rgba(0,0,0,0.1);
}

.tvd__card__list {
  flex-grow: 1;
  overflow-y: auto;
  padding: 0;
  margin: 0;
  list-style: none;
}
</style>