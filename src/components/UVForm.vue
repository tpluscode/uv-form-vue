<template>
  <b-tabs v-model="activeTab" :animated="false">
    <b-tab-item v-for="section in sections" :key="section.id" :label="section.label">
      <form class="form" @submit.prevent="onSubmit">
        <UVFormField
          v-for="field in section.fields"
          :key="field.shape.term.value"
          :shape="field.shape"
          :data="field.values"
          :change-callback="field.changeCallback"
          :validationResults="field.validationResults"
        />
        <button type="submit" class="button is-primary">Submit</button>
      </form>
    </b-tab-item>
  </b-tabs>

</template>

<script>
import { Clownface } from 'clownface'
import UVFormField from './UVFormField.vue'

export default {
  name: 'UVForm',
  components: { UVFormField },

  props: {
    data: Clownface
  },

  data () {
    return {
      activeTab: 0,
      sections: []
    }
  },

  methods: {
    onSubmit () {
      this.$emit('submit')
    },

    addField ({ shape, changeCallback, values, validationResults }) {
      const sectionKey = shape.group ? shape.group.id.value : ''

      let section = this.sections.find(s => s.id === sectionKey)
      if (!section) {
        section = {
          id: sectionKey,
          label: shape.group ? shape.group.label : 'default',
          fields: []
        }
        this.sections.push(section)
      }

      const index = section.fields.findIndex(field => field.shape.term.equals(shape.id))
      const field = {
        shape: shape._selfGraph,
        changeCallback,
        values,
        validationResults
      }
      if (index >= 0) {
        section.fields.splice(index, 1, field)
      } else {
        section.fields.push(field)
      }
    }
  }
}
</script>
