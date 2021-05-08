<template>
  <div class="about">
    <input
        v-model="inputValue"
    />
    <h3>Привет {{ setText }}</h3>
    <hr/>
    <button
        @click="fetchDogs"
    >Fetch dogs</button>
    <button @click="clearBreeds">Clear breeds</button>
    <div v-for="breed in breeds">
      <p>{{ breed.name }}</p>
      <p class="red"
         v-if="breed.subBreeds.length"
         v-for="subBreed in breed.subBreeds"
      >{{ subBreed }}</p>
    </div>
    <hr/>
    <div>
      <button
          @click="randomBreed"
      >Random breed</button>
      <div v-for="breed in randomBreedVar">
        <p>{{ breed.name }}</p>
        <p class="red"
           v-if="breed.subBreeds.length"
           v-for="subBreed in breed.subBreeds"
        >{{ subBreed }}</p>
      </div>
    </div>
  </div>
  <hr/>
  <div>
    <button
        @click="addRandom"
    >Add random breed</button>
    <button
        @click="deleteRandomBreed"
    >Delete random breed</button>
    <div
        v-for="breed in randomBreeds"
    >{{breed.name}}</div>
  </div>
  <div style="width: 100%" class="container container-lists">
    <div class="item">
      <List
          :data="breeds"
          :on-click-item="selectBreed"
          :checked="(name) => selectedBreeds.find((item) => item.name === name)"
          :color-checked="{backgroundColor: 'blue', color: 'white'}"
      />
    </div>
    <div class="item">
      <List
          v-bind:data="selectedBreeds"
          v-bind:on-click-item="unselectBreed"
      />
    </div>
  </div>
</template>

<script>
import List from './List'

export default {
  data() {
    return {
      inputValue: '',
      breeds: [],
      randomBreedVar: [],
      // randomBreeds: [],
      randomBreeds: [],
      selectedBreeds: []
    }
  },
  components: {
    List
  },
  methods: {
    convert(data){
      return Object.entries(data).map(item => ({name: item[0], subBreeds: item[1]}))
    },
    fetchDogs(){
      fetch('https://dog.ceo/api/breeds/list/all')
          .then(response => response.json())
          .then(json => this.breeds = this.convert(json.message))
    },
    randomBreed(){
      fetch('https://dog.ceo/api/breeds/list/all/random')
          .then(response => response.json())
          .then(json => this.randomBreedVar = this.convert(json.message))
    },
    clearBreeds(){
      this.breeds = []
    },
    addRandom(){
      fetch('https://dog.ceo/api/breeds/list/all/random')
          .then(response => response.json())
          .then(json => this.randomBreeds = this.randomBreeds.concat(this.convert(json.message)))
    },
    randomNumberGenerator(max){
      return Math.floor(Math.random() * max)
    },
    deleteRandomBreed(){
      let number = this.randomNumberGenerator(this.randomBreeds.length)
      this.randomBreeds = [
        ...this.randomBreeds.slice(0, number),
        ...this.randomBreeds.slice(number + 1)
      ];
    },
    selectBreed(name) {
      return this.selectedBreeds = [...this.selectedBreeds, this.breeds.find((item) => item.name === name)];
    },
    unselectBreed(name) {
      return this.selectedBreeds = this.selectedBreeds.filter((item) => item.name !== name);
    }
  },
  computed: {
    setText(){
      if(this.inputValue.trim()){
        return this.inputValue
      } else {
        return "незнакомец"
      }
    }
  }
}
</script>

<style>
.red {
  color: red;
}
button {
  margin: 20px;
}
.container-lists {
  display: flex;
  justify-content: center;
}
.item {
  width: 150px;
  padding: 20px;
}
</style>
