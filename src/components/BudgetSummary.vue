<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';
    const budgets = ref([]);
    const item = ref({ name: '', price: 0, budgetId: null });

    onMounted(async () => {
      const response = await axios.get('http://localhost:8080/budgets');
      budgets.value = response.data;
    });

    const submitItem = async () => {
      await axios.post('/api/items', item.value);
      alert('Item added successfully');
    };
</script>

<template>
  <form @submit.prevent="submitItem">
    <label for="itemName">Item Name</label>
    <input v-model="item.name" id="itemName" placeholder="Enter item name" />

    <label for="itemPrice">Price</label>
    <input v-model.number="item.price" id="itemPrice" type="number" placeholder="Enter price" />

    <label for="budget">Budget</label>
    <select v-model="item.budgetId" id="budget">
      <option v-for="budget in budgets" :key="budget.id" :value="budget.id">
        {{ budget.name }} ({{ budget.category }})
      </option>
    </select>

    <button type="submit">Add Item</button>
  </form>
</template>

