<template>
  <div class="carousel">
    <div class="carousel-inner">
      <carousel-item :current-side="currentSlide" v-for="(slide, index) in slides" slide="slide" :key="`Item-${index}`" :index="index">
        <div class="carousel-item">
          <img :src="slide" />
        </div>
      </carousel-item>
    </div>
  </div>
</template>

<script>
  import CarouelItem from './CarouselItem.vue'
  export default {
    props: ['slides'],
    components: [
      CarouelItem
    ],
    data: () => {
      return {
        currentSlide: 0,
        slideInterval: null
      }
    },
    methods: {
      setCurrentSlide (index) {
        this.currentSlide = index
      }
    },
    mounted () {
      this.slideInterval = setInterval(() => {
        const index = this.currentSlide < this.slides.length - 1 ? this.currentSlide + 1 : 0
        this.setCurrentSlide(index)
      }, 3000)
    },
    beforeUnmount () {
      clearInterval(this.slideInterval)
    }
  }
</script>

<style scoped lang="less">
.carousel{
    width: 100%;
    display: flex;
    justify-content: center;
    .carousel-inner{
      width: 100%;
      position: relative;
      overflow: hidden;
    }
  }
</style>
