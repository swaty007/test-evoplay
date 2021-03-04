<template>
  <div class="vendor-filter__scroll-wrap">
    <div class="vendor-filter__scroll" :class="selectedFilter">
      <div v-for="(items, key) in list" :key="key" class="vendor-filter__list" :class="key">
        <template v-if="items">
          <h3 class="vendor-filter__list-title">
            {{ key }}
          </h3>
          <div v-for="photo in items" :key="photo.id"
               class="vendor-filter__item">
            <img :src="photo.thumbnailUrl" class="vendor-filter__item-photo"/>
            <p class="vendor-filter__item-text">
              {{ photo.title }}
            </p>
            <img class="vendor-filter__item-favorite"
                 :src="favoriteItems.includes(photo.id) ? '/img/favorite_full.svg' : '/img/favorite_empty.svg'"
                 @click.stop="$emit('changeFav', photo.id)"/>
          </div>
        </template>
      </div>
    </div>
  </div>
</template>

<script>
export default {
    props: {
        list: {
            type: Object,
            default: () => {},
        },
        favoriteItems: {
            type: Array,
            default: () => [],
        },
        selectedFilter: {
            type: String,
            default: 'alph',
        },
    },

    data () {
        return {};
    },

    computed: {},

    created () {
    },

    methods: {},
};
</script>

<style lang="scss">

</style>
