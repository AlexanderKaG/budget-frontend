<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";
import BudgetService from "./components/BudgetSummary.vue";

const items = ref([]); // Holds the fetched items
const newItem = ref({ name: "", price: "", category: "" }); // Holds new item form data
const isLoading = ref(true); // Loader state
const errorMessage = ref(null); // Holds error messages (if any)

// API Base URL (adjust based on your backend)
const API_BASE_URL = "http://localhost:8080/items";

// Function to fetch items
const fetchItems = async () => {
  try {
    const response = await axios.get(API_BASE_URL); // Replace with your API URL
    items.value = response.data; // Assign fetched data to items
  } catch (error) {
    errorMessage.value = "Failed to fetch items. Please try again later.";
    console.error(error);
  } finally {
    isLoading.value = false; // Stop loader
  }
};

// Function to create a new item
const createItem = async () => {
  if (!newItem.value.name || !newItem.value.price || !newItem.value.category) {
    alert("Please fill out all fields.");
    return;
  }

  try {
    const response = await axios.post(API_BASE_URL, newItem.value); // POST request
    items.value.push(response.data); // Add the new item to the list
    console.log(response.data);
    alert("Item created successfully!");
    resetForm(); // Clear the form
  } catch (error) {
    console.error("Create Error:", error);
    alert("Failed to create the item. Please try again.");
  }
};

// Function to reset the form
const resetForm = () => {
  newItem.value = { name: "", price: "", category: "" };
};

// Call fetchItems when the component is mounted
onMounted(fetchItems);
</script>

<template>
  <BudgetService></BudgetService>
  <div>
    <h1>Item List</h1>

    <!-- If loading, display a spinner -->
    <p v-if="isLoading">Loading items...</p>

    <!-- Display error message, if any -->
    <p v-if="errorMessage" class="error">{{ errorMessage }}</p>

    <!-- Display items in a list -->
    <ul v-else>
      <li v-for="(item, index) in items" :key="index">
        <strong>{{ item.name }}</strong> - ${{ item.price }} <em>({{ item.category }})</em>
      </li>
    </ul>

    <h2>Create New Item</h2>
    <!-- Item creation form -->
    <form @submit.prevent="createItem">
      <div>
        <label for="name">Name:</label>
        <input
          id="name"
          v-model="newItem.name"
          type="text"
          placeholder="Item Name"
          required
        />
      </div>
      <div>
        <label for="price">Price:</label>
        <input
          id="price"
          v-model="newItem.price"
          type="number"
          step="0.01"
          placeholder="Item Price"
          required
        />
      </div>
      <div>
        <label for="category">Category:</label>
        <input
          id="category"
          v-model="newItem.category"
          type="text"
          placeholder="Item Category"
          required
        />
      </div>
      <button type="submit">Create Item</button>
      <button type="button" @click="resetForm">Reset</button>
    </form>
  </div>
</template>

<style scoped>
/* Add some basic styling */
.error {
  color: red;
  font-weight: bold;
}
ul {
  list-style: none;
  padding: 0;
}
li {
  margin: 8px 0;
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
}
</style>
