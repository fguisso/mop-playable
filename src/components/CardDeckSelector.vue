<template>
  <div class="min-h-screen bg-gray-100 p-8">
    <!-- Search Filters -->
    <div class="mb-8 bg-white p-6 rounded-lg shadow">
      <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
        <input
          v-model="filters.name"
          placeholder="Search by name"
          class="p-2 border rounded"
        />
        <input
          v-model="filters.cost"
          placeholder="Search by cost"
          class="p-2 border rounded"
        />
        <input
          v-model="filters.type"
          placeholder="Search by type"
          class="p-2 border rounded"
        />
        <input
          v-model="filters.edition"
          placeholder="Search by edition"
          class="p-2 border rounded"
        />
        <input
          v-model="filters.description"
          placeholder="Search in description"
          class="p-2 border rounded"
        />
      </div>
    </div>

    <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
      <!-- Available Cards -->
      <div class="col-span-2">
        <h2 class="text-2xl font-bold mb-4">Available Cards</h2>
        <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
          <div
            v-for="card in filteredCards"
            :key="card.id"
            class="bg-white rounded-lg shadow overflow-hidden hover:shadow-lg transition-shadow"
          >
            <img 
              :src="`/images/${card.image}`" 
              :alt="card.name" 
              class="w-full h-auto"
              @error="handleImageError"
            />
            <div class="p-4">
              <div class="flex justify-between items-start mb-2">
                <h3 class="font-bold">{{ card.name }}</h3>
                <span class="text-gray-600">{{ card.cost }}</span>
              </div>
              <p class="text-sm text-gray-600 mb-2">{{ card.type }}</p>
              <p class="text-sm text-gray-500 mb-4">{{ card.edition }}</p>
              <p class="text-sm mb-4">{{ card.description }}</p>
              <button
                @click="addToDeck(card)"
                :disabled="!canAddCard(card)"
                class="w-full bg-blue-500 text-white py-2 px-4 rounded hover:bg-blue-600 disabled:bg-gray-300 disabled:cursor-not-allowed"
              >
                Add to Deck ({{ getCardCount(card) }}/4)
              </button>
            </div>
          </div>
        </div>
      </div>

      <!-- Current Deck -->
      <div class="bg-white p-6 rounded-lg shadow">
        <h2 class="text-2xl font-bold mb-4">
          Current Deck ({{ currentDeck.length }}/60)
        </h2>
        <div class="space-y-4">
          <div
            v-for="(card, index) in currentDeck"
            :key="index"
            class="flex justify-between items-center p-2 bg-gray-50 rounded"
          >
            <div class="flex items-center">
              <img 
                :src="`/images/${card.image}`" 
                :alt="card.name" 
                class="w-10 h-10 object-cover rounded mr-2"
                @error="handleImageError"
              />
              <div>
                <span class="font-medium">{{ card.name }}</span>
                <span class="text-sm text-gray-600 ml-2">{{ card.cost }}</span>
              </div>
            </div>
            <button
              @click="removeFromDeck(index)"
              class="text-red-500 hover:text-red-600"
            >
              Remove
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import cardsData from '../../public/cards.json'

export default {
  name: 'CardDeckSelector',
  data() {
    return {
      cards: [],
      currentDeck: [],
      filters: {
        name: '',
        cost: '',
        type: '',
        edition: '',
        description: ''
      }
    }
  },
  computed: {
    filteredCards() {
      return this.cards.filter(card => {
        const nameMatch = card.name.toLowerCase().includes(this.filters.name.toLowerCase())
        const costMatch = card.cost.toLowerCase().includes(this.filters.cost.toLowerCase())
        const typeMatch = card.type.toLowerCase().includes(this.filters.type.toLowerCase())
        const editionMatch = card.edition.toLowerCase().includes(this.filters.edition.toLowerCase())
        const descriptionMatch = card.description.toLowerCase().includes(this.filters.description.toLowerCase())

        return nameMatch && costMatch && typeMatch && editionMatch && descriptionMatch
      })
    }
  },
  methods: {
    handleImageError(e) {
      // Replace broken image with a placeholder
      e.target.src = 'https://placehold.co/400x600?text=Card+Image+Not+Found'
    },
    getCardCount(card) {
      return this.currentDeck.filter(c => c.name === card.name).length
    },
    canAddCard(card) {
      if (this.currentDeck.length >= 60) return false
      
      const cardCount = this.getCardCount(card)
      return cardCount < 4
    },
    addToDeck(card) {
      if (this.canAddCard(card)) {
        this.currentDeck.push(card)
      }
    },
    removeFromDeck(index) {
      this.currentDeck.splice(index, 1)
    }
  },
  mounted() {
    // Load cards from the imported JSON file
    this.cards = cardsData.cards || []
  }
}
</script>
