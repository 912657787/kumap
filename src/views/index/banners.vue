<template>
  <div class="banner-warp">
    <swiper :options="swiperOption">
      <swiper-slide v-for="item in banners" :key="item.id">
        <router-link :to="'/app/home/productDetail/'+item.url" target=_blank>
          <img style="width: 100%; height: 300px" :src="item.img" alt="" />
        </router-link>
      </swiper-slide>
      <div class="swiper-pagination" slot="pagination"></div>
    </swiper>
  </div>
</template>
<style>
  .banner-warp {
    height: 300px;
  }
</style>



<script>
  import {
    swiper,
    swiperSlide
  } from 'vue-awesome-swiper'
  import {
    bannerGoods
  } from '../../api/api'

  export default {
    components: {
      swiper,
      swiperSlide,
    },
    data() {

      return {

        swiperOption: {
          pagination: '.swiper-pagination',
          paginationClickable: true,
          autoplay: 2500,
          autoplayDisableOnInteraction: false,
        },
        banners: []

      }

    },
    methods: {
      getBanner() {
        bannerGoods()
          .then((response) => {
            console.log("获取轮播图")
            console.log(response)
            //跳转到首页页response.body面
            this.banners = response.data
            console.log(this.banners)
          })
          .catch(function(error) {
            console.log(error);
          });
      }
    },
    created() {
      this.getBanner();
    }

  }
</script>
