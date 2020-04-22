<template>
  <div class="section">
    <h1 class="title">Form demo</h1>

    <b-tabs v-model="activeTab" :animated="false">
      <b-tab-item v-for="step in steps" :key="step.id" :label="step.name">
        <Form :description="step" />
      </b-tab-item>
    </b-tabs>
  </div>
</template>

<script>
import Form from '@/components/Form.vue'

export default {
  name: 'Home',

  components: {
    Form
  },

  data: () => ({
    activeTab: 0,
    steps: []
  }),

  async mounted () {
    const response = await fetch('forms.json')
    this.steps = await response.json()
  }
}
</script>
