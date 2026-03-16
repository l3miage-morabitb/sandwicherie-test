<template>
  <div class="category">
    <h3>{{ title }}</h3>
    <ul>
      <li v-for="(item, index) in ingredients" :key="index">
        {{ item }} <button class="delete-btn" @click="$emit('remove', index)">X</button>
      </li>
    </ul>
    <div class="add-form">
      <input v-model="newItem" :placeholder="'Nouveau ' + itemName" @keyup.enter="handleAdd" />
      <button @click="handleAdd">Ajouter</button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const props = defineProps<{
  title: string
  itemName: string
  ingredients: string[]
}>()

const emit = defineEmits<{
  (e: 'add', item: string): void
  (e: 'remove', index: number): void
}>()

const newItem = ref('')

const handleAdd = () => {
  if (newItem.value.trim()) {
    emit('add', newItem.value.trim())
    newItem.value = ''
  }
}
</script>

<style scoped>
.category {
  border: 1px solid #ddd;
  padding: 15px;
  border-radius: 8px;
  background-color: #f9f9f9;
}

.category h3 {
  margin-top: 0;
  margin-bottom: 15px;
}

ul {
  list-style: none;
  padding-left: 0;
}

.category li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 5px;
  padding-bottom: 5px;
  border-bottom: 1px solid #eee;
}

button { 
  padding: 8px 16px; 
  cursor: pointer; 
  background-color: #007bff; 
  color: white; 
  border: none; 
  border-radius: 4px; 
  font-size: 14px; 
}

button:hover { 
  background-color: #0056b3; 
}

.delete-btn {
  background-color: #dc3545;
  padding: 2px 8px;
  font-size: 12px;
}

.delete-btn:hover {
  background-color: #c82333;
}

.add-form {
  display: flex;
  gap: 5px;
  margin-top: 15px;
}

.add-form input {
  flex: 1;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.add-form button {
  padding: 8px 12px;
}
</style>
