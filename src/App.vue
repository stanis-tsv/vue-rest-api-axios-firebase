<template>
  <div class="container">
    <form class="card" @submit.prevent="createPerson">
      <h1>Create person:</h1>
      <input type="text" v-model="name" v-focus>
      <button class="btn" :disabled="!name.length">Create</button>
    </form>
    <div class="loader" v-if="loading"></div>
    <p class="card" v-if="noData">No people yet, create one</p>
    <People 
      :people="people" 
      @loadPeople="loadPeople" 
      @remove="remove" 
      @save="save" 
    />
  </div>
</template>

<script>
import axios from 'axios'
import People from './components/People'

export default {
  components: {People},

  data() {
    return {
      name: '',
      people: [],
      loading: false,
      noData: false
    }
  },
  methods: {
    async createPerson() {
      const {data} = await axios.post('https://vue-rest-api-75ecc-default-rtdb.firebaseio.com/people.json', {
        firstName: this.name,
        isEditing: false
      })
      this.people.push({
        id: data.name,
        firstName: this.name,
        isEditing: false
      })
      this.name = ''
      this.noData = false
    },

    async loadPeople() {
      this.loading = true
      const {data} = await axios.get('https://vue-rest-api-75ecc-default-rtdb.firebaseio.com/people.json')
      if (!data) {
        this.noData = true
        this.loading = false  
      }
      this.people = Object.keys(data).map(p => {
        return {
          id: p,
          ...data[p]
        }
      })
      this.loading = false  
    },

    async remove(id) {
      await axios.delete(`https://vue-rest-api-75ecc-default-rtdb.firebaseio.com/people/${id}.json`)
      this.people = this.people.filter(p => p.id !== id)
    },

    async save(id, newName) {
      await axios.put(`https://vue-rest-api-75ecc-default-rtdb.firebaseio.com/people/${id}.json`, {
        firstName: newName,
        isEditing: false
      })
      this.people.find(p => {
        if (p.id === id) {
          p.firstName = newName,
          p.isEditing = false
        }
      }) 
    }

  }
}
</script>

<style>
body {
  color: lightslategray;
  background: lightseagreen;
}
.container {
  margin: 0 auto;
  max-width: 800px;
  text-align: center;
}
.card {
  padding: 20px;
  margin-bottom: 20px;
  border-radius: 20px;
  box-shadow: 2px 3px 10px rgba(0, 0, 0, 0.2);
  background: #fff;
}
input {
  margin: 0 auto 20px;
  outline: none;
  border: 2px solid #8c9baa;
  display: block;
  width: 90%;
  border-radius: 20px;
  font-size: 20px;
  padding: 5px 20px;
  color: lightslategray;
}
.btn {
  color: lightslategray;
  border-radius: 20px;
  border: 2px solid lightblue;
  text-transform: uppercase;
  margin: 5px 10px;
  padding: 10px 40px;
  background: transparent;
  transition: all .3s;
  cursor: pointer;
  font-weight: bold;
}
.btn:hover {
  background: lightblue;
  color: #fff;
}
.danger {
  border-color: lightcoral;
}
.danger:hover {
  background: lightcoral;
  color: #fff;
}
.primary {
  border-color: lightgreen;
}
.primary:hover {
  background: lightgreen;
  color: #fff;
}

.loader {
  margin: 30px auto;
  border: 5px solid #f3f3f3;
  border-top: 5px solid lightseagreen;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  animation: spin 1s linear infinite;
}
@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
</style>