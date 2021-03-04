<template>
  <div class="vendor-filter__header">
    <div class="vendor-filter__title">
      Фильтр:
      <span v-for="filter in filters"
            class="vendor-filter__name"
            :key="filter.value"
            :class="{'vendor-filter__name-select': selectedFilter === filter.value}"
            @click="$emit('setFilter', filter.value)">
        {{ filter.name }}
      </span>
    </div>
  </div>
</template>

<script>
export default {
    props: {
        filters: {
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
