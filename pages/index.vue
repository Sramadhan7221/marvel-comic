<template>
  <div class="home">
    <!-- Hero -->
    <Hero />
    <!-- Comic -->
    <div class="container comics">
      <div id="comic-grid" class="comics-grid">
        <div v-for="(comic, index) in comics" :key="index" class="comic" >
          <div class="comic-img">
            <img
              :src="`${comic.thumbnail.path}/portrait_xlarge.${comic.thumbnail.extension}`"
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
            <NuxtLink
              class="button button-light"
              :to="{ name: 'comic-comicid', params: { comicid: comic.id } }"
              >Get More Info</NuxtLink
            >
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import md5 from 'blueimp-md5'
export default {
  data() {
    return {
      comics: [],
    }
  },
  async fetch() {
    await this.getComics()
  },
  methods: {
    async getComics() {
      const ts = Date.now()
      const hash = md5(
        ts +
          '5a58e83a879dd3811909b196e9e6cb1b37e1fd72' +
          'e87a19e72566520830fdabc1aa0bd4d7'
      )
      await this.$axios
        .get(
          `v1/public/comics?ts=${ts}&apikey=e87a19e72566520830fdabc1aa0bd4d7&hash=${hash}`
        )
        .then((res) => (this.comics = res.data.data.results))
      console.log(this.comics)
    },
  },
  // created() {
  //   this.getComics()
  // },
}
</script>

<style lang="scss">
.home {
  .loading {
    padding-top: 120px;
    align-items: flex-start;
  }
  .search {
    display: flex;
    padding: 32px 16px;
    input {
      max-width: 350px;
      width: 100%;
      padding: 12px 6px;
      font-size: 14px;
      border: none;
      &:focus {
        outline: none;
      }
    }
    .button {
      border-top-left-radius: 0;
      border-bottom-left-radius: 0;
    }
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
        grid-template-columns: repeat(2, 1fr);
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
