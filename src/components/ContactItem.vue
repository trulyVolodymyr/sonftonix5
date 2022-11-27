<template>
  <div class="rounded-lg bg-white shadow relative">
    <div class="p-6 pb-2">
      <div class="flex">
        <div class="flex-grow text-sm truncate">
          <template v-if="editMode">
            <input
              ref="inputRef"
              v-model="localContact.name"
              type="text"
              class="block font-medium w-full border"
              placeholder="Name"
            >
            <input
              v-model="localContact.description"
              type="text"
              class="block mt-1 text-gray w-full border"
              placeholder="Description"
            >
            <input
              v-if="showImgInput"
              v-model="localContact.image"
              type="text"
              class="block mt-1 text-gray w-full border"
              placeholder="Image link"
            >
          </template>

          <template v-else>
            <p class="font-medium">{{ cardInfo }}</p>
            <p class="text-gray mt-1 truncate">
              {{ contact.description }}
            </p>
          </template>
        </div>
        <img
          class="w-[40px] h-[40px] object-cover ml-2 rounded-full shrink-0"
          :src="contact.image" alt="contact-logo"
        >
      </div>
      <div class="flex justify-end mt-2 gap-2">
        <template v-if="editMode">
          <span
            class="text-blue-500 font-medium text-xs cursor-pointer hover:underline"
            @click="cencelCreation"
          >Cancel</span>

          <span
            class="text-blue-500 font-medium text-xs cursor-pointer hover:underline"
            :class="{disabled:blockSaveBtn}"
            @click="onSave"
          >Save</span>
        </template>

        <template v-else>
          <span
            class="text-blue-500 font-medium text-xs cursor-pointer hover:underline"
            @click="triggerEditMode"
          >Edit</span>

          <span
            class="text-red-500 font-medium text-xs cursor-pointer hover:underline"
            @click="$emit('delete')"
          >Delete</span>
        </template>
      </div>
    </div>

    <div class="flex text-sm font-medium text-gray-dark border-t border-gray-ultra-light">
      <div class="flex items-center justify-center flex-1 py-4 cursor-pointer hover:text-gray">
        <IconEnvelope />
        <span class="ml-3">Email</span>
      </div>
      <div
        class="flex items-center justify-center flex-1 py-4 border-l
            border-gray-ultra-light cursor-pointer hover:text-gray"
      >
        <IconPhone />
        <span class="ml-3">Call</span>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, nextTick, computed, onMounted, onUpdated } from 'vue'
import type { IContact } from '@/types'
import IconEnvelope from '@/components/icons/IconEnvelope.vue'
import IconPhone from '@/components/icons/IconPhone.vue'

const props = defineProps<{
  contact: IContact
  contacts: IContact[]
}>()

const showImgInput = ref(false)
const blockSaveBtn = ref(false)

const cardInfo = computed(() => {
  return `${props.contact.id}:  ${props.contact.name}`
})

const emit = defineEmits(['delete', 'save', 'cencel'])

const inputRef = ref<HTMLInputElement>()

const localContact = ref<Omit<IContact, 'id'>>({
  name: '',
  description: '',
  image: ''
})

const editMode = ref(false)

async function triggerEditMode () {
  editMode.value = true
  localContact.value = { ...props.contact }
  await nextTick()
  inputRef.value?.focus()
}

function onSave () {
  if (!blockSaveBtn.value) {
    emit('save', localContact.value)
    editMode.value = false
  }
}

onMounted(() => {
  if (!props.contacts[props.contacts.length - 1].name) {
    triggerEditMode()
  }
  if (!props.contacts[props.contacts.length - 1].image) {
    showImgInput.value = true
    triggerEditMode()
  }
})

if (!localContact.value.name && !localContact.value.description && !localContact.value.image) {
  blockSaveBtn.value = true
} else {
  blockSaveBtn.value = false
}

onUpdated(() => {
  console.log()
  if (localContact.value.name && localContact.value.description && localContact.value.image) {
    blockSaveBtn.value = false
  } else {
    blockSaveBtn.value = true
  }
})

function cencelCreation () {
  editMode.value = false
  if (!props.contacts[props.contacts.length - 1].name && !props.contacts[props.contacts.length - 1].description &&
  !props.contacts[props.contacts.length - 1].image) {
    emit('cencel', localContact.value)
  }
}
</script>

<style lang="scss">
.disabled{
  color: rgb(107, 107, 107) !important;
}
</style>
