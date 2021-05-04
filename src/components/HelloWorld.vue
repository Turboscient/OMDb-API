<template>
  <span>
    <b-container fluid>
      <b-row>
        <b-col><h1 class="text-center">OMDb API Movie Search</h1></b-col>
      </b-row>
      <b-row style="background-color: #e0e0eb">

        <!-- Display search results view -->
        <b-col xs="12" md="3" class="overflow-auto" style="max-height: 80vh">
          <b-card tag="article" class="mb-4 shadow-sm">
            <h2><b>Search results</b></h2>
          </b-card>
          <span v-for="result in searchResults.Search" :key="result.imdbID + '_s' + Math.random().toString()">
            <b-card
              v-if="notInMovieList(result.imdbID)"
              class="text-left mb-2 shadow"
            >
              <b-row class="text-center">
                <b-col>
                  <h3>
                    {{ result.Title }} ({{ result.Year }})
                  </h3>
                </b-col>
              </b-row>
              <b-row class="text-center">
                <b-col>
                  <b-button
                    href="#"
                    @click="nominationProcess(result)"
                    variant="danger"
                    >Nominate
                  </b-button>
                </b-col>
                <b-col class="pl-0">
                  <b-button href="#" variant="info" @click="fetchInfo(result.Title)">More info </b-button>
                </b-col>
              </b-row>
            </b-card>
          </span>
        </b-col>

        <b-col xs="12" md="6">
          <b-card
            title="The Shoppies"
            img-src="https://images.pexels.com/photos/3137890/pexels-photo-3137890.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=650&w=940"
            img-alt="Movies"
            img-top
            tag="article"
            class="mb-2 shadow"
          >
            <b-row class="justify-content-center">
              <b-col cols="9">
                <b-form-input
                  v-model="text"
                  placeholder="Search for a movie"
                ></b-form-input>
              </b-col>
            </b-row>
          </b-card>
        </b-col>
        <b-col xs="12" md="3" class="overflow-auto" style="max-height: 80vh">
          <b-card tag="article" class="mb-4 shadow-sm">
            <h2><b>Nominated</b></h2>
          </b-card>
          <b-card
            v-for="nomination in nominated"
            :key="nomination.imdbID + '_n' + Math.random().toString()"
            class="mb-2 shadow text-center"
          >
            <b-row class="text-center">
              <b-col>
                <h3>
                  {{ nomination.Title }} ({{ nomination.Year }})
                </h3>
              </b-col>
            </b-row>
            <b-row>
              <b-col class="pr-0">
                <b-button
                  href="#"
                  @click="removeMovie(nomination.imdbID)"
                  variant="warning"
                  ><span style="color: black">Remove</span>
                </b-button>
              </b-col>
              <b-col class="pl-0">
                <b-button
                  href="#"
                  variant="info"
                  @click="fetchInfo(nomination.imdbID)"
                  >More info
                </b-button>
              </b-col>
            </b-row>
          </b-card>
        </b-col>
      </b-row>
    </b-container>
    <b-modal id="modal-1" title="OMDb Notification">
      <p class="my-4">You've added five movies!</p>
    </b-modal>
            <!-- Display movie info view -->
        <b-modal id="modal-0" title="Movie information">
          <b-row>
          <b-col v-if="json" cols="6" class="text-center">
            <img :src="json.Poster" />
          </b-col>
          <b-col v-if="resultsOrInfo === 'info'">
            <h4><strong>Title</strong></h4>
            <h5>{{ json.Title }}</h5>
            <h4><strong>Director</strong></h4>
            <h5>{{ json.Director }}</h5>
            <h4><strong>Runtime</strong></h4>
            <h5>{{ json.Runtime }}</h5>
            <h4><strong>Released</strong></h4>
            <h5>{{ json.Year }}</h5>
            <div v-for="rating in json.Ratings" :key="rating.Source + '_t' + Math.random().toString()">
              <h4>
                <strong>{{
                  rating.Source === "Internet Movie Database"
                    ? "IMDb"
                    : rating.Source
                }}</strong>
              </h4>
              <h5>{{ rating.Value }}</h5>
            </div>
            <div style="margin-bottom: 2vh"></div>
          </b-col>
          </b-row>
        </b-modal>
  </span>
</template>

<script>
export default {
  name: "HelloWorld",
  data: () => ({
    json: null,
    poster: null,
    text: "",
    resultsOrInfo: "results",
    nominated: [],
    searchResults: [],
  }),
  methods: {
    async fetchInfo(id) {
      let res = await fetch(
        "https://www.omdbapi.com/?apikey=4a20833c&i=" + id
      );
      let data = await res.text();
      this.json = JSON.parse(data);
      this.resultsOrInfo = "info";      
      this.$bvModal.show("modal-0");
      console.log(this.json);
    },
    async search(title) {
      let res = await fetch(
        "https://www.omdbapi.com/?apikey=4a20833c&s=" + title
      );
      let data = await res.text();
      this.searchResults = JSON.parse(data);
    },
    removeMovie(id) {
      this.nominated = this.nominated.filter(
        (nomination) => nomination.imdbID !== id
      );
    },
    notInMovieList(id) {
      return (
        this.nominated.filter((nomination) => nomination.imdbID === id)
          .length === 0
      );
    },
    nominationProcess(elem) {
      this.nominated.push(elem);
      if (this.nominated.length === 5) {
        this.$bvModal.show("modal-1");
      }
    },
  },
  created() {},
  watch: {
    text: function () {
      this.search(this.text);
    },    
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin-top: 0.5rem;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: white;
}
</style>

<style>

.modal-dialog {
  max-width: 80vw !important;
}
</style>
