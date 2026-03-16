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

    <input type="text" v-model="filterIngredient" placeholder="Filtrer par ingrédient"/>

    <div class="saved-section" v-if="filteredSandwiches.length > 0">
      <ul>
        <li v-for="sandwich in filteredSandwiches" :key="sandwich.id" class="saved-sandwich">
          <div>
            Bread : {{ sandwich.bread }} | Sauce : {{ sandwich.sauce }} | Cheese : {{ sandwich.cheese }} | Filling : {{ sandwich.filling }}
          </div>
          <button @click="deleteSandwich(sandwich.id)">Supprimer</button>
        </li>
      </ul>
    </div>
      <h2>Nombre de sandwichs sauvegardés : {{ savedSandwiches.length }}</h2>

    <hr />

    <div class="ingredient-manager">
      <h2>Gestion des ingrédients</h2>
      <div class="manager-grid">
        <IngredientCategory 
          title="Pains" 
          itemName="pain" 
          :ingredients="breads" 
          @add="(val) => addIngredient(breads, val)" 
          @remove="(index) => removeIngredient(breads, index)" 
        />
        <IngredientCategory 
          title="Sauces" 
          itemName="sauce" 
          :ingredients="sauces" 
          @add="(val) => addIngredient(sauces, val)" 
          @remove="(index) => removeIngredient(sauces, index)" 
        />
        <IngredientCategory 
          title="Fromages" 
          itemName="fromage" 
          :ingredients="cheeses" 
          @add="(val) => addIngredient(cheeses, val)" 
          @remove="(index) => removeIngredient(cheeses, index)" 
        />
        <IngredientCategory 
          title="Garnitures" 
          itemName="garniture" 
          :ingredients="fillings" 
          @add="(val) => addIngredient(fillings, val)" 
          @remove="(index) => removeIngredient(fillings, index)" 
        />
      </div>
    </div>

  </div>  
</template>

<script setup lang="ts">
import { computed, onMounted, ref } from 'vue'
import IngredientCategory from '../components/IngredientCategory.vue'

interface Sandwich {
  id: number;
  bread: string;
  sauce: string;
  cheese: string;
  filling: string;
}

const breads = ref(['Baguette', 'Ciabatta', 'Pain de seigle', 'Wrap', 'Pain aux céréales'])
const sauces = ref(['Mayonnaise', 'Moutarde', 'Pesto', 'Sauce barbecue', 'Sauce aigre-douce'])
const cheeses = ref(['Cheddar', 'Mozzarella', 'Gouda', 'Emmental', 'Roquefort'])
const fillings = ref(['Jambon', 'Poulet', 'Saumon', 'Thon', 'Bacon'])

const addIngredient = (category: string[], newItem: string) => {
  if (newItem.trim()) {
    category.push(newItem.trim())
  }
}

const removeIngredient = (category: string[], index: number) => {
  category.splice(index, 1)
}

const currentSandwich = ref<Sandwich | null>(null)
const savedSandwiches = ref<Sandwich[]>([])

let nextId = 1

const filterIngredient = ref("")

const filteredSandwiches = computed(() => {
  if (!filterIngredient.value) {
    return savedSandwiches.value
  }

  return savedSandwiches.value.filter(s =>
    s.bread.toLowerCase().includes(filterIngredient.value.toLowerCase()) ||
    s.sauce.toLowerCase().includes(filterIngredient.value.toLowerCase()) ||
    s.cheese.toLowerCase().includes(filterIngredient.value.toLowerCase()) ||
    s.filling.toLowerCase().includes(filterIngredient.value.toLowerCase())
  )
})

const getRandomItem = (array: string[]) => {
  if (array.length === 0) return 'Aucun'
  return array[Math.floor(Math.random() * array.length)]
}

const generateSandwich = () => {
  currentSandwich.value = {
    id: 0, 
    bread: getRandomItem(breads.value),
    sauce: getRandomItem(sauces.value),
    cheese: getRandomItem(cheeses.value),
    filling: getRandomItem(fillings.value),
  }
}

onMounted(() => {
  const storedSandwiches = localStorage.getItem("sandwiches")

  if (storedSandwiches) {
    savedSandwiches.value = JSON.parse(storedSandwiches)
  }
})

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
      return
    }

    savedSandwiches.value.push({
      ...currentSandwich.value,
      id: nextId++
    })

    localStorage.setItem(
      "sandwiches",
      JSON.stringify(savedSandwiches.value)
    )
  }
}

const deleteSandwich = (id: number) => {
  savedSandwiches.value = savedSandwiches.value.filter(s => s.id !== id)
  localStorage.setItem(
  "sandwiches",
  JSON.stringify(savedSandwiches.value)
)
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

hr {
  margin: 30px 0;
  border: 0;
  border-top: 1px solid #ccc;
}

.ingredient-manager {
  margin-top: 20px;
}

.manager-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
}

</style>