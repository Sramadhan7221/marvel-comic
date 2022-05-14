<template>
  <div class="home">
    <!-- Hero -->
    <Hero />
    <!-- Comic -->
    <form class="formSearch" v-on:submit.prevent="cari">
      <div class="col">
        <input
          class="form-control me-2"
          type="search"
          placeholder="Search"
          v-model.lazy="search"
        />
        <button v-show="search !== ''" class="button" type="submit">
          Search
        </button>
      </div>
      <pagePagination
        :totalPages="5"
        :perPage="20"
        :currentPage="currentPage"
        @pagechanged="onPageChange"
      />
    </form>
    <div v-if="loading" class="loading">
      <img src="~/assets/imgs/loding.gif" class="img" alt="" />
    </div>
    <div v-else class="container comics">
      <div id="comic-grid" class="comics-grid">
        <div v-for="(comic, index) in comics" :key="index" class="comic">
          <div class="comic-img">
            <img
              :src="`${comic.thumbnail.path}/portrait_uncanny.${comic.thumbnail.extension}`"
              alt=""
            />
            <p class="review">
              {{ comic.variantDescription }}
            </p>
            <p class="overview">
              {{ comic.description }}
            </p>
          </div>
          <div class="info">
            <p class="title">
              {{ comic.title.slice(0, 25) }}
              <span v-if="comic.title.length > 25">...</span>
            </p>
            <a
              class="button button-light"
              :href="'https://www.google.com/search?q=' + comic.title"
              >Get More Info</a
            >
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import md5 from 'blueimp-md5'
import PagePagination from '~/components/PagePagination.vue'
export default {
  components: {
    PagePagination,
  },
  data() {
    return {
      comics: [],
      search: '',
      currentPage: 1,
      loading: false,
    }
  },
  async fetch() {
    await this.getComics(20, 0)
  },
  methods: {
    async getComics(limit, offset) {
      this.loading = true
      const ts = Date.now()
      const hash = md5(
        ts +
          '5a58e83a879dd3811909b196e9e6cb1b37e1fd72' +
          'e87a19e72566520830fdabc1aa0bd4d7'
      )
      const url = this.search
        ? `v1/public/comics?title=${this.search}&limit=${limit}}&offset=${offset}&ts=${ts}&apikey=e87a19e72566520830fdabc1aa0bd4d7&hash=${hash}`
        : `v1/public/comics?dateDescriptor=thisMonth&limit=${limit}}&offset=${offset}&ts=${ts}&apikey=e87a19e72566520830fdabc1aa0bd4d7&hash=${hash}`
      await this.$axios.get(url).then((res) => {
        if (res.data.data.results.length == 0) {
          console.log('data kosong')
        } else {
          this.comics = res.data.data.results
        }
      })
      this.loading = false
    },
    async cari() {
      this.currentPage = 1
      await this.getComics(20, 0)
    },
    onPageChange(page) {
      this.getComics(20, page * 20)
      this.currentPage = page
    },
  },
  // created() {
  //   this.getComics()
  // },
}
</script>

<style lang="scss">
.home {
  .formSearch {
    display: flex;
    margin-left: 3.5em;
    padding: 32px 16px;
    justify-content: space-between;
    .col {
      display: flex;
      width: 35%;
      input {
        width: 100%;
        margin: 0px 5px;
        padding: 12px 6px;
        font-size: 14px;
        border: none;
        border-radius: 5px;
        &:focus {
          outline: none;
        }
      }

      .button {
        border-top-left-radius: 0;
        border-bottom-left-radius: 0;
      }
    }
  }
  .loading {
    display: flex;
    justify-content: center;
  }
  .comics {
    padding: 32px 16px;
    .comics-grid {
      display: grid;
      column-gap: 32px;
      row-gap: 64px;
      grid-template-columns: 1fr;
      @media (min-width: 500px) {
        grid-template-columns: repeat(2, 1fr);
      }
      @media (min-width: 750px) {
        grid-template-columns: repeat(3, 1fr);
      }
      @media (min-width: 1100px) {
        grid-template-columns: repeat(4, 1fr);
      }
      .comic {
        position: relative;
        display: flex;
        flex-direction: column;
        .comic-img {
          position: relative;
          overflow: hidden;
          &:hover {
            .overview {
              transform: translateY(0);
            }
          }
          img {
            display: block;
            width: 100%;
            height: 100%;
          }
          .review {
            position: absolute;
            top: 0;
            left: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            width: 40px;
            height: 40px;
            background-color: #c92502;
            color: #fff;
            border-radius: 0 0 16px 0;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1),
              0 2px 4px -1px rgba(0, 0, 0, 0.06);
          }
          .overview {
            line-height: 1.5;
            position: absolute;
            bottom: 0;
            background-color: rgba(201, 38, 2, 0.9);
            padding: 12px;
            color: #fff;
            transform: translateY(100%);
            transition: 0.3s ease-in-out all;
          }
        }
        .info {
          margin-top: auto;
          .title {
            margin-top: 8px;
            color: #fff;
            font-size: 20px;
          }
          .release {
            margin-top: 8px;
            color: #c9c9c9;
          }
          .button {
            margin-top: 8px;
          }
        }
      }
    }
  }
}
</style>
