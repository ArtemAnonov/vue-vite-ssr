<template>
  <slider-banners-node
    class="slider-banners-fashion-blog"
    :bannerCategoryId="69"
    identificator="slider-banners-fashion-blog"
    :space-between="60"
    :breakpoints="{
      '320': {
        slidesPerView: 1.25,
        spaceBetween: 20,
      },
      '992': {
        slidesPerView: 2,
        spaceBetween: 50,
      },
    }"
  >
    <template #banner-category-name>
      <div @click="$router.push('/blog-page')"  class="slider-banners-fashion-blog__title">Fashion-блог</div>
    </template>
    <template #banner-title="bannerTitleProps">
      <div @click="$router.push('/blog-page')"  class="slider-banners-fashion-blog__banner-title">
        {{ bannerTitleProps.banner.title.rendered }}
      </div>
    </template>
  </slider-banners-node>
</template>

<script>
import SliderBannersNode from "@/components/sliders/SliderBannersNode.vue";

export default {
  components: {
    SliderBannersNode,
  },
  props: {},
  data() {
    return {};
  },
  computed: {},
  methods: {},
};
</script>

<style lang="scss">
.slider-banners-fashion-blog {
  background-color: #231f20;

  &__title {
    font-size: 2rem;
    cursor: pointer;
  }

  &__banner-title {
    cursor: pointer;
    font-size: 1.4rem;
    line-height: 2rem;
    color: #fff;
    text-align: center;
    margin: 1.3333333333rem 1.3333333333rem 0;
    text-transform: uppercase;
    @media (max-width: ($md3+px)) {
      font-size: 0.8666666667rem;
      line-height: 1.2rem;
    }
  }

  .slider-banners {
    &__items {
      display: flex;
      justify-content: space-between;
      color: #fff;
      margin: 0.6666666667rem 0 1.3333333333rem;
    }
    &__image {
      padding: 0 0 60% 0;
    }
  }
  .slider-arrows {
    position: static;
  }
}
</style>
