<template>
  <div class="container my-5">
    <div class="card shadow-lg p-4">
      <h1 class="text-center text-muted mb-4">Spintax Variation Generator</h1>
      <textarea
        v-model="spintax"
        placeholder="Enter your spintax here..."
        rows="5"
        class="form-control mb-3"
      ></textarea>
      <div class="mb-3 d-flex align-items-center">
        <label for="count" class="form-label me-3">Number of Variations:</label>
        <input
          type="number"
          id="count"
          v-model.number="count"
          min="1"
          max="20"
          class="form-control w-25"
        />
      </div>
      <button @click="generateVariations" class="btn btn-primary w-100 mb-3">
        Generate Variations
      </button>
      <div v-if="errorMessage" class="alert alert-danger text-center">
        {{ errorMessage }}
      </div>
      <ul v-if="results.length > 0" class="list-group mt-4">
        <li
          v-for="(result, index) in results"
          :key="index"
          class="list-group-item mb-3 list-group-item-success d-flex align-items-center justify-content-between"
        >
          <span>{{ index + 1 }}. {{ result }}</span>
        </li>
      </ul>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
import axios from "axios";

export default defineComponent({
  setup() {
    const spintax = ref("");
    const count = ref(3);
    const results = ref<string[]>([]);
    const errorMessage = ref("");

    const generateVariations = async () => {
      errorMessage.value = "";
      results.value = [];
      try {
        const response = await axios.post("http://localhost:3000/api/spintax", {
          spintax: spintax.value,
          count: count.value,
        });
        results.value = response.data.variations;
      } catch (error: any) {
        errorMessage.value =
          error.response?.data?.message || "Something went wrong.";
      }
    };

    return {
      spintax,
      count,
      results,
      errorMessage,
      generateVariations,
    };
  },
});
</script>

<style scoped>
.card {
  border-radius: 12px;
  background: #f8f9fa;
  border: 1px solid #e0e0e0; /* Subtle border for the card */
}

textarea:focus,
input:focus {
  outline: none;
  box-shadow: 0 0 8px rgba(0, 123, 255, 0.5);
  border-color: #007bff; /* Change border color on focus */
}

.btn {
  background-color: #007bff; /* Primary button color */
  border: none;
  transition: background-color 0.3s, transform 0.2s;
}

.btn:hover {
  background-color: #0056b3; /* Darker shade on hover */
  transform: scale(1.02);
}

.list-group-item {
  border-radius: 6px;
  transition: background-color 0.3s;
}

.list-group-item:hover {
  background-color: #d4edda; /* Light green on hover */
}

.alert {
  border-radius: 6px;
  transition: opacity 0.3s;
}

.list-group {
  max-height: 300px; /* Limit height of results */
  overflow-y: auto; /* Scroll if too many results */
}
</style>