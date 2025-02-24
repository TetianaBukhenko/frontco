<script setup>
import { computed, onMounted, onUnmounted, ref } from 'vue'
import axios from 'axios'
import SelectedImages from './components/SelectedImages.vue'
import CarouselComponent from './CarouselComponent.vue'

let images = ref([])
let loading = ref(false)
let error = ref('')
let displayIndex = ref({ index: 0, direction: 'increase' })
const width = ref(window.innerWidth)
const updateWidth = () => (width.value = window.innerWidth)
const selectedImages = ref([])

onMounted(() => {
  loading.value = true
  axios
    .get('https://picsum.photos/v2/list')
    .then((response) => {
      images.value = [...response.data]
    })
    .catch(() => {
      error.value = 'Failed to load images'
    })
    .finally(() => {
      loading.value = false
    })

  window.addEventListener('resize', updateWidth)
})

onUnmounted(() => {
  window.removeEventListener('resize', updateWidth)
})

const imagesPerView = computed(() => (width.value > 1440 ? 3 : width.value > 864 ? 2 : 1))

function changeIndex(direction = 'increase') {
  if (direction === 'increase') {
    displayIndex.value.index =
      displayIndex.value.index + 1 > images.value.length - imagesPerView.value
        ? 0
        : displayIndex.value.index + 1
    displayIndex.value.direction = 'increase'
  } else {
    displayIndex.value.index =
      displayIndex.value.index - 1 < 0
        ? images.value.length - imagesPerView.value
        : displayIndex.value.index - 1
    displayIndex.value.direction = 'decrease'
  }
}

const addImage = (image) => {
  if (selectedImages.value && !selectedImages.value.some((img) => img.id === image.id)) {
    selectedImages.value.push(image)
  } else {
    selectedImages.value = selectedImages.value.filter((el) => el.id !== image.id)
  }
}
</script>

<template>
  <header>
    <div class="wrapper"></div>
  </header>

  <main>
    <div class="slider-container">
      <p v-if="error">{{ error }}</p>
      <Loader v-if="loading" />
      <template v-else>
        <button @click="changeIndex('decrease')" class="button button-left"><</button>

        <CarouselComponent
          :images="images"
          :displayIndex="displayIndex"
          :selectedImages="selectedImages"
          :imagesPerView="imagesPerView"
          @add-image="addImage($event)"
        />
        <button @click="changeIndex('increase')" class="button button-right">></button>
      </template>
    </div>

    <template v-if="selectedImages.length > 0">
      <button
        type="button"
        class="btn btn-success modal-button btn-lg"
        data-bs-toggle="modal"
        data-bs-target="#selectedImagesModal"
      >
        Show Selected Images
      </button>

      <SelectedImages :selected-images="selectedImages" />
    </template>
  </main>
</template>

<style scoped>
.slider-container {
  overflow: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0;
  gap: 24px;
  position: relative;
  border-radius: 50px;
  box-shadow:
    rgba(50, 50, 93, 0.25) 0px 50px 100px -20px,
    rgba(0, 0, 0, 0.3) 0px 30px 60px -30px;

  @media (min-width: 864px) {
    padding: 24px;
  }
}

.button {
  cursor: pointer;
  width: 50px;
  height: 50px;
  padding: 0;
  background: none;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.2);
  backdrop-filter: blur(12px);
  border: none;
  position: absolute;
  z-index: 5;

  box-shadow:
    rgba(50, 50, 93, 0.25) 0px 50px 100px -20px,
    rgba(0, 0, 0, 0.3) 0px 30px 60px -30px,
    rgba(10, 37, 64, 0.35) 0px -2px 6px 0px inset;

  @media (min-width: 1024px) {
    position: static;
  }
}

.button-left {
  bottom: 12px;
  transform: translateX(-80%);

  @media (min-width: 864px) {
    transform: translateX(0);
    left: 32px;
    bottom: auto;
  }
}

.button-right {
  bottom: 12px;
  transform: translateX(80%);

  @media (min-width: 864px) {
    transform: translateX(0);
    right: 32px;
    bottom: auto;
  }
}

.modal-button {
  position: fixed;
  bottom: 40px;
  left: 50%;
  transform: translateX(-50%);
}
</style>
