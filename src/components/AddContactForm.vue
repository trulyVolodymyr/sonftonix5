<template>
  <div class="p-5">
    <h2 class="mb-4">ADD CONTACT</h2>
    <form @submit.prevent>
      <input v-model="name" type="text" placeholder="Name" class="input">
      <p v-if="nameError" class="error"> Please enter name</p>

      <input v-model="description" type="text" placeholder="Description" class="input">
      <p v-if="descriptionError" class="error"> Please description</p>

      <input v-model="image" type="text" placeholder="Image link" class="input">
      <p v-if="ImgError" class="error"> Please pase image</p>

      <button
        class="bg-gray p-3 rounded"
        type="button"
        @click="AddContact"
      >
        Add Contact
      </button>
    </form>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue'

const name = ref('')
const description = ref('')
const image = ref('')

const nameError = ref(false)
const descriptionError = ref(false)
const ImgError = ref(false)

const emit = defineEmits(['add-contact'])

function clearInputs () {
  name.value = ''
  description.value = ''
  image.value = ''
}

function AddContact () {
  if (name.value === '') {
    nameError.value = true
  }

  if (description.value === '') {
    descriptionError.value = true
  }

  if (image.value === '') {
    ImgError.value = true
  }

  if (name.value !== '' && description.value !== '' && image.value !== '') {
    nameError.value = descriptionError.value = ImgError.value = false
    emit('add-contact', { id: 0, name: name.value, description: description.value, image: image.value })
    clearInputs()
  }
}
</script>

<style lang="scss">
.input{
  @apply block w-full mb-2 p-2 border
}
.error{
  @apply text-xs text-rose-500 mb-2
}

</style>
