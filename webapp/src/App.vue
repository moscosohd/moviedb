<template>
  <div id='app'>
    <header>
      <a id='logo' href='#'>
        <img src='./assets/logo.png'>
      </a>
      <a href='#'>Login <i class='f7-icons' data-icon='person_crop_circle_fill'></i></a>
    </header>
    <nav id='app-4'>
      <a v-for='link in navlinks' v-bind:href='link.loc' v-bind:class='link.class' v-bind:key='link.text'>{{ link.text }}</a>
    </nav>
    <form class='search' v-on:submit='createSearch($event)'>
      <input type='text' placeholder='Search...' name='search' id='searchText'>
      <button type='submit'><i class='f7-icons f7-size-32' data-icon='search'></i></button>
    </form>
    <router-view/>
    <p id='pages'><a href='#'>1</a><a href='#'>2</a><a href='#'>3</a><a href='#'>4</a><a href='#'><i class='f7-icons' data-icon='chevron_right_circle_fill'></i> Next</a></p>
    <footer>
      <p>&copy; 2021 moviedb — <a href='#'><i class='f7-icons f7-size-21' data-icon='logo_facebook'></i></a><a href='#'><i class='f7-icons f7-size-21' data-icon='logo_twitter'></i></a><a href='#'><i class='f7-icons f7-size-21' data-icon='logo_instagram'></i></a><a href='#'><i class='f7-icons f7-size-21' data-icon='logo_googleplus'></i></a></p>
    </footer>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'App',
  data: function () {
    return {
      navlinks: [
        {text: 'TV', loc: '#'},
        {text: 'Tickets', loc: '#'},
        {class: 'breaker'},
        {text: 'Movies', loc: '#'},
        {class: 'breaker'},
        {text: 'Forums', loc: '#'},
        {text: 'Help', loc: '#'}
      ],
      createSearch: function (e) {
        e.preventDefault()
        document.getElementById('pages').style.display = 'block'
        let params = {query: document.getElementById('searchText').value}
        return axios.get('http://localhost:8080/static/properties.json').then(function (envl) {
          let searchPattern = envl.data.API_BASE_URL + '/3/search/movie?api_key=' + envl.data.API_KEY + '&language=en-US&query=' + params.query
          params.page ? searchPattern += '&page=' + params.page : false
          params.adult ? searchPattern += '&include_adult=' + params.adult : searchPattern += '&include_adult=false'
          axios.get(searchPattern).then(function (res) {
            let resultLength = res.data.results.length
            let searchDisplayElement = document.getElementById('searchResults')
            const imagePath = 'https://image.tmdb.org/t/p/w500'
            searchDisplayElement.innerHTML = ''
            for (var i = 0; i < resultLength; i++) {
              if (i < 10) {
                let currentResult = res.data.results[i]
                let searchResult = document.createElement('div')

                let rating = (Math.round((currentResult.vote_average/2) * 2) / 2) + 0.5
                console.log(rating)
                let stars = ''
                for (var j = 0; j < 5; j += 0.5) {
                  if (j < rating) {
                    stars += '<i class="f7-icons" data-icon="star_lefthalf_fill"></i>'
                  } else {
                    stars += '<i class="f7-icons" data-icon="star"></i>'
                  }
                }
                stars = stars.replace(/<i class="f7-icons" data-icon="star_lefthalf_fill"><\/i><i class="f7-icons" data-icon="star_lefthalf_fill"><\/i>/g, '<i class="f7-icons" data-icon="star_fill"></i>')
                stars = stars.split('</i>')
                let finalStars = ''
                for (var j = 0; j < 5; j++) {
                  finalStars += stars[j] + '</i>'
                }

                searchResult.innerHTML = '<div><object data="' + imagePath + currentResult.poster_path + '" type="image/jpeg"><img src="/static/img/logo.ecaad9e.png"></object><aside><h1>' + currentResult.title + '</h1><p>' + currentResult.overview + '</p><span>' + finalStars + '</span></aside></div>'
                searchResult.classList = 'moviecard'
                searchResult.style.background = 'url(' + imagePath + currentResult.backdrop_path + ') no-repeat center center, url("/static/img/logo.ecaad9e.png") no-repeat center center'


                searchDisplayElement.append(searchResult)
              }
            }

            console.log(res.data)

          })
        })
      }
    }
    
  }
}
</script>


<style>
@import url('https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,100;0,300;0,400;0,500;0,700;0,900;1,100;1,300;1,400;1,500;1,700;1,900&display=swap');
@import url('./assets/framework7-icons.css');
:root {
  --orange: #f26b3a;
  --hover-orange: #f68f6A;
  --green: #42b983;
  --hover-green: #65c89b;
  --nav-height: 50px;
  --button-size: 100px;
  --button-font-size: 20px;
  --movie-size: 100px;
}
@media (max-width: 600px) {
  :root {
    --button-size: 60px;
    --button-font-size: 12px;
  }
  nav a {
    font-weight: bold;
  }
}
* {
  box-sizing: border-box;
}
body {
  margin: 0;
  font-family: 'Roboto', sans-serif;
  background-color: black;
  color: white;
}
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 60px;
  display: flex;
  min-height: calc(calc(100vh - var(--nav-height)) - 10px);
  flex-direction: column;
}
main {
  display: flex;
  flex: 1;
  flex-wrap: wrap;
  align-items: stretch;
}
a:hover {
  font-weight: bold;
}
header {
  position: absolute;
  top: 0;
  left: 0;
  width: 100vw;
  height: var(--nav-height);
  background-color: var(--orange);
}
header a:first-child {
  padding: 0;
}
header a {
  font-size: 20px;
  color: white;
  text-decoration: none;
  float: right;
  line-height: var(--nav-height);
  padding: 0 16px;
  height: var(--nav-height);
}
header a:nth-child(2):hover {
  background-color: var(--hover-orange);
}
#logo {
  float: left;
}
#logo img {
  height: var(--nav-height);
}
nav {
  width: calc(calc(var(--button-size) * 4.4) + 20px);
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
  height: calc(calc(var(--button-size) * 2) + 20px);
}
nav a {
  font-size: var(--button-font-size);
  color: white;
  text-decoration: none;
  display: block;
  height: var(--button-size);
  width: var(--button-size);
  border-radius: 50%;
  background-color: var(--green);
  line-height: var(--button-size);
  margin: 5px;
}
nav a:hover {
  background-color: var(--hover-green);
}
.breaker {
  flex-basis: 100%;
  width: 0;
  align-self: stretch;
}
nav a:nth-child(4){
  background-color: var(--orange);
  height: calc(var(--button-size) * 2);
  width: calc(var(--button-size) * 2);
  line-height: calc(var(--button-size) * 2);
  font-size: calc(var(--button-font-size) * 2.5);
  font-weight: bold;
}
nav a:nth-child(4):hover {
  background-color: var(--hover-orange);
}
form.search {
  margin: 30px auto;
  max-width: calc(var(--button-size) * 5);
}
form.search > * {
  height: 60px;
  border: none;
}
form.search input[type=text] {
  line-height: 60px;
  padding: 0 20px;
  font-size: 20px;
  float: left;
  width: 80%;
  background: #f1f1f1;
  border-top-left-radius: 60px;
  border-bottom-left-radius: 60px;
}
form.search button {
  float: left;
  width: 20%;
  padding: 10px;
  background: var(--orange);
  color: white;
  font-size: 17px;
  border-left: none;
  cursor: pointer;
  border-top-right-radius: 60px;
  border-bottom-right-radius: 60px;
}
form.search button:hover {
  background-color: var(--hover-orange);
}
.moviecard {
  min-height: calc(var(--movie-size) * 3);
  width: calc(var(--movie-size) * 4);
  flex-grow: 1;
  background-size: cover !important;
  text-align: left;
  text-shadow: 0 2px 5px black;
  margin: 5px;
}
.moviecard div {
  display: flex;
  align-items: flex-start;
  height: 100%;
  width: 100%;
  backdrop-filter: blur(8px) brightness(50%) saturate(125%);
  -webkit-backdrop-filter: blur(8px) brightness(50%) saturate(125%);
}
.moviecard object {
  align-self: center;
  max-height: calc(var(--movie-size) * 2.5);
  max-width: calc(var(--movie-size) * 2);
  margin: 1.17em;
  box-shadow: 0 2px 5px black;
}
.moviecard img {
  width: calc(var(--movie-size) * 1.69);
}
.moviecard h1, .moviecard p {
  padding-right: 1.67em;
}
.moviecard p {
  font-size: 0.75em;
}
#pages {
  display: none;
}
#pages a {
  color: white;
  text-decoration: none;
  padding: 4px;
}
footer {
  width: 100vw;
  height: var(--nav-height);
  background-color: var(--green);
}
footer p {
  margin: 0;
  line-height: var(--nav-height);
}
footer a {
  color: white;
  text-decoration: none;
  padding: calc(calc(var(--nav-height) / 2) - 10px) 4px;
  height: var(--nav-height);
}
footer a:hover {
  background-color: var(--hover-green);
}
</style>
