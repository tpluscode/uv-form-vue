<template>
  <form class="form">
    <FormField v-for="field in fields" :key="field.id" :description="field" />
  </form>
</template>

<script>
import FormField from './FormField.vue'

export default {
  name: 'Form',
  components: {
    FormField
  },
  props: {
    description: Object
  },
  data () {
    return {
      fields: this.description.fields || []
    }
  }
}
</script>

<style scoped>
</style>
