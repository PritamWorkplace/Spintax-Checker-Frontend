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

      <div class="slider-demo-block text-center mb-3">
        <el-slider v-model="count" :min="1" :max="20" :step="1" show-input size="large" />
        <strong>Number of Variations: {{ count }}</strong>
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
import { defineComponent, ref } from 'vue'
import axios from 'axios'

export default defineComponent({
  setup() {
    const spintax = ref('')
    const count = ref(3)
    const results = ref<string[]>([])
    const errorMessage = ref('')

    const generateVariations = async () => {
      errorMessage.value = ''
      results.value = []
      try {
        const response = await axios.post('http://localhost:3000/api/spintax', {
          spintax: spintax.value,
          count: count.value,
        })
        results.value = response.data.variations
      } catch (error: any) {
        errorMessage.value =
          error.response?.data?.message ||
          error.response?.data?.errors[0]?.message ||
          'Something went wrong.'
      }
    }

    return {
      spintax,
      count,
      results,
      errorMessage,
      generateVariations,
    }
  },
})
</script>
