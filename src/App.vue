<template>
  <div id="app" class="container">
    <h1 class="text-danger">Movie Finder</h1>
    <div class="card py-5 px-5">
      <div class="row">
        <div class="col-9">
          <input type="text" name="movieName" class="form-control" placeholder="Enter movie name" v-model="searchText">
        </div>
        <div class="col-3">
          <button class="btn btn-primary btn-block" v-on:click="searchMovie" v-bind:disabled="isSearching"><span v-if="!isSearching">Search</span> <div class="spinner-border" role="status" v-if="ifSearching">
            <span class="visually-hidden">Loading...</span>
          </div></button>
        </div>
      </div>
      <p v-if="errorMessage !=''" class="text-danger mt-3>"{{errorMessage}}</p>

      <table class="table table-striped table-bordered table-hover mt-5" v-if="movie.length > 0">
        <thead class="bg-success text-light">
          <tr>
            <th>Movie Poster</th>
            <th>Movie name</th>
            <th>Movie year</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="movie in movies" :key="movie.imdbID" v-on:click="selectMovie(movie)" v-bind:class="{'bg-warning':selectedMovie && selectedMovie.imdbID == movie.imdbID}">
            <td><img v-bind:src="movie.Poster" height="150px"></td>
            <td>{{movie.Title}}</td>
            <td>{{movie.Year}}</td>
          </tr>

        </tbody>
      </table>
    </div>
    <div class="card py-5 px-5 mt-5" v-if="selectedMovie">
      <h2>Movie Detail</h2>
      <img v-band:src="selectedMovie.Poster">
      <h4>{{selectedMovie.Title}}</h4>
      <p>{{selectedMovie.Year}}</p>
      <p>{{selectedMovie.Plot}}</p>
      <p>{{selectedMovie.Actors}}</p>
      <p>{{selectedMovie.Genre}}</p>
      <p>Directed by: {{selectedMovie.Director}}</p>
      <p>Writer : {{selectedMovie.Writer}}</p>
      <h5>Reviews</h5>
      <div class="card py-3 px-3" v-for="review of selectedMovie.Ratings" :key="review.Source">
        <h3>{{review.Source}}</h3>
        <p class="text-danger">{{review.Value}}</p>
      </div>
    </div>
  </div>
</template>

<script>


export default {
  name: 'App',
  data() {
    return {
      searchText:"",
      movies:[],
      selectedMovie:null, 
      errorMessage:"",
      isSearching:false
    }
  },
  methods: {
    searchMovie: function(){
      this.errorMessage = '';
      this.isSearching = true;

      fetch('http://www.omdbapi.com/?s='+this.searchText+'&apikey=87d10179')
      .then(response => response.json())
      .then(data => {
        this.isSearching = false;
        if (data["Response"] == "True"){
          console.log(data)
          this.movies = data["Search"]
        }
        else {
          console.log(data"Error")
          this.errorMessage = data["Error"]
        }
      }).catch(err=>{

        console.log(err)
        this.isSearching = false;
      }
      )
    },
    selectMovie: function(movie){
      console.log(movie)

      fetch(' http://www.omdbapi.com/?i='+movie.imdbID+'&apikey=87d10179')
      .then(response => response.json())
      .then(data => {
        console.log(data)
        this.selectedMovie = data
      }).catch(err=>
      console.log(err)
      )
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
