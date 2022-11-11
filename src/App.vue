<template>
  <div
    class="w-full h-fit sm:h-screen bg-fixed bg-center bg-cover flex flex-col justify-around"
    :style="this.currentFilm.poster"
  >
    <div
      class="text-center hidden w-48 h-14 xl:flex bg-black/70 absolute top-5 left-5 justify-center items-center rounded-2xl border border-[#f1e107] shadow-md shadow-[#f1e107]"
    >
      <h1
        class="text-4xl bg-gradient-to-r from-[#e0a204] to-[#f1e107] bg-clip-text text-transparent font-bold"
      >
        VueFilms
      </h1>
    </div>
    <div
      class="w-full lg:w-2/3 mx-auto flex items-center flex-col pt-6 h-full bg-black/70"
    >
      <div class="w-full mx-auto flex items-center justify-center">
        <input
          v-model="this.searchInput"
          @keydown.enter="this.fetchFilmsArray"
          maxlength="40"
          type="text"
          class="w-3/4 h-8 rounded-xl px-3 text-lg font-['Roboto'] border-2 border-[#f1e107] tracking-wider bg-gradient-to-r bg-black/10 focus:outline-none focus:shadow-md focus:shadow-[#f1e107] transition-all duration-300 focus:bg-black text-white"
        />
        <svg
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
          stroke-width="1.5"
          stroke="currentColor"
          class="w-7 h-7 text-white absolute lg:right-[25.5%] right-[13.4%]"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M21 21l-5.197-5.197m0 0A7.5 7.5 0 105.196 5.196a7.5 7.5 0 0010.607 10.607z"
          />
        </svg>
      </div>
      <div
        v-if="this.isSearchOptionVisible"
        @mouseleave="this.isSearchOptionVisible = false"
        class="bg-white w-3/4 lg:w-1/2 top-14 absolute rounded-b-xl rounded-t-md mx-auto flex max-h-40 h-40 border border-slate-500 flex-col overflow-hidden"
      >
        <div
          v-for="item in this.filmsArray"
          @click="this.choseFilm(item.id)"
          class="w-full h-1/5 border-b border-slate-300 last:border-0 px-2 font-['Roboto'] flex items-center cursor-pointer hover:bg-blue-500 transition-all duration-300 last:rounded-b-lg first:rounded-t-sm"
        >
          {{item.title}}
        </div>
      </div>
      <div
        class="w-full h-full sm:p-4 pt-2 flex flex-col sm:flex-row items-center"
      >
        <div class="h-2/3 w-2/3 sm:w-1/3 border border-white">
          <img :src='this.currentFilm.image' class="w-full h-full object-cover" alt="">
        </div>

        <div
          class="h-full w-full sm:w-2/3 p-4 text-white flex flex-col"
        >
          <h2 class="text-4xl break-words">{{ this.currentFilm.fullTitle }}</h2>
          <p class="text-sm sm:text-lg my-2 italic">
            {{ this.currentFilm.plot }}
          </p>
          <p class="my-2 text-sm sm:text-lg">
            Genres: <span class="italic">{{ this.currentFilm.genres }}</span>
          </p>
          <p class="my-2 text-sm sm:text-lg">
            Runtime: <span class="italic">{{ this.currentFilm.runtimeStr }}</span>
          </p>
          <p class="my-2 text-sm sm:text-lg flex items-center">
            IMDB: <span class="italic">{{ this.currentFilm.imDbRating }}</span
            ><svg
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 20 20"
              fill="currentColor"
              class="w-5 h-5 text-yellow-500"
            >
              <path
                fill-rule="evenodd"
                d="M10.868 2.884c-.321-.772-1.415-.772-1.736 0l-1.83 4.401-4.753.381c-.833.067-1.171 1.107-.536 1.651l3.62 3.102-1.106 4.637c-.194.813.691 1.456 1.405 1.02L10 15.591l4.069 2.485c.713.436 1.598-.207 1.404-1.02l-1.106-4.637 3.62-3.102c.635-.544.297-1.584-.536-1.65l-4.752-.382-1.831-4.401z"
                clip-rule="evenodd"
              />
            </svg>
          </p>
          <div
            class="w-full flex items-center justify-center"
          >
            <button class="w-40 h-20 bg-red-500 rounded-xl" @click="openPlayer">
              Watch trailer
            </button>
          </div>
          <div
            class="w-screen h-screen absolute top-0 left-0 bg-black/80 flex items-center justify-center"
            :class="{'hidden':!this.isPlayerVisible}"
          >
            <iframe
              id="player"
              :src="this.currentFilm.trailer.linkEmbed"
              :class="{'hidden':!this.isPlayerVisible}"
              class="w-2/3 h-1/4 sm:h-2/3"
              frameborder="0"
            ></iframe>
            <svg
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="1.5"
              stroke="currentColor"
              class="absolute top-4 right-4 w-10 h-10 hover:cursor-pointer hover:scale-110"
              @click="closePlayer"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M9.75 9.75l4.5 4.5m0-4.5l-4.5 4.5M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
              />
            </svg>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      searchInput: '',
      isPlayerVisible: false,
      isSearchOptionVisible: false, 
      filmsArray: [],
      currentFilm: {
        "id": "tt0110413",
        "fullTitle": "Léon: The Professional (1994)",
        "runtimeStr": "1h 50min",
        "plot": "12-year-old Mathilda is reluctantly taken in by Léon, a professional assassin, after her family is murdered. An unusual relationship forms as she becomes his protégée and learns the assassin...",
        "image": "https://m.media-amazon.com/images/M/MV5BOTgyMWQ0ZWUtN2Q2MS00NmY0LWI3OWMtNjFkMzZlNDZjNTk0XkEyXkFqcGdeQXVyMjUzOTY1NTc@._V1_Ratio0.6762_AL_.jpg",
        "genres": "Action, Crime, Drama",
        "imDbRating": "8.5",
        "poster": "background-image: url(https://m.media-amazon.com/images/M/MV5BMjIyNjk1OTgzNV5BMl5BanBnXkFtZTcwOTU0OTk1Mw@@._V1_Ratio1.5000_AL_.jpg)",
        "trailer": {
          'linkEmbed' : "https://www.imdb.com/video/imdb/vi2959588889/imdb/embed",
        },
      },
    };
  },
  methods: {
    async choseFilm(id) {
      const newFilm = await axios.get('https://imdb-api.com/en/API/Title/k_auc4k6f1/' + id + '/Trailer,');
      this.currentFilm = {...newFilm.data};
      const newPoster = await axios.get('https://imdb-api.com/en/API/Images/k_auc4k6f1/' + id);
      this.currentFilm.poster = "background-image: url(" + newPoster.data.items[0].image + ")";

    },
    async fetchFilmsArray() {
      this.filmsArray = [];
      if (this.searchInput) {
        const title = this.searchInput;
        const response = await axios.get('https://imdb-api.com/API/SearchMovie/k_auc4k6f1/' + title);
        for (let i = 0; i < 5; i++) {
          this.filmsArray.push(response.data.results[i]);
        }
        this.isSearchOptionVisible = true;
      }
    },
    closePlayer() {
      this.isPlayerVisible = false;
    },
    openPlayer() {
      this.isPlayerVisible = true;
    }
  }
}
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap");
</style>
