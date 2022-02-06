<template>
  <div class="home">
    <HeroMain />

    <div class="container search">
      <input
        @keyup.enter="$fetch"
        type="text"
        placeholder="Search"
        v-model.lazy="search"
      />
      <button @click="clearSearch" v-show="search != ''" class="button">
        Clear
      </button>
    </div>
    <Loding-Movie v-if="$fetchState.pending" />
    <div v-else class="container movies">
      <div v-if="search !== ''" id="movie-grid" class="movies-grid">
        <div
          class="movie"
          v-for="(movie, index) in searchedMovies"
          :key="index"
        >
          <div class="movie-img">
            <img
              :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`"
              alt=""
            />
            <p class="review">{{ movie.vote_average }}</p>
            <p class="overview">{{ movie.overview }}</p>
          </div>
          <div class="info">
            <p class="title">
              {{ movie.title.slice(0, 25) }}
              <span v-if="movie.title.length > 25">...</span>
            </p>
          </div>
          <p class="release">
            Release:
            {{
              new Date(movie.release_date).toLocaleString('en-us', {
                month: 'long',
                day: 'numeric',
                year: 'numeric',
              })
            }}
          </p>
          <NuxtLink
            class="button button-light"
            :to="{ name: 'movies-movieid', params: { movieid: movie.id } }"
            >Get More Info</NuxtLink
          >
        </div>
      </div>
      <div v-else id="movie-grid" class="movies-grid">
        <div class="movie" v-for="(movie, index) in movies" :key="index">
          <div class="movie-img">
            <img
              :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`"
              alt=""
            />
            <p class="review">{{ movie.vote_average }}</p>
            <p class="overview">{{ movie.overview }}</p>
          </div>
          <div class="info">
            <p class="title">
              {{ movie.title.slice(0, 25) }}
              <span v-if="movie.title.length > 25">...</span>
            </p>
          </div>
          <p class="release">
            Release:
            {{
              new Date(movie.release_date).toLocaleString('en-us', {
                month: 'long',
                day: 'numeric',
                year: 'numeric',
              })
            }}
          </p>
          <NuxtLink
            class="button button-light"
            :to="{ name: 'movies-movieid', params: { movieid: movie.id } }"
            >Get More Info</NuxtLink
          >
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'IndexPage',
  data() {
    return {
      movies: [],
      searchedMovies: [],
      search: '',
    }
  },
  async fetch() {
    if (this.search === '') {
      await this.getMovies()
      return
    }
    await this.searchMovies()
  },
  fetchDelay: 1000,
  methods: {
    async getMovies() {
      const data = axios.get(
        'https://api.themoviedb.org/3/movie/now_playing?api_key=d72eb1d37a1fb0ec1db66a0008e1a3bf'
      )
      const result = await data
      // console.log(result)
      result.data.results.forEach((movie) => {
        this.movies.push(movie)
      })
      console.log(this.movies)
    },
    async searchMovies() {
      const data = axios.get(
        `https://api.themoviedb.org/3/search/movie?api_key=d72eb1d37a1fb0ec1db66a0008e1a3bf&language=en-US&page=1&query=${this.search}`
      )
      const result = await data
      result.data.results.forEach((movie) => {
        this.searchedMovies.push(movie)
      })
    },
    clearSearch() {
      this.search = ''
      this.searchedMovies = []
    },
  },
}
</script>

<style lang="scss" scoped>
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
  .movies {
    padding: 32px 16px;
    .movies-grid {
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
      .movie {
        position: relative;
        display: flex;
        flex-direction: column;
        .movie-img {
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
