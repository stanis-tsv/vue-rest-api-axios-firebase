<template>
  <div class="card" v-if="!people.length">
    <button @click="$emit('loadPeople')" class="btn">Load people</button>
  </div>
  <div v-else>
    <div class="card" v-for="person in people" :key="person.id">
      <div v-if="person.isEditing">
        <input type="text" v-model="editName" v-focus="person.isEditing">
      </div>
      <h2 v-else>{{ person.firstName }}</h2>
      <button class="btn danger" @click="$emit('remove', person.id)">Delete</button>
      <button class="btn" @click="edit(person.id, person.firstName)">Edit name</button>
      <button 
        class="btn primary" 
        :disabled="!editName.length" 
        @click="$emit('save', person.id, editName)" 
        v-if="person.isEditing"
      >Save</button>
    </div>
  </div>
</template>

<script>

export default {
  emits: ['loadPeople', 'remove', 'save'],
  props: ['people'],
  data() {
    return {
      editName: ''
    }
  },
  methods: {
    edit(id, name) {
      this.people.find(p => p.isEditing = false)
      this.people.find(p => p.id === id).isEditing = true
      this.editName = name
    }
  }
}
</script>

<style>

</style>