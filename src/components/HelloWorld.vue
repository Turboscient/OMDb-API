<template>
  <div class="hello">
    <div>
      <h1>OMDb API Movie Search</h1>
      <div style="width: 20vw; float: left; margin-left: 5vw;">
        <b-card       
          tag="article"
          style="max-width: 20rem;"
          class="mb-2"
        >
          <h2><b>Movie info</b></h2>
          <h3>Title: {{ JSON.parse(jsonRes).Title }}</h3>
          <h3>Released: {{ JSON.parse(jsonRes).Released }}</h3>
          <h3 v-for="rating in JSON.parse(jsonRes).Ratings" :key="rating.Source">
              {{ rating.Source }}: {{ rating.Value }}
          </h3>
          <div style="margin-bottom: 2vw;"></div>
          
            <b-button href="#" 
                      v-if="notInMovieList(JSON.parse(jsonRes).imdbID)"
                      @click="nominationProcess((JSON.parse(jsonRes)))" 
                      variant="outline-primary">Nominate
            </b-button>      
           
        </b-card>
      </div>

      <div style="float: left;">
        <b-card
          title="The Shoppies"
          img-src="https://images.pexels.com/photos/3137890/pexels-photo-3137890.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=650&w=940"
          img-alt="Movies"
          img-top
          tag="article"
          style="max-width: 50vw;"
          class="mb-2"
        >        
            <div style="width: 20vw; margin-left: 15vw">
              <b-form-input v-model="text" placeholder="Search for a movie"></b-form-input>            
            </div>        
        </b-card>
      </div>

      <div style="width: 20vw; float: left; margin-left: 2vw;">
        <b-card       
          tag="article"
          style="max-width: 20rem;"
          class="mb-2"
        >
          <h2><b>Nominated</b></h2>
          <h3 v-for="nomination in nominated" :key="nomination.imdbID">                 
            {{ nomination.Title }}
            <b-button href="#"       
              @click="removeMovie(nomination.imdbID)"            
              variant="dark">Remove
            </b-button>                
          </h3>
        </b-card>
      </div>
    </div>    

    <div>

      <b-modal id="modal-1" title="OMDb Notification">
        <p class="my-4">You've added five movies!</p>
      </b-modal>
      
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',  
  data: () => ({
    jsonRes: null,
    poster: null,
    text: '',
    nominated: []
  }),
  methods: {
    async fetchText(title) {
          let res = await fetch('http://www.omdbapi.com/?apikey=a72d8c19&t=' + title)
          let pos = await fetch('http://img.omdbapi.com/?apikey=a72d8c19&t=' + title)
          let data = await res.text()          
          this.jsonRes = data    
          this.poster = pos    
    },
    removeMovie(id) {
      this.nominated.splice(id, 1)     
    },
    notInMovieList(id) {
      const newArr = [...this.nominated]      
      return newArr.filter(nomination => nomination.imdbID == id).length == 0
    },
    nominationProcess(elem) {
      this.nominated.push(elem)
      if (this.nominated.length == 5) {
        this.$bvModal.show('modal-1') 
      }
    }
  },
  created() {
    this.fetchText('')
  },
  watch: {
    text: function () {
      this.fetchText(this.text)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
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
  color: #42b983;
}
</style>
