<template>
  <div class="container">
    <form class="card" @submit.prevent="createPerson">
      <h2>Работа с базой данных</h2>

      <div class="form-control">
        <label for="name">Введите имя</label>
        <input type="text" id="name" v-model.trim="name">
      </div>

      <button class="btn primary" :disabled="name.length === 0">Создать человека</button>
    </form>

    <app-people-list 
      :people="people"
      @load="loadPeople"
      @remove="removePeople"
    ></app-people-list>
  </div>
  

  
</template>

<script>
import AppPeopleList from './AppPeopleList.vue'
import axios from 'axios'

export default {
  data() {
    return {
      name: '',
      people: []
    }
  },
  mounted() {
    this.loadPeople()
  },
  methods: {
    async createPerson() {
      const response = await fetch('https://vue-with-http-f9ba5-default-rtdb.firebaseio.com/people.json', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          firstName: this.name
        })
      })

      const firebaseData = await response.json()

      this.people.push({
        firstName: this.name,
        id: firebaseData.name
      })

      this.name = ''
    },
    async loadPeople() {
      const {data} = await axios.get('https://vue-with-http-f9ba5-default-rtdb.firebaseio.com/people.json')
      this.people = Object.keys(data).map(key => {
        return {
          id: key,
          ...data[key]
        }
      })
    },
    removePeople() {
      
    }
  },
  components: {AppPeopleList}
}
</script>