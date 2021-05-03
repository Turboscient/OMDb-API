<template>
  <span>
    <b-container fluid>
      <b-row>
        <b-col><h1 class="text-center">OMDb API Movie Search</h1></b-col>
      </b-row>
      <b-row style="background-color: #e0e0eb">
        <b-col cols="3">
          <b-card tag="article" class="mb-4 shadow-sm">
            <h2><b>Movie info</b></h2>
          </b-card>
          <b-card v-if="json.Title" class="text-left">
            <h3><strong>Title</strong></h3>
            <h4>{{ json.Title }}</h4>
            <h3><strong>Released</strong></h3>
            <h4>{{ json.Released }}</h4>
            <div v-for="rating in json.Ratings" :key="rating.Source">
              <h3>
                <strong>{{ rating.Source === 'Internet Movie Database' ? 'IMDb' : rating.Source }}</strong>
              </h3>
              <h4>{{ rating.Value }}</h4>
            </div>
            <div style="margin-bottom: 2vh"></div>            
          </b-card>
        </b-col>
        <b-col cols="6">
          <b-card
            title="The Shoppies"
            img-src='https://images.pexels.com/photos/3137890/pexels-photo-3137890.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=650&w=940'
            img-alt="Movies"
            img-top
            tag="article"
            style="max-width: 50vw"
            class="mb-2 shadow"
          >
            <b-row class="justify-content-center">
              <b-col cols="9">
                <b-form-input
                  v-model="text"
                  placeholder="Search for a movie"
                ></b-form-input>
              </b-col>
              <b-col cols="3">              
                <b-button
                  href="#"
                  v-if="notInMovieList(json.imdbID)"
                  @click="nominationProcess(json)"
                  variant="info"
                  class="btn-block"                  
                  >Nominate
                </b-button>
              </b-col>
            </b-row>
          </b-card>
        </b-col>
        <b-col cols="3">
          <b-card tag="article" class="mb-4 shadow-sm">
            <h2><b>Nominated</b></h2>
          </b-card>
          <b-card
            v-for="nomination in nominated"
            :key="nomination.imdbID"
            class="mb-2 shadow"
          >
            <h3>
              {{ nomination.Title }}
            </h3>
            <b-button
              href="#"
              @click="removeMovie(nomination.imdbID)"             
              variant="warning"             
              ><span style="color: black">Remove</span>
            </b-button>
          </b-card>
        </b-col>
      </b-row>
    </b-container>
    <b-modal id="modal-1" title="OMDb Notification">
        <p class="my-4">You've added five movies!</p>
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
    nominated: [],
  }),
  methods: {
    async fetchText(title) {
      let res = await fetch(
        "https://www.omdbapi.com/?apikey=4a20833c&t=" + title
      );
      let pos = await fetch(
        "https://img.omdbapi.com/?apikey=4a20833c&t=" + title
      );
      let data = await res.text();      
      this.poster = pos;
      console.log(this.poster)
      this.json = JSON.parse(data);      
    },
    removeMovie(id) {
      this.nominated = this.nominated.filter(
        (nomination) => nomination.imdbID !== id
      );
    },
    notInMovieList(id) {      
      return this.nominated.filter((nomination) => nomination.imdbID == id).length === 0;
    },
    nominationProcess(elem) {
      console.log(elem);
      console.log(elem.Response);
      if (elem.Response === "True") {
        this.nominated.push(elem);
        if (this.nominated.length == 5) {
          this.$bvModal.show("modal-1");
        }
      }
    },
  },
  created() {
    this.fetchText("");
  },
  watch: {
    text: function () {
      this.fetchText(this.text);
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
