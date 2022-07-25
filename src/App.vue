<script setup>
import { ref, onMounted, computed } from 'vue'
const query = ref('')
//add to anime list
const my_anime = ref([])
//results gets back from query submited by API
const search_results = ref([])

// shows list from a-z
const my_anime_asc = computed(() => {
  return my_anime.value.sort((a, b) => {
    //converts into number tells which one comes first
    return a.title.localeCompare(b.title)
  })
})

//anime search function
const searchAnime = () => {
  //everything put in query , query will search in this function
 
  const url = `https://api.jikan.moe/v4/anime?q=${query.value}`
  fetch(url)
    //convert the result into a json
    .then(res => res.json())
    // pass the data back
    .then(data => {
      search_results.value = data.data
    })
}

// handles query inserted at the request field
const handleInput = (e) => {
  //if we dont have the target value
  if (!e.target.value) {
    //return a empty array and reset into nothing
    search_results.value = []
  }
}

//adding a anime to the list
const addAnime = (anime) => {
  search_results.value = []
  //close the search box if you add a anime
  query.value = ''

  my_anime.value.push({
    id: anime.mal_id,
    title: anime.title,
    image: anime.images.jpg.image_url,
    total_episodes: anime.episodes,
    watched_episodes: 0,
  })

  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

const increasewatch = (anime) => {
  //add episode
  anime.watched_episodes++

  //add episode and save the list
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

const decreasewatch = (anime) => {
  //remove episode
  anime.watched_episodes--

  //removes episode and save list
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

//check local storage and add it or else initialize empty array
onMounted(() => {
  my_anime.value = JSON.parse(localStorage.getItem('my_anime')) || []
})
</script>

<template>
  <main>
    <h1>My anime tracker</h1>

    <form @submit.prevent="searchAnime">
      <input 
      type="text" 
      placeholder="Search for an anime" 
      v-model="query" 
      @input="handleInput"
      />
      <button type="submit">Search</button>
    </form>

    <div class="results" v-if="search_results.length > 0">
      <div class="result" v-for="anime in search_results">
      <img :src="anime.images.jpg.image_url"/>
        <div class="details">
          <h3>{{ anime.title }}</h3>
        </div>
      </div>
    </div>
  </main>
</template>

<style>
</style>
