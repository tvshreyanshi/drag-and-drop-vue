<template>
  <li class="tvd__column__item" 
      :class="{ 'section-drag-over': isDragOver }"
      @dragover.prevent="handleDragOver"
      @dragleave="handleDragLeave"
      @drop.prevent="handleDrop">
    <!-- Section header -->
    <div class="tvd__column__title--wrapper">
      <h2>{{ section.title }}</h2>
      <i
        class="tvd__column__item--cta fa-solid fa-plus"
        @click.prevent="$emit('show-add-card', sectionIndex)"
      ></i>
    </div>
    
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
      />
      
      <!-- Empty state placeholder -->
      <li v-if="section.data.length === 0" class="tvd__card__empty">
        <p>Drop cards here</p>
      </li>
      
      <!-- Add card form -->
      <li class="tvd__card__item" v-if="isAddingCard">
        <button class="tvd_close_addcard" @click="$emit('close-card')">
          <i class="tvd__icons fa-solid fa-xmark"></i>
        </button>
        <slot name="cardForm">
          <div>
            <input
              v-model="newCard.title"
              type="text"
              placeholder="Card Title"
              class="tvd__add-input"
            />
            <textarea
              v-model="newCard.description"
              placeholder="Card Description"
              class="tvd__add-textarea"
            ></textarea>
            <button class="tvd__add-btn" @click.prevent="addCard">
              <h6>{{ addCardTitle }}</h6>
            </button>
          </div>
        </slot>
      </li>
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
  'open-delete-modal'
]);

const newCard = ref({
  title: "",
  description: "",
});

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

const addCard = () => {
  if (newCard.value.title.trim() !== "") {
    const cardData = {
      title: newCard.value.title,
      description: `<p>${newCard.value.description}</p>`,
      attachment: null,
      deadlineDate: new Date(new Date().getTime() + 2 * 24 * 60 * 60 * 1000)
        .toLocaleDateString('en-US', { month: 'short', day: '2-digit', year: 'numeric' })
    };
    
    emit("add-card", props.sectionIndex, cardData);
    
    // Reset form
    newCard.value.title = "";
    newCard.value.description = "";
    emit("close-card");
  }
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

.tvd__card__empty {
  border: 2px dashed #ccc;
  border-radius: 6px;
  padding: 20px;
  margin-bottom: 8px;
  text-align: center;
  color: #999;
  min-height: 80px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.tvd__card__empty p {
  margin: 0;
}
</style>