<template>
  <div class="app-container" :class="{ 'dark-mode': isDarkMode }">
    <div class="app-header">
      <h1 class="app-title">Kanban Board</h1>
      <div class="app-controls">
        <!-- Search & Filter -->
        <div class="search-filter">
          <input 
            type="text" 
            v-model="searchQuery" 
            placeholder="Search cards..." 
            class="search-input"
          />
          <select v-model="priorityFilter" class="filter-select">
            <option value="">All Priorities</option>
            <option value="urgent">Urgent</option>
            <option value="high">High</option>
            <option value="medium">Medium</option>
            <option value="low">Low</option>
          </select>
        </div>
        <!-- Dark Mode Toggle -->
        <button @click="toggleDarkMode" class="theme-toggle">
          <i :class="isDarkMode ? 'fa-solid fa-sun' : 'fa-solid fa-moon'"></i>
        </button>
      </div>
    </div>
    
    <SimpleDrag
      :response-data="filteredData"
      :is-custom-edit="false"
      add-card-title="+Add Card"
      add-section-title="Add Section"
      @add-card="addNewCard"
      @edit-card="editData"
      @delete-card="deleteCard"
      @add-comment="addComment"
      @add-tag="addTag"
      @remove-tag="removeTag"
    />
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import SimpleDrag from "./components/SimpleDrag.vue";

// Initialize dark mode from localStorage if available
const isDarkMode = ref(false);

onMounted(() => {
  const savedMode = localStorage.getItem('darkMode');
  isDarkMode.value = savedMode === 'true';
});

// Toggle dark mode function
const toggleDarkMode = () => {
  isDarkMode.value = !isDarkMode.value;
  localStorage.setItem('darkMode', isDarkMode.value);
};

// Search and filter
const searchQuery = ref('');
const priorityFilter = ref('');

// Board data

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
          deadldeadlineDate: new Date(new Date().getTime() + 2 * 24 * 60 * 60 * 1000).toLocaleDateString('en-US', { month: 'short', day: '2-digit', year: 'numeric' }),
          priority: "high",
          comments: [
            { text: "This needs to be completed ASAP", author: "User", timestamp: new Date().toISOString() }
          ],
          tags: ["frontend", "bug"]
        },
        { 
          title: 'Card 2',
          description: "<p>It is a long established fact that a reader will be distracted by the readable content of a page when looking at its layout.</p>",
          attachment: null,
          deadlineDate: new Date(new Date().getTime() + 2 * 24 * 60 * 60 * 1000).toLocaleDateString('en-US', { month: 'short', day: '2-digit', year: 'numeric' }),
          priority: "medium",
          comments: [],
          tags: ["backend"]
        },
        { 
          title: "Card 3",
          description: "<p>It is a long established fact that a reader will be distracted by the readable content of a page when looking at its layout.</p>",
          attachment: null,
          deadlineDate: new Date(new Date().getTime() + 2 * 24 * 60 * 60 * 1000).toLocaleDateString('en-US', { month: 'short', day: '2-digit', year: 'numeric' }),
          priority: "low",
          comments: [],
          tags: ["design", "frontend"]
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
          priority: "urgent",
          comments: [],
          tags: ["research"]
        }
      ],
    },
    {
      title: "Section 3",
      data: [],
    },
  ],
});
// Filter data based on search and filter selections
const filteredData = computed(() => {
  return {
    data: response.value.data.map(section => {
      const filteredCards = section.data.filter(card => {
        const matchesSearch = !searchQuery.value || 
          card.title.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
          card.description.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
          (card.tags && card.tags.some(tag => tag.toLowerCase().includes(searchQuery.value.toLowerCase())));
        const matchesPriority = !priorityFilter.value || card.priority === priorityFilter.value;
        return matchesSearch && matchesPriority;
      });

      // 🚨 Important: Keep reference to original array if no filtering
      return {
        ...section,
        data: filteredCards.length === section.data.length ? section.data : filteredCards
      };
    })
  };
});

// Card management functions
const addNewCard = (event) => {
  if (event && event.index !== undefined) {
    console.log("Added new card", event);
    if (response.value.data[event.index]) {
      // Add the new card to the correct section
      const newCard = {
        ...event.value,
        comments: [],
        tags: []
      };
      response.value.data[event.index].data.push(newCard);
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
    response.value.data[arrayIndex].data[itemIndex].priority = event.priority;

    // Make sure we preserve comments and tags if not included in the event
    if (event.comments) {
      response.value.data[arrayIndex].data[itemIndex].comments = event.comments;
    }
    if (event.tags) {
      response.value.data[arrayIndex].data[itemIndex].tags = event.tags;
    }
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
// Comment management
const addComment = (event) => {
  if (!event) return;
  
  const { arrayIndex, itemIndex, comment } = event;
  
  if (response.value.data[arrayIndex] && response.value.data[arrayIndex].data[itemIndex]) {
    if (!response.value.data[arrayIndex].data[itemIndex].comments) {
      response.value.data[arrayIndex].data[itemIndex].comments = [];
    }
    
    const newComment = {
      text: comment,
      author: "User", // In a real app, this would come from authentication
      timestamp: new Date().toISOString()
    };
    
    response.value.data[arrayIndex].data[itemIndex].comments.push(newComment);
  }
};

// Tag management
const addTag = (event) => {
  if (!event) return;
  
  const { arrayIndex, itemIndex, tag } = event;
  
  if (response.value.data[arrayIndex] && response.value.data[arrayIndex].data[itemIndex]) {
    if (!response.value.data[arrayIndex].data[itemIndex].tags) {
      response.value.data[arrayIndex].data[itemIndex].tags = [];
    }
    
    // Only add if the tag doesn't already exist
    if (!response.value.data[arrayIndex].data[itemIndex].tags.includes(tag)) {
      response.value.data[arrayIndex].data[itemIndex].tags.push(tag);
    }
  }
};

const removeTag = (event) => {
  if (!event) return;
  
  const { arrayIndex, itemIndex, tag } = event;
  
  if (response.value.data[arrayIndex] && 
      response.value.data[arrayIndex].data[itemIndex] && 
      response.value.data[arrayIndex].data[itemIndex].tags) {
    
    response.value.data[arrayIndex].data[itemIndex].tags = 
      response.value.data[arrayIndex].data[itemIndex].tags.filter(t => t !== tag);
  }
};
</script>

<style>
.app-container {
  padding: 20px;
  max-width: 100%;
  overflow-x: hidden;
  transition: background-color 0.3s, color 0.3s;
}

.app-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.app-title {
  text-align: center;
  color: #333;
  font-size: 28px;
  margin: 0;
}

.app-controls {
  display: flex;
  align-items: center;
  gap: 15px;
}

.search-filter {
  display: flex;
  gap: 10px;
}

.search-input, .filter-select {
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 14px;
}

.theme-toggle {
  background: none;
  border: none;
  font-size: 20px;
  cursor: pointer;
  color: #333;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background-color 0.3s;
}

.theme-toggle:hover {
  background-color: rgba(0, 0, 0, 0.1);
}

/* Dark mode styles */
.dark-mode {
  background-color: #222;
  color: #eee;
}

.dark-mode .app-title {
  color: #eee;
}

.dark-mode .theme-toggle {
  color: #eee;
}

.dark-mode .theme-toggle:hover {
  background-color: rgba(255, 255, 255, 0.1);
}

.dark-mode .search-input, 
.dark-mode .filter-select {
  background-color: #333;
  border-color: #444;
  color: #eee;
}

.dark-mode .tvd__column__item {
  background-color: #333;
  color: #eee;
  border-color: #444;
}

.dark-mode .tvd__card__item {
  background-color: #444;
  color: #eee;
  border-color: #555;
}

.dark-mode .tvd__add-btn {
  background-color: #555;
  color: #eee;
}

.dark-mode .tvd__toast {
  background-color: #555;
  color: #eee;
}

@media (max-width: 768px) {
  .app-header {
    flex-direction: column;
    gap: 15px;
  }
  
  .app-controls {
    width: 100%;
    flex-wrap: wrap;
  }
  
  .search-filter {
    flex-grow: 1;
    flex-wrap: wrap;
  }
  
  .search-input, .filter-select {
    flex-grow: 1;
  }
}
</style>