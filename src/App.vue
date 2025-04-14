<template>
  <div class="app-container">
    <h1 class="app-title">Kanban Board</h1>
    <SimpleDrag
      :response-data="response"
      :is-custom-edit="false"
      add-card-title="+Add Card"
      add-section-title="Add Section"
      @add-card="addNewCard"
      @edit-card="editData"
      @delete-card="deleteCard"
    />
  </div>
</template>

<script setup>
import { ref } from "vue";
import SimpleDrag from "./components/SimpleDrag.vue";

const response = ref({
  data: [
    {
      title: "Section 1",
      drop_col_id: "",
      data: [
        { 
          title: 'Card 1',
          description: "<p>It is a long established fact that a reader will be distracted by the readable content of a page when looking at its layout.</p>",
          attachment: null,
          deadlineDate: new Date(new Date().getTime() + 2 * 24 * 60 * 60 * 1000).toLocaleDateString('en-US', { month: 'short', day: '2-digit', year: 'numeric' }), 
        },
        { 
          title: 'Card 2',
          description: "<p>It is a long established fact that a reader will be distracted by the readable content of a page when looking at its layout.</p>",
          attachment: null,
          deadlineDate: new Date(new Date().getTime() + 2 * 24 * 60 * 60 * 1000).toLocaleDateString('en-US', { month: 'short', day: '2-digit', year: 'numeric' }),
        },
        { 
          title: "Card 3",
          description: "<p>It is a long established fact that a reader will be distracted by the readable content of a page when looking at its layout.</p>",
          attachment: null,
          deadlineDate: new Date(new Date().getTime() + 2 * 24 * 60 * 60 * 1000).toLocaleDateString('en-US', { month: 'short', day: '2-digit', year: 'numeric' }),
        },
      ],
    },
    {
      title: "Section 2",
      data: [
        { 
          title: "Card 4", 
          description: "<p>It is a long established fact that a reader will be distracted by the readable content of a page when looking at its layout.</p>",
          attachment: null,
          deadlineDate: new Date(new Date().getTime() + 2 * 24 * 60 * 60 * 1000).toLocaleDateString('en-US', { month: 'short', day: '2-digit', year: 'numeric' }),
        }
      ],
    },
    {
      title: "Section 3",
      data: [],
    },
  ],
});

const addNewCard = (event) => {
  if (event && event.index !== undefined) {
    console.log("Added new card", event);
    if (response.value.data[event.index]) {
      // Add the new card to the correct section
      response.value.data[event.index].data.push(event.value);
    }
  }
};

const editData = (event) => {
  if (!event) return;
  
  const arrayIndex = event.arrayindex;
  const itemIndex = event.itemindex;
  
  if (response.value.data[arrayIndex] && response.value.data[arrayIndex].data[itemIndex]) {
    response.value.data[arrayIndex].data[itemIndex].title = event.title;
    response.value.data[arrayIndex].data[itemIndex].description = event.description;
    response.value.data[arrayIndex].data[itemIndex].deadlineDate = event.deadlineDate;
    response.value.data[arrayIndex].data[itemIndex].attachment = event.attachment;
  }
};

const deleteCard = (event) => {
  if (!event) return;
  
  const arrayIndex = event.arrayindex;
  const itemIndex = event.itemindex;
  
  if (response.value.data[arrayIndex] && response.value.data[arrayIndex].data) {
    response.value.data[arrayIndex].data.splice(itemIndex, 1);
  }
};
</script>

<style>
.app-container {
  padding: 20px;
  max-width: 100%;
  overflow-x: hidden;
}

.app-title {
  text-align: center;
  margin-bottom: 20px;
  color: #333;
  font-size: 28px;
}
</style>