<script setup>
import { computed, ref, watch } from 'vue'

const props = defineProps({
  images: {
    type: Array,
    required: true,
  },
  displayIndex: {
    type: Object,
    required: true,
  },
  selectedImages: {
    type: Array,
    required: true,
  },
  imagesPerView: {
    type: Number,
    required: true,
  },
})

const isTransitioning = ref(true)

const sliderStyle = computed(() => ({
  transform: `translateX(calc(-100% * ${props.displayIndex.index} - 12px * ${props.displayIndex.index}))`,
  transition: isTransitioning.value ? 'transform 0.3s ease-in-out' : 'none',
}))

watch(
  () => props.displayIndex.index,
  (newIndex) => {
    console.log(newIndex, props.images.length - props.imagesPerView)

    if (newIndex >= props.images.length - props.imagesPerView || newIndex === 0) {
      isTransitioning.value = false
    } else {
      isTransitioning.value = true
    }
  },
)

const isSelected = (image) => {
  return props.selectedImages.some((img) => img.id === image.id)
}
</script>
<template>
  <div class="slider" @transitionend="handleTransitionEnd">
    <div v-for="image in images" :key="image.id" class="slide">
      <img
        :src="image.download_url"
        class="slide"
        :class="{ 'slide--selected': isSelected(image) }"
        loading="lazy"
        :style="sliderStyle"
        @click="$emit('add-image', image)"
      />
    </div>
  </div>
</template>

<style scoped>
.slider-container {
  min-height: 70vh;
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

.slider {
  display: flex;
  width: 80vw;
  height: 100%;
  gap: 12px;

  overflow: hidden;

  @media (min-width: 864px) {
    /* padding-inline: 100px; */
    width: calc(var(--image-width) * 2 + 12px);
    align-self: center;
  }

  @media (min-width: 1440px) {
    padding-block: 36px;
    width: calc(var(--image-width) * 3 + 24px);
    max-width: 1224px;
    align-self: center;
  }
}

.slide {
  width: 80vw;
  height: 80vh;
  border-radius: 50px;
  transition-duration: 1s;

  transition:
    opacity 2s,
    transform 2s;

  @media (min-width: 864px) {
    width: var(--image-width);
    height: var(--image-width);
  }
}

.slide--selected {
  border: 6px solid green;
}

.button {
  cursor: pointer;
  width: 50px;
  height: 50px;
  background: none;
  border-radius: 50%;
  backdrop-filter: blur(4px);
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
  left: 32px;
}

.button-right {
  right: 32px;
}

.modal-button {
  position: fixed;
  bottom: 40px;
  left: 50%;
  transform: translateX(-50%);
}

@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}
</style>
