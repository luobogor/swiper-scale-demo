<template>
 <div>
   <swiper ref="mySwiper"
           :options="swiperOptions"
           :auto-play="false"
           @touch-start="onTouchStart"
           @touch-move="onTouchmove"
           @touch-end="onTouchend"
   >
     <swiper-slide
         v-for="(item, index) in list"
         :key="index">
       {{  item  }}
     </swiper-slide>
   </swiper>
 </div>
</template>

<script>
import { Swiper, SwiperSlide, directive } from 'vue-awesome-swiper'
import 'swiper/css/swiper.css'

export default {
  name: 'CustomSwiper',
  components: {
    Swiper,
    SwiperSlide
  },
  directives: {
    swiper: directive
  },
  data() {
    return {
      // 经测试 list 是可以动态修改的，可以做懒加载
      list: ['Slide 1', 'Slide 2', 'Slide 3'],
      swiperOptions: {
        loop: true,
        slidesPerView: 3,
        spaceBetween: 30,
        centeredSlides: true,
      }
    }
  },
  methods:{
    onTouchStart(event){
      // 防止某些浏览器没有 touchend 事件，在这里再设置一次 allowTouchMove
      this.swiper.allowTouchMove = true;
      this.startX = event.touches[0].clientX; // 获取触摸起始点的水平坐标
    },
    // 限制一次只能滑动一个元素
    // Swiper7 可以直接在 SwiperOptions 设置 touchReleaseOnEdges: true 实现类似的效果，Swiper5 没有这个参数
    onTouchmove(event) {
      const threshold = 100
      const distance = event.touches[0].clientX - this.startX
      const distanceAbs = Math.abs(distance) // 计算滑动距离
      console.log('滑动距离：' + distance + '像素');

      if (distanceAbs > threshold) {
        this.swiper.allowTouchMove = false; // 禁止继续滑动s
        // this.swiper.slideNext() or slidePrev(); 不要用这种方式滑动，向左滑动的时候有时候会有 active 显示错误问题，找不到原因。不用这种方式滑动，当手指释放的时候会自动滑动，不会有 bug。
      }
    },
    onTouchend(){
      this.swiper.allowTouchMove = true;
    },
  },
  computed: {
    // 不要用这种快捷方式，styleListKey++ 后 swiper 重新渲染不会触发 computed swiper 更新，computed swiper 一直是旧的实例
    swiper() {
      return this.$refs.mySwiper.$swiper
    }
  },
}
</script>

<style lang="scss">
.swiper-container {
  height: 300px;
  width: 100%;

  .swiper-slide {
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
    font-weight: bold;
    font-size: 16px;
    background-color: #2c3e50;
    color: #fff;
    transition: transform 0.4s ease; /* 添加滑动动画效果 */
    transform: scale(0.8); /* 默认缩小效果 */
  }

  /* 当前激活的幻灯片放大 */
  .swiper-slide.swiper-slide-active {
    transform: scale(1);
  }

  /* 解决滑动时偶尔抖动的问题 */
  .swiper-slide.swiper-slide-duplicate-active {
    transform: scale(1);
  }
}
</style>

