<template>
  <div class="field">
    <label v-if="fieldLabel" :for="fieldId" class="label">{{ fieldLabel }}</label>
    <input :id="fieldId" :type="fieldType" :value="fieldValue" class="input" />
  </div>
</template>

<script>
export default {
  name: 'FormField',
  components: {
  },
  props: {
    description: Object
  },
  data () {
    return {
      fieldType: this.description.type || 'text',
      fieldLabel: this.description.label || '',
      fieldId: this.description.id,
      fieldValue: ''
    }
  }
}
</script>

<style scoped>
</style>
