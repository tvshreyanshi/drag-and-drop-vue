<template>
  <div v-if="isOpen" class="tvd__modal-mask">
    <div class="tvd__modal-wrapper">
      <div class="tvd__modal-container" ref="target">
        <div class="tvd__modal-header">
          <h6>{{ isEdit ? 'Edit Card' : 'Show Card'}}</h6>
          <button class="tvd__close_editmodal tvd__modal-close" @click="$emit('modal-close')">
            <i class="tvd__icons fa-solid fa-xmark"></i>
          </button>
        </div>
        <hr class="tvd__hr"/>
        <div class="tvd__modal-body">
          <div v-if="!isEdit">
            <div class="tvd__show_title" v-if="getData.event && getData.event.title">
              <h6>{{ getData.event.title }}</h6>
            </div>
            <div class="tvd__show_title" v-if="getData.event && getData.event.deadlineDate">
              <span>DeadLine:</span>
              <span>{{ getData.event.deadlineDate }}</span>
            </div>
            <div v-if="getData.event && getData.event.description">
              <span v-html="getData.event.description"></span>
            </div>
            <div class="tvd__image" v-if="getData.event && getData.event.attachment">
              <div v-for="(data, index) in getData.event.attachment" :key="index">
                <div v-if="data.type=='image'">
                  <img class="tvd__img_attachment" :src="data.dataUrl" alt="image" />
                </div>
              </div>
            </div>
          </div>
          <div v-if="isEdit">
            <form @submit.prevent="UpdateData()">
              <div class="tvd__form-group">
                <label class="tvd__form-label">Title</label>
                <input class="tvd__form-control" type="text" v-model="title" />
              </div>
              <div class="tvd__form-group">
                <label class="tvd__form-label">Description</label>
                <textarea
                  class="tvd__form-control"
                  rows="4"
                  v-model="description"
                ></textarea>
              </div>
              <div class="tvd__form-group">
                <label class="tvd__form-label">Due Date</label>
                <input
                  class="tvd__form-control"
                  type="date"
                  v-model="duedate"
                />
              </div>
              <div class="tvd__form-group">
                <label class="tvd__form-label">Attachment</label>
                <input
                  class="tvd__form-control"
                  type="file"
                  multiple
                  @change="handleFileChange"
                />
              </div>

              <div class="tvd__modal-footer">
                <button type="file" class="tvd__edit_btn">Submit</button>
                <button class="tvd__edit_btn" @click="$emit('modal-close')">
                  Close
                </button>
              </div>
            </form>
          </div>
          <!-- <slot name="content"> {{ getData.age }} </slot>
          <p name="header" v-html="getData.html"></p> -->
        </div>
      </div>
    </div>
  </div>
</template>
<script setup>
import { watch, defineProps, defineEmits, ref, computed } from "vue";
import { onClickOutside } from "@vueuse/core";

const props = defineProps({
  isOpen: {
    type: Boolean,
    default: false,
  },
  isEdit: {
    type: Boolean,
    default: false,
  },
  data: {
    default: () => ({}),
  },
  type: String,
});
const title = ref("");
const description = ref("");
const duedate = ref(null);
const attachment = ref([]);
const emit = defineEmits(["modal-close"], ["edit-data"]);

const target = ref(null);

const handleFileChange = (event) => {
  const selectedFile = event.target.files;
  if(selectedFile && selectedFile.length > 0) {
    const newAttachments = []
    Array.from(selectedFile).forEach((file) => {
      let type = file.type.split("/")[0];
      const reader = new FileReader();
      reader.onload = () => {
        newAttachments.push({
          type,
          file,
          dataUrl: reader.result,
        });
        if (newAttachments.length === selectedFile.length) {
          attachment.value = newAttachments;
        }
      };
      reader.readAsDataURL(file);
    });
  } else {
    console.error('Selected files is not an array:', selectedFile);
  }
};

onClickOutside(target, () => {
  if (props.isOpen) {
    emit("modal-close");
  }
});
const getData = computed(() => {
  return props.data;
});
const UpdateData = () => {
  const editData = {
    title: title.value,
    description: description.value,
    deadlineDate: duedate.value,
    attachment: attachment.value,
    arrayindex: getData.value.arrayindex,
    itemindex: getData.value.itemindex,
  };
  emit("edit-data", editData);
};
watch(getData, async (val) => {
  if (val) {
    title.value = val.event.title;
    description.value = val.event.description;
    duedate.value = val.event.deadlineDate;
    attachment.value = val.event.attachment;
  }
});
</script>

