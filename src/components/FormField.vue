<template>
  <div class="field">
    <label v-if="fieldLabel" :for="fieldId" class="label">{{ fieldLabel }}</label>

    <div class="control">
      <div v-if="fieldType === 'select'" class="select">
        <b-select :id="fieldId" :value="fieldValue">
          <option></option>
          <option v-for="(choice, index) in choices" :key="index" :value="choice.value">
            {{ choice.label }}
          </option>
        </b-select>
      </div>
      <div v-else-if="fieldType === 'checkbox'">
        <div class="checkbox">
          <input :id="fieldId" :type="fieldType" :selected="fieldValue" />
        </div>
      </div>
      <div v-else-if="fieldType === 'text'">
        <textarea class="textarea" :id="fieldId" :value="fieldValue" />
      </div>
      <input v-else :id="fieldId" :type="fieldType" :value="fieldValue" class="input" />
    </div>
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
      fieldValue: '',
      choices: this.description.choices || []
    }
  }
}
</script>

<style scoped>
</style>
