<template>
  <section class="wrapper p-5">
    <ul class="column__list h-100">
      <li
        class="column__item h-100"
        v-for="(data, index) in response.data"
        :key="index"
      >
        <div class="column__title--wrapper">
          <h2 class="w-100">{{ data.title }}</h2>
          <font-awesome-icon
            :icon="['fas', 'plus']"
            class="column__item--cta"
          />
          <font-awesome-icon :icon="['fas', 'ellipsis']" class="icons" />
        </div>
        <ul
          class="card__list"
          @drop.prevent="dropItem(index, $event)"
          @dragover.prevent="allowDrop"
        >
          <li
            class="card__item"
            v-for="(item, itemindex) in data.data"
            :key="itemindex"
            :draggable="true"
            @dragstart="dragStart(index, itemindex)"
            @dragover.prevent="internalAllowDrop"
            @drop.prevent="internalDrop(index, itemindex)"
          >
            <div>
              <span
                v-if="item && item.html"
                v-html="item.html"
                class="card__tag"
              ></span>
              <button @click="openModal(item)" class="float-end border-0">
                <font-awesome-icon
                  class="icons float-end"
                  :icon="['fas', 'pen-to-square']"
                />
              </button>
            </div>
            <span v-if="item && item.html" v-html="item.html"></span>
            <div class="card__actions">
              <li class="card__actions--wrapper">
                <font-awesome-icon
                  class="icons"
                  :icon="['fas', 'align-left']"
                />
                <font-awesome-icon :icon="['far', 'comment']" class="icons" />
                <div class="card__avatars">
                  <li class="card__avatars--item">
                    <img
                      src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRuXwdzjy8m5Awd5zZ6GknOv2dVo0xYeHEvJu3JGU3SlXEH7y1WV4GIpgSlAmQIN4a0qBI&usqp=CAU"
                      alt="Man standing near balcony"
                      class="avatar__image"
                    />
                  </li>
                </div>
              </li>
            </div>
          </li>
          <li class="card__item" v-if="addingCard == index">
            <button class="float-end border-0" @click="closeCard()">
              <font-awesome-icon
                class="icons float-end"
                :icon="['fas', 'xmark']"
              />
            </button>
            <input
              v-model="newCard.title"
              type="text"
              placeholder="Card Title"
              class="add-input"
            />
            <textarea
              v-model="newCard.description"
              placeholder="Card Description"
              class="add-textarea"
            ></textarea>
            <button class="btn add-btn p-2" @click.prevent="addCard(index)">
              <h6 class="mb-0">+ Add Card</h6>
            </button>
          </li>
        </ul>
        <button class="btn add-btn" @click.prevent="showAddCardUI(index)">
          <!-- <font-awesome-icon :icon="['fas', 'plus']" class="icons" /> -->
          <h6 class="mb-0">+ Add another card</h6>
        </button>
      </li>
      <li class="card__item" v-if="addinglist">
        <button class="float-end border-0" @click="closeNewList()">
          <font-awesome-icon class="icons float-end" :icon="['fas', 'xmark']" />
        </button>
        <input
          v-model="newList.listTitle"
          type="text"
          placeholder="Card Title"
          class="add-input"
        />
        <button class="btn add-btn" @click.prevent="addList()">
          <h6 class="mb-0">+ Add New List</h6>
        </button>
      </li>
      <li class="column__item h-100">
        <button class="btn add-btn" @click.prevent="showAddListUI()">
          <!-- <font-awesome-icon :icon="['fas', 'plus']" class="icons" /> -->
          <h6 class="mb-0">+ Add another List</h6>
        </button>
      </li>
    </ul>
    <EditModal
      :isOpen="isModalOpened"
      @modal-close="closeModal"
      @edit-data="editdata"
      @submit="submitHandler"
      name="first-modal"
    />
  </section>
</template>
<script setup>
import EditModal from "./Modal/EditModal.vue";
import { ref } from "vue";

const isModalOpened = ref(false);
const draggedItem = ref(null);
const draggedArray = ref(null);
let addingCard = ref(null);
let addinglist = ref(null);

const response = ref({
  data: [
    {
      title: "Array 1",
      drop_col_id: "",
      data: [
        { html: "<p>AA<p>", age: 11 },
        { html: "<p>BB</p>", age: 22 },
        { html: "<p>CC</p>", age: 33 },
      ],
    },
    {
      title: "Array 2",
      data: [{ html: "<p>XX</p>", age: 66 }],
    },
    {
      title: "Array 3",
      data: [],
    },
  ],
});
const newCard = ref({
  title: "",
  description: "",
});
const newList = ref({
  listTitle: "",
});
const openModal = (event) => {
  isModalOpened.value = true;
  console.log("event", event);
};
const closeModal = () => {
  isModalOpened.value = false;
};

const submitHandler = () => {
  //here you do whatever
};
const showAddCardUI = (index) => {
  addingCard.value = index;
};
const showAddListUI = () => {
  addinglist.value = true;
};
const closeCard = () => {
  addingCard.value = null;
};
const closeNewList = () => {
  addinglist.value = null;
};
const addCard = (index) => {
  console.log('index', index);
  if (newCard.value.title.trim() !== "") {
    if (!response.value.data[index]) {
      response.value.data[index] = { data: [] };
    }
    response.value.data[index].data.push({
      html: `<p>${newCard.value.title}</p>`,
      age: `<p>${newCard.value.description}</p>`, // Set the age as needed
    });
    newCard.value.title = "";
    newCard.value.description = "";
    addingCard.value = null;
  }
};
const addList = () => {
  if (newList.value.listTitle.trim() !== "") {
    if (!response.value.data) {
      response.value.data = { data: [] };
    }
    response.value.data.push({
      title: `${newList.value.listTitle}`,
      data: [],
    });
    newList.value.listTitle = "";
    addinglist.value = null;
  }
};
const editdata = () => {
  console.log('edit');
}

const dragStart = (arrayIndex, itemIndex) => {
  draggedItem.value = itemIndex;
  draggedArray.value = arrayIndex;
};
const allowDrop = (event) => {
  event.preventDefault();
};
const internalAllowDrop = (event) => {
  event.preventDefault();
};
const internalDrop = (targetArrayIndex, targetIndex) => {
  if (draggedItem.value === null || draggedArray.value === null) return;
  if (draggedArray.value === targetArrayIndex) {
    const targetArray = response.value.data[targetArrayIndex];
    const draggedItemObj = targetArray.data.splice(draggedItem.value, 1)[0];
    targetArray.data.splice(targetIndex, 0, draggedItemObj);
  }
};

const dropItem = (targetArrayIndex, event) => {
  event.preventDefault();
  if (draggedItem.value === null || draggedArray.value === null) return;

  const targetArray = response.value.data[targetArrayIndex];
  const targetList = event.target.parentElement;
  if (targetList && targetList.children !== null) {
    const targetIndex = Array.from(targetList && targetList.children).indexOf(
      event.target
    );
    if (targetIndex === targetList && targetList.children.length - 1) {
      const sourceArrayIndex = draggedArray.value;
      if (sourceArrayIndex !== targetArrayIndex) {
        const sourceArray = response.value.data[sourceArrayIndex];
        targetArray.data.push(sourceArray.data[draggedItem.value]);
        sourceArray.data.splice(draggedItem.value, 1);
      }
    } else {
      const sourceArrayIndex = draggedArray.value;
      if (sourceArrayIndex !== targetArrayIndex) {
        const sourceArray = response.value.data[sourceArrayIndex];
        targetArray.data.splice(
          targetIndex,
          0,
          sourceArray.data[draggedItem.value]
        );
        sourceArray.data.splice(draggedItem.value, 1);
      }
    }
  }
  draggedItem.value = null;
  draggedArray.value = null;
};
</script>


<style>
.column__list {
  display: flex;
  flex-direction: row;
  gap: 10px;
}

.column__item {
  border-radius: 0.2rem;
  background-color: #dfe3e6;
  padding: 0.5rem;
  min-width: 270px;
}

.column__title--wrapper {
  display: flex;
  /* grid-template-columns: repeat(2, 1fr); */
  padding: 0.25rem;
  align-items: center;
}

.column__title--wrapper h2 {
  color: #17394d;
  font-weight: 700;
  font-size: 0.9rem;
}

.column__title--wrapper .icons,
.icons {
  text-align: right;
  color: #798d99;
}

.column__item--cta {
  padding: 0.25rem;
  display: flex;
  color: #798d99;
}

.column__item--cta .icons {
  margin-right: 0.25rem;
}
.card__list {
  display: grid;
  grid-template-rows: auto;
  grid-gap: 0.5rem;
  margin: 0.5rem 0;
  height: calc(100% - 80px);
  min-height: 50px;
}

.card__item {
  background-color: white;
  border-radius: 0.25rem;
  box-shadow: 0 1px 0 rgba(9, 45, 66, 0.25);
  padding: 0.5rem;
  height: fit-content;
  min-width: 250px;
}

.card__tag {
  font-size: 1rem;
  padding: 0.1rem 0.5rem;
  border-radius: 0.25rem;
  font-weight: 700;
  color: black;
  margin-bottom: 0.75rem;
  display: inline-block;
  margin-left: 0.2rem;
}

.card__image {
  width: 100%;
  margin-bottom: 0.25rem;
}

/* sticker colors */

.card__tag--design {
  background-color: #61bd4f;
}

.card__tag--browser {
  background-color: #c377e0;
}

.card__tag--mobile {
  background-color: #f2d600;
}

.card__tag--high {
  background-color: #eb5a46;
}

.card__tag--low {
  background-color: #00c2e0;
}

.card__title {
  color: #17394d;
  margin-bottom: 0.75rem;
}

/* card actions */

.card__actions {
  display: flex;
  align-items: center;
}

.card__actions--wrapper .icons {
  color: #798d99;
  margin-right: 0.5rem;
}
.card__actions--wrapper {
  display: flex;
  width: 100%;
}

.card__actions--text {
  color: #798d99;
  font-size: 0.8rem;
  margin-left: -0.25rem;
  margin-right: 0.5rem;
}

.card__avatars {
  display: flex;
  flex: 1;
  justify-content: flex-end;
}

.card__avatars--item {
  margin-left: 0.25rem;
  width: 28px;
  height: 28px;
}

.avatar__image {
  border-radius: 50%;
  width: 100%;
  height: 100%;
  object-fit: cover;
}
ul {
  list-style-type: none;
}
li {
  list-style-type: none;
  margin: 0;
  padding: 0;
}
ol {
  list-style-type: none;
}
ol,
ul {
  padding-left: 0%;
}
.add-btn {
  border: none;
  width: 100%;
  border-radius: 0.25rem;
  box-shadow: 0 1px 0 rgba(9, 45, 66, 0.25);
  display: flex;
  justify-content: center;
}
.add-input,
.add-textarea,
.add-input:focus-visible {
  margin: 5px 0;
  width: 100%;
  border-radius: 3px;
  border: 1px solid #80808052;
  font-size: 12px;
}
</style>