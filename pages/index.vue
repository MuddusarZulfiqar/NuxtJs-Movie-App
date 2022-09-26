<template>
  <div class="home">
    <Loading v-if="$fetchState.pending" />
    <template v-else>
      <!-- ? Hero -->
      <Hero />

      <!-- ? Search -->
      <div class="container search">
        <input
          v-model.lazy="search"
          type="text"
          placeholder="Search"
          @keyup.enter="searchList"
        />
        <button v-show="search != ''" class="button" @click="search = ''">
          Clear Search
        </button>
      </div>
      <!-- ? Movies -->
      <div v-if="search == ''" class="container movies">
        <div v-if="movies.length > 0" id="movie-grid" class="movies-grid">
          <div v-for="(movie, index) in movies" :key="index" class="movie">
            <div class="movie-img">
              <img
                :src="`https://image.tmdb.org/t/p/w500${movie.poster_path}`"
                :alt="movie.original_title"
              />
              <p class="review">{{ movie.vote_average }}</p>
              <p class="overview">
                {{ movie.overview.slice(0, 200)
                }}<span v-if="movie.overview.length > 200">...</span>
              </p>
            </div>
            <div class="info">
              <p class="title">
                {{ movie.title.slice(0, 25)
                }}<span v-if="movie.title.length > 25">...</span>
              </p>
              <p class="release">
                Released:
                {{
                  new Date(movie.release_date).toLocaleString('en-us', {
                    month: 'long',
                    day: 'numeric',
                    year: 'numeric',
                  })
                }}
              </p>
              <n-link
                class="button button-light"
                :to="{ name: 'movies-movieId', params: { movieId: movie.id } }"
                >Get More Info</n-link
              >
            </div>
          </div>
        </div>
        <p v-else class="container text--white">No Movie Found...</p>
      </div>

      <template v-else>
        <div v-if="searchMovies.length > 0" class="container movies">
          <div id="movie-grid" class="movies-grid">
            <div
              v-for="(movie, index) in searchMovies"
              :key="index"
              class="movie"
            >
              <div class="movie-img">
                <img
                  :src="`https://image.tmdb.org/t/p/w500${movie.poster_path}`"
                  :alt="movie.original_title"
                />
                <p class="review">{{ movie.vote_average }}</p>
                <p class="overview">
                  {{ movie.overview.slice(0, 200)
                  }}<span v-if="movie.overview.length > 200">...</span>
                </p>
              </div>
              <div class="info">
                <p class="title">
                  {{ movie.title.slice(0, 25)
                  }}<span v-if="movie.title.length > 25">...</span>
                </p>
                <p class="release">
                  Released:
                  {{
                    new Date(movie.release_date).toLocaleString('en-us', {
                      month: 'long',
                      day: 'numeric',
                      year: 'numeric',
                    })
                  }}
                </p>
                <n-link
                  class="button button-light"
                  :to="{
                    name: 'movies-movieId',
                    params: { movieId: movie.id },
                  }"
                  >Get More Info</n-link
                >
              </div>
            </div>
          </div>
        </div>
        <p  v-else class="container text--white">
          No Movie Found...
        </p>
      </template>
    </template>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
  data() {
    return {
      movies: [],
      search: '',
      searchMovies: [],
      
    }
  },
  async fetch() {
    if (this.search === '') {
      await this.getMovies()
      return
    }
    await this.searchMoviesList()
  },
  methods: {
    async getMovies() {
      this.results = []
      this.searchMovies = []
      try {
        const result = await this.$axios.$get(
          `/movie/now_playing?api_key=80f9f2f935f729091000ba95082ddee8&language=en-US&page=1`
        )
        result.results.forEach((movie) => this.movies.push(movie))
      } catch (error) {
        console.log(error)
      }
    },
    async searchMoviesList() {
      this.results = []
      this.searchMovies = []
      const data = await this.$axios.$get(
        `/search/movie?api_key=80f9f2f935f729091000ba95082ddee8&query=${this.search}`
      )
      data.results.forEach((movie) => this.searchMovies.push(movie))
    },
    searchList() {
      if (this.search === '') {
        return false
      }
      this.$fetch()
    },
  },
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
        grid-template-columns: repeat(2, 1fr);
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
.text--white{
  color: #fff;
}
</style>
