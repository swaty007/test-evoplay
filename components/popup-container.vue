<template>
  <div class="wrap">

    <div class="vendor-filter">
      <vendor-filter :filters="filters"
                     :selectedFilter="selectedFilter"
                     @setFilter="setFilter"/>
      <photo-list :list="currentList"
                  :selectedFilter="selectedFilter"
                  :favorite-items="favoriteItems"
      @changeFav="toggleFavorite"/>
    </div>

    <div class="wrap__bg"></div>
  </div>
</template>

<script>
import VendorFilter from '@/components/vendor-filter';
import PhotoList from '@/components/photo-list';

export default {
    components: {
        VendorFilter,
        PhotoList,
    },

    data () {
        return {
            photos: [],
            // photosIds: [],
            filters: [
                {
                    name: 'По алфавиту',
                    value: 'alph',
                },
                {
                    name: 'По альбомам',
                    value: 'album',
                },
                {
                    name: 'Избранное',
                    value: 'favorite',
                },
            ],
            selectedFilter: 'alph',
            favoriteItems: [],
        };
    },

    computed: {
        currentList () {
            let list = {};
            switch (this.selectedFilter) {
                case 'album':
                    this.photos.forEach((val) => {
                        const key = val.albumId;
                        if (list[key] === undefined) list[key] = [];
                        list[key].push(val);
                    });
                    break;
                case 'favorite':
                  let favorite = this.photos.filter((photo) => this.favoriteItems.includes(photo.id))
                  return { favorite }
                    break;
                default:
                    this.photos.forEach((val) => {
                        const letter = val.title.slice(0, 1);
                        if (list[letter] === undefined) list[letter] = [];
                        list[letter].push(val);
                    });
                    break;
            }
            for (const item in list) {
                list[item].length = this.randomInteger(5, 10);
                list[item].sort((first, second) => (first.title < second.title ? -1 : 1));
            }

            return this.sortObjectKeys(list);
        },
    },

    watch: {
        selectedFilter () {
            localStorage.setItem('selectedFilter', JSON.stringify(this.selectedFilter));
        },
        favoriteItems () {
            localStorage.setItem('favoriteItems', JSON.stringify(this.favoriteItems));
        },
    },

    created () {
        this.init();

    },

  mounted () {
    this.fetchData();
  },

  methods: {
        init () {
            this.selectedFilter = JSON.parse(localStorage.getItem('selectedFilter'));
            this.favoriteItems = JSON.parse(localStorage.getItem('favoriteItems')) ?? [];
        },
        fetchData () {
            this.$axios.$get('http://jsonplaceholder.typicode.com/photos')
                .then((data) => {
                    this.photos = data.filter((el) => el.albumId <= 32);
                    // this.photos.forEach((val) => {
                    //     this.photosIds[val.id] = val;
                    // });
                });
        },
        setFilter (value) {
            this.selectedFilter = value;
        },
        toggleFavorite (id) {
            const index = this.favoriteItems.indexOf(id);
            if (index > -1) {
                this.favoriteItems.splice(index, 1);
            } else {
                this.favoriteItems.push(id);
            }
        },
        sortObjectKeys (arr) {
            return Object.keys(arr)
                .sort().reduce((obj, key) => {
                    obj[key] = arr[key];

                    return obj;
                }, {});
        },
        randomInteger (min, max) {
            const rand = min - 0.5 + Math.random() * (max - min + 1);

            return Math.round(rand);
        },
    },
};
</script>

<style lang="scss">
.wrap {
    position: relative;
    height: 100vh;
    display: flex;

    &__bg {
        position: fixed;
        top: 0;
        right: 0;
        bottom: 0;
        left: 0;
        z-index: -1;
        background: rgba(27, 32, 79, 0.2);
    }
}

.vendor-filter {
    font-weight: 700;
    font-family: Open Sans, Roboto, sans-serif;
    max-width: 1300px;
    width: 100%;
    height: 600px;
    margin: auto;
    padding: 3.6rem 4.8rem 6rem;
    font-size: 1.2rem;
    background-color: #ffffff;
    border-radius: 1.2rem;
    box-sizing: border-box;

    &__header {
        margin: 0 0 1.5rem;
    }

    &__title {
        font-weight: bold;
        font-size: 1.4rem;
        line-height: 1.9rem;
    }

    &__name {
        margin: 0 0.5rem;
        text-decoration: underline;
        cursor: pointer;
        padding: 5px;
        border-radius: 8px;

        &-select {
            background-color: #000;
            color: #ffffff;
        }
    }

    &__scroll-wrap {
        overflow: hidden;
        max-height: 100%;
        overflow-y: auto;
    }

    &__scroll {
        width: 100%;
        column-count: 4;

        &.favorite {
            column-count: unset;
        }
    }

    &__list {
        position: relative;
        display: inline-block;

        &-title {
            font-weight: bold;
            font-size: 12px;
            line-height: 16px;
            color: #1b204f;
            margin-left: 12px;
            margin-bottom: 8px;
            margin-top: 18px;
            text-transform: uppercase;
        }

        &.favorite {
            display: flex;
            //justify-content: space-between;
            flex-wrap: wrap;

            .vendor-filter__list-title {
                display: none;
            }

            .vendor-filter__item {
                width: 20%;
            }
        }
    }

    &__item {
        display: flex;
        align-items: center;
        margin-bottom: 8px;
        padding: 4px;
        border-radius: 8px;
        position: relative;
        padding-right: 28px;

        &-photo {
            width: 32px;
            height: 32px;
            object-fit: cover;
            border-radius: 8px;
        }

        &-text {
            font-size: 12px;
            line-height: 16px;
            color: #1c214c;
            margin: 0 0 0 11px;
        }

        &-favorite {
            width: 18px;
            height: 18px;
            cursor: pointer;
            position: absolute;
            right: 5px;
        }
    }
}
</style>
