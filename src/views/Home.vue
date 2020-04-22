<template>
  <div class="section">
    <div class="level">
      <div class="level-item-left">
        <h1 class="title">Docuteam curator demo</h1>
      </div>
      <div class="level-item-right">
        <b-button
          type="is-white"
          icon-left="search"
          @click="search"
          v-shortkey="['ctrl', 'f']"
          @shortkey="search"
        >
          Search
        </b-button>
      </div>
    </div>

    <b-tabs v-model="activeTab" :animated="false">
      <b-tab-item v-for="step in steps" :key="step.id" :label="step.name">
        <Form :description="step" />
      </b-tab-item>
    </b-tabs>

    <b-modal
      :active.sync="showSearch"
      has-modal-card
      trap-focus
      aria-role="dialog"
      aria-modal>
      <SearchForm />
    </b-modal>
  </div>
</template>

<script>
import Form from '@/components/Form.vue'
import SearchForm from '@/components/SearchForm.vue'

export default {
  name: 'Home',

  components: {
    Form,
    SearchForm
  },

  data: () => ({
    activeTab: 0,
    steps: [],
    showSearch: false
  }),

  async mounted () {
    const response = await fetch('forms.json')
    this.steps = await response.json()
  },

  methods: {
    search () {
      this.showSearch = true
    }
  }
}
</script>
