<template>
  <div class="sandwich-generator">
    <h1>Générateur de Sandwich</h1>
    
    <div class="generator-section">
      <button @click="generateSandwich">Générer un sandwich</button>
      
      <div v-if="currentSandwich" class="current-sandwich">
        <h2>Sandwich généré :</h2>
        <ul>
          <li>Bread : {{ currentSandwich.bread }}</li>
          <li>Sauce : {{ currentSandwich.sauce }}</li>
          <li>Cheese : {{ currentSandwich.cheese }}</li>
          <li>Filling : {{ currentSandwich.filling }}</li>
        </ul>
        <button @click="saveSandwich">Sauvegarder</button>
      </div>
    </div>

    <div class="saved-section" v-if="savedSandwiches.length > 0">
      <ul>
        <li v-for="sandwich in savedSandwiches" :key="sandwich.id" class="saved-sandwich">
          <div>
            Bread : {{ sandwich.bread }} | Sauce : {{ sandwich.sauce }} | Cheese : {{ sandwich.cheese }} | Filling : {{ sandwich.filling }}
          </div>
          <button @click="deleteSandwich(sandwich.id)">Supprimer</button>
        </li>
      </ul>
    </div>
      <h2>Nombre de sandwichs sauvegardés : {{ savedSandwiches.length }}</h2>

  </div>  
</template>

<script setup lang="ts">
import { ref } from 'vue'

interface Sandwich {
  id: number;
  bread: string;
  sauce: string;
  cheese: string;
  filling: string;
}

const breads = ['Baguette', 'Ciabatta', 'Pain de seigle', 'Wrap', 'Pain aux céréales']
const sauces = ['Mayonnaise', 'Moutarde', 'Pesto', 'Sauce barbecue', 'Sauce aigre-douce']
const cheeses = ['Cheddar', 'Mozzarella', 'Gouda', 'Emmental', 'Roquefort']
const fillings = ['Jambon', 'Poulet', 'Saumon', 'Thon', 'Bacon']

const currentSandwich = ref<Sandwich | null>(null)
const savedSandwiches = ref<Sandwich[]>([])

let nextId = 1

const getRandomItem = (array: string[]) => {
  return array[Math.floor(Math.random() * array.length)]
}

const generateSandwich = () => {
  currentSandwich.value = {
    id: 0, 
    bread: getRandomItem(breads),
    sauce: getRandomItem(sauces),
    cheese: getRandomItem(cheeses),
    filling: getRandomItem(fillings),
  }
}

const saveSandwich = () => {
  if (currentSandwich.value) {
    const isDuplicate = savedSandwiches.value.some(s => 
      s.bread === currentSandwich.value!.bread &&
      s.sauce === currentSandwich.value!.sauce &&
      s.cheese === currentSandwich.value!.cheese &&
      s.filling === currentSandwich.value!.filling
    )

    if (isDuplicate) {
      alert("Ce sandwich a déjà été sauvegardé !")
    }

    savedSandwiches.value.push({
      ...currentSandwich.value,
      id: nextId++
    })
  }
}

const deleteSandwich = (id: number) => {
  savedSandwiches.value = savedSandwiches.value.filter(s => s.id !== id)
}
</script>


<style scoped>
.sandwich-generator {
  font-family: Arial;
  padding: 10px;
  max-width: 600px;
  margin: 0 auto;
}

.generator-section,
.saved-section {
  margin-bottom: 20px;
}

.current-sandwich,
.saved-sandwich {
  padding: 10px;
  border: 1px solid ;
  margin-top: 10px;
}

ul {
  list-style: none;
  padding-left: 0;
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


</style>