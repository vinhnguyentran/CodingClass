<template>
  <!-- <Transition name="slide-in"> -->
    <div class="carousel-item" v-show="currentSlide === index">
      <img :src="slide" />
    </div>
  <!-- </Transition> -->
</template>

<script>
export default {
  props: ['slide', 'currentSlide', 'index']
}
</script>
<style scoped lang="less">
.carousel-item{
  position: absolute;
  top: 0;
  right: 0;
  left: 0;
  bottom: 0;
  border-radius: 12px;
  box-shadow: 1px 2px 5px #eee275;
}
.slide-in-enter-active,
.slide-in-leave-active{
  transition: all 1s ease-in-out;
}
.slide-in-enter-from{
  transform: translateX(-100%);
}
.slide-in-leve-to{
  transform: translateX(100%);
}
</style>