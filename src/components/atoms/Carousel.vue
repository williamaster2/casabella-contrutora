<template>
  <div>
    <div class="slideshow-container">
      <img
        class="fade slide"
        :class="{ 'slide--visible': shouldRender }"
        v-for="({ source, alt, shouldRender }, index) in computedSlides"
        :key="index"
        :src="source"
        :alt="alt"
        width="100%"
        loading="lazy"
      />

      <BaseButton
        v-if="hasMultipleSlides"
        class="prev"
        @click="plusSlides(-1)"
        icon-button
        aria-label="ícone de slide anterior"
      >
        <font-awesome-icon size="lg" icon="fa-solid fa-chevron-left" />
      </BaseButton>
      <BaseButton
        v-if="hasMultipleSlides"
        class="next"
        @click="plusSlides(1)"
        icon-button
        aria-label="ícone de próximo slide"
      >
        <font-awesome-icon size="lg" icon="fa-solid fa-chevron-right" />
      </BaseButton>
    </div>

    <div style="text-align: center">
      <span
        class="dot"
        :class="{ 'dot--active': isActive(index) }"
        v-for="(_, index) in computedSlides"
        :key="index"
        @click="currentSlide(1)"
      ></span>
    </div>
  </div>
</template>

<script setup>
import { computed, onMounted, ref } from 'vue'
import BaseButton from './BaseButton.vue'

const props = defineProps({
  slides: {
    type: Array,
    default: () => []
  }
})

const slideIndex = ref(0)

const computedSlides = computed(() => {
  return props.slides.map((el, index) => {
    return {
      ...el,
      shouldRender: index === slideIndex.value
    }
  })
})

const hasMultipleSlides = computed(() => {
  return computedSlides.value.length > 1
})

// Next/previous controls
function plusSlides(n) {
  showSlides((slideIndex.value += n))
}

// Thumbnail image controls
function currentSlide(n) {
  showSlides((slideIndex.value = n))
}

function showSlides(n) {
  let slides = computedSlides.value
  if (n >= slides.length) {
    slideIndex.value = 0
  }
  if (n < 0) {
    slideIndex.value = slides.length - 1
  }
}

function isActive(index) {
  return index === slideIndex.value
}

onMounted(() => {
  showSlides(slideIndex)
})
</script>

<style lang="scss" scoped>
/* Slideshow container */
.slideshow-container {
  position: relative;
  margin: auto;
}

/* Hide the images by default */
.slide {
  display: none;
  width: 100%;
  aspect-ratio: 16/9;

  &--visible {
    display: block;
  }
}

/* Next & previous buttons */
.prev,
.next {
  position: absolute;
  top: 50%;
  width: auto;
  margin-top: -22px;
  padding: 16px;
  user-select: none;
}

/* Position the "next button" to the right */
.next {
  right: 0;
}

/* The dots/bullets/indicators */
.dot {
  cursor: pointer;
  height: 15px;
  width: 15px;
  margin: 0 2px;
  background-color: #bbb;
  border-radius: 50%;
  display: inline-block;
  transition: background-color 0.6s ease;

  &--active,
  &:hover {
    background-color: #717171;
  }
}

/* Fading animation */
.fade {
  animation-name: fade;
  animation-duration: 1.5s;
}

@keyframes fade {
  from {
    opacity: 0.4;
  }
  to {
    opacity: 1;
  }
}
</style>
