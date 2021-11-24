<template>
  <table>
    <thead>
      <tr>
        <th valign="top" :class="{ thFocused: focusMode }">
          <input
            type="text"
            :placeholder="placeholder"
            autocomplete="off"
            v-model="inputText"
            @focus="inputFocused"
            @blur="inputBlur"
            @keyup="getResults(inputText)"
            :class="{ inputFocused: focusMode, loadingGif: load }"
          />
          <h4 v-show="focusMode">Enter a movie name</h4>
        </th>
      </tr>
    </thead>
    <tbody @mouseenter="this.mouseInAutoComplete = true" @mouseleave="this.mouseInAutoComplete = false">
      <tr v-for="result in this.resultTable" :key="result.id">
        <td @click="getTitle(result.title)">
          {{ result.title }}
          <p>{{ result.vote_average }} rating. {{ result.release_date.slice(0, 4) }}</p>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script>
const API_KEY = "0d720d9ec399cd0d44f361e8a7dc9255";
let timer;

export default {
  data() {
    return {
      placeholder: "Enter a movie name",
      focusMode: false,
      load: false,
      inputText: "",
      resultTable: [],
      mouseInAutoComplete: false,
    };
  },

  methods: {
    inputFocused() {
      this.focusMode = true;
      this.placeholder = "";
    },
    inputBlur() {
      this.focusMode = false;
      this.placeholder = "Enter a movie name";

      if (!this.mouseInAutoComplete) {
        this.resultTable = [];
        this.getTitle(this.inputText);
      }
    },
    debounce(func, timeout = 2000) {
      return (...args) => {
        clearTimeout(timer);
        timer = setTimeout(() => {
          func.apply(this, args);
        }, timeout);
      };
    },
    async getMovieList(data) {
      if (data != "") {
        this.load = true;
        let MOVIE_URL = `https://api.themoviedb.org/3/search/movie?api_key=${API_KEY}&language=en-US&query=${data}`;
        const response = await fetch(MOVIE_URL);
        const jsn = await response.json();
        this.load = false;
        let sortedJSON = jsn.results.sort((a, b) => b.vote_average > a.vote_average);

        this.resultTable = sortedJSON.length > 8 ? sortedJSON.slice(0, 8) : sortedJSON.slice(0, jsn.length);
      }
    },
    getResults(e) {
      const debounceCall = this.debounce((data) => this.getMovieList(data));
      if (e.length > 2) {
        debounceCall(e);
      } else {
        debounceCall("");
      }
    },
    getTitle(title) {
      this.inputText = title;
      this.resultTable = [];
      this.$emit("pickedResult", title);
    },
  },
};
</script>

<style>
:root {
  --movie-white: url("data:image/svg+xml, %3Csvg%20version%3D%221.1%22%20id%3D%22Layer_1%22%20xmlns%3D%22http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg%22%20%20x%3D%220px%22%0A%20%20%20%20%20y%3D%220px%22%0A%20%20%20%20%20width%3D%22512px%22%20height%3D%22512px%22%20viewBox%3D%220%200%20512%20512%22%20enable-background%3D%22new%200%200%20512%20512%22%20xml%3Aspace%3D%22preserve%22%3E%0A%3Cpath%20d%3D%22M352%2C255.5l-192%2C96v-192L352%2C255.5z%20M512%2C31.5v448H0v-448H512z%20M320%2C95.5h64v-32h-64V95.5z%20M224%2C95.5h64v-32h-64V95.5z%0A%09%20M128%2C95.5h64v-32h-64V95.5z%20M32%2C95.5h64v-32H32V95.5z%20M96%2C415.5H32v32h64V415.5z%20M192%2C415.5h-64v32h64V415.5z%20M288%2C415.5h-64v32h64%0A%09V415.5z%20M384%2C415.5h-64v32h64V415.5z%20M480%2C415.5h-64v32h64V415.5z%20M480%2C127.5H32v256h448V127.5z%20M480%2C63.5h-64v32h64V63.5z%22%20fill%3D%22%23eee%22%2F%3E%0A%3C%2Fsvg%3E");
  --movie-black: url("data:image/svg+xml, %3Csvg%20version%3D%221.1%22%20id%3D%22Layer_1%22%20xmlns%3D%22http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg%22%20%20x%3D%220px%22%0A%20%20%20%20%20y%3D%220px%22%0A%20%20%20%20%20width%3D%22512px%22%20height%3D%22512px%22%20viewBox%3D%220%200%20512%20512%22%20enable-background%3D%22new%200%200%20512%20512%22%20xml%3Aspace%3D%22preserve%22%3E%0A%3Cpath%20d%3D%22M352%2C255.5l-192%2C96v-192L352%2C255.5z%20M512%2C31.5v448H0v-448H512z%20M320%2C95.5h64v-32h-64V95.5z%20M224%2C95.5h64v-32h-64V95.5z%0A%09%20M128%2C95.5h64v-32h-64V95.5z%20M32%2C95.5h64v-32H32V95.5z%20M96%2C415.5H32v32h64V415.5z%20M192%2C415.5h-64v32h64V415.5z%20M288%2C415.5h-64v32h64%0A%09V415.5z%20M384%2C415.5h-64v32h64V415.5z%20M480%2C415.5h-64v32h64V415.5z%20M480%2C127.5H32v256h448V127.5z%20M480%2C63.5h-64v32h64V63.5z%22%20fill%3D%22%23000%22%2F%3E%0A%3C%2Fsvg%3E");
}

input:focus {
  outline: none;
}

input {
  height: 40px;
  font-size: 1em;
  font-weight: bold;
  background: rgba(214, 214, 214, 0.322);
  background-image: var(--movie-white);
  background-position: 0.75rem center, right center;
  background-repeat: no-repeat;
  background-size: 30px 30px;
  padding-left: 3.2rem;
  border: none;
  border-radius: 2px;
  color: aliceblue;
  box-sizing: border-box;
  width: 100%;
}

th.thFocused {
  background: white;
}

input.inputFocused {
  background-image: var(--movie-black);
  color: #17222b;
}

input.inputFocused.loadingGif {
  background-image: var(--movie-black), url("../assets/loading.gif");
  color: #17222b;
}

table {
  border-collapse: collapse;
  width: 50%;
}

h4 {
  margin: 0 0;
  position: absolute;
  margin-left: 3rem;
  margin-top: -0.1rem;
  color: #6d8494;
}

tbody {
  display: block;
  max-height: 300px;
  overflow-y: scroll;
  background: white;
}

th {
  height: 54px;
  border-radius: 2px;
  align-items: center;
  padding: 0;
}

td {
  height: 60px;
  font-size: 1.2em;
  font-weight: bold;
  color: #334755;
}

td p {
  margin: 0;
  font-size: 0.9em;
  font-weight: normal;
  color: #3f525f;
}
</style>
