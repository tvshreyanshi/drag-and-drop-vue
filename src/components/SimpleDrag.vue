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
          <font-awesome-icon :icon="['fas', 'plus']" class="column__item--cta" />
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
            <span class="card__tag card__tag--browser">Browser</span>
            <span class="card__tag card__tag--design">Design</span>
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
                    <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRuXwdzjy8m5Awd5zZ6GknOv2dVo0xYeHEvJu3JGU3SlXEH7y1WV4GIpgSlAmQIN4a0qBI&usqp=CAU" alt="Man standing near balcony" class="avatar__image">
                  </li>
                </div>
              </li>
            </div>
          </li>{{ addNewCard }}
          <ul
          v-if="addNewCard"
          class="card__list"
          @drop.prevent="dropItem(index, $event)"
          @dragover.prevent="allowDrop"
        >
          <li
            class="card__item"
            :draggable="true"
            @dragstart="dragStart(index, itemindex)"
            @dragover.prevent="internalAllowDrop"
            @drop.prevent="internalDrop(index, itemindex)"
          >
            <span class="card__tag card__tag--browser">Browser</span>
            <span class="card__tag card__tag--design">Design</span>
            <span></span>
            <div class="card__actions">
              <li class="card__actions--wrapper">
                <font-awesome-icon
                  class="icons"
                  :icon="['fas', 'align-left']"
                />
                <font-awesome-icon :icon="['far', 'comment']" class="icons" />
                <div class="card__avatars">
                  <li class="card__avatars--item">
                    <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRuXwdzjy8m5Awd5zZ6GknOv2dVo0xYeHEvJu3JGU3SlXEH7y1WV4GIpgSlAmQIN4a0qBI&usqp=CAU" alt="Man standing near balcony" class="avatar__image">
                  </li>
                </div>
              </li>
            </div>
            <div class="form-group">
            <label for="cardTitle">Title:</label>
            <input v-model="newCard.title" type="text" id="cardTitle" />
          </div>
          <div class="form-group">
            <label for="cardDescription">Description:</label>
            <textarea v-model="newCard.description" id="cardDescription"></textarea>
          </div>
          <button @click="addCard(id)">Add Card</button>
          </li>
        </ul>
        </ul>
          <button class="add-btn" @click.prevent="addCard(index)">
            <!-- <font-awesome-icon :icon="['fas', 'plus']" class="icons" /> -->
            <h6>+ Add another card</h6>
          </button>
      </li>
    </ul>
    <!-- <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-body">
            <div class="form-group">
            <label for="cardTitle">Title:</label>
            <input v-model="newCard.title" type="text" id="cardTitle" />
          </div>
          <div class="form-group">
            <label for="cardDescription">Description:</label>
            <textarea v-model="newCard.description" id="cardDescription"></textarea>
          </div>
          <button @click="addCard(id)">Add Card</button>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
            <button type="button" class="btn btn-primary">Save changes</button>
          </div>
        </div>
      </div>
    </div> -->
    
  </section>
</template>
<script>
import { ref } from "vue";
export default {
  name: "SimpleDrag",
  setup() {
    const response = ref({
      data: [
        {
          title: "Array 1",
          drop_col_id:'',
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
    const draggedItem = ref(null);
    const draggedArray = ref(null);
    let getArrayId = ref(null);
    let addNewCard = ref(false);
    const newCard = ref({
      title: "",
      description: "",
    });
    
    const addCard = (index) => {

      console.log('avla--------', getArrayId);
      if (newCard.value.title.trim() !== "") {
        if (!response.value.data[index]) {
          response.value.data[index] = { data: [] };
        }
        console.log('res', response.value);
        response.value.data[index].data.push({
          html: `<p>${newCard.value.title}</p>`,
          age: `<p>${newCard.value.description}</p>`, // Set the age as needed
        });
        console.log('response', response.value);
        newCard.value.title = "";
        newCard.value.description = "";
      }
    };

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
      if(draggedArray.value === targetArrayIndex){
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
      const targetIndex = Array.from(targetList && targetList.children).indexOf(event.target);
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
          targetArray.data.splice(targetIndex, 0, sourceArray.data[draggedItem.value]);
          sourceArray.data.splice(draggedItem.value, 1);
        }
      }
      draggedItem.value = null;
      draggedArray.value = null;
    };
    return {
      response,
      dragStart,
      allowDrop,
      dropItem,
      internalDrop,
      internalAllowDrop,
      addCard,
      newCard,
      addNewCard,
    };
  },
};
</script>
<style>
.column__list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));
  grid-gap: 0.5rem;
  align-items: start;
  /* uncomment these lines if you want to have the standard Trello behavior instead of the column wrapping */
  /*   grid-auto-flow: column;
    grid-auto-columns: minmax(260px, 1fr); */
}

.column__item {
  border-radius: 0.2rem;
  background-color: #dfe3e6;
  padding: 0.5rem;
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

.column__title--wrapper .icons {
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
}

.card__item {
  background-color: white;
  border-radius: 0.25rem;
  box-shadow: 0 1px 0 rgba(9, 45, 66, 0.25);
  padding: 0.5rem;
}

.card__tag {
  font-size: 0.75rem;
  padding: 0.1rem 0.5rem;
  border-radius: 0.25rem;
  font-weight: 700;
  color: white;
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
ol, ul {
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
</style>