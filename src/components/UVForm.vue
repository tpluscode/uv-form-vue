<template>
  <b-tabs v-model="activeTab" :animated="false">
    <b-tab-item v-for="section in sections" :key="section.id" :label="section.label">
      <form class="form" @submit.prevent="onSubmit">
        <UVFormField
          v-for="field in section.fields"
          :key="field.term.value"
          :shape="field"
          :data="formData"
          :validationReport="validationReport"
          @change="onChange"
        />
        <button type="submit" class="button is-primary">Submit</button>
      </form>
    </b-tab-item>
  </b-tabs>

</template>

<script>
import clownface, { Clownface } from 'clownface'
import { rdf, rdfs, sh } from '@tpluscode/rdf-ns-builders'
import RDF from '@rdfjs/dataset'
import Validator from 'rdf-validate-shacl'
import UVFormField from './UVFormField.vue'

export default {
  name: 'UVForm',
  components: { UVFormField },

  props: {
    shape: Clownface,
    data: Clownface
  },

  data () {
    const getOrder = (term) => term.out(sh.order).value || Infinity

    const sections = this.shape.node(sh.PropertyGroup).in(rdf.type)
      .toArray()
      .sort((group1, group2) => getOrder(group1) - getOrder(group2))
      .map((section) => ({
        id: section.term.value,
        label: section.out(rdfs.label).value,
        fields: section.in(sh.group)
          .toArray()
          .sort((field1, field2) => getOrder(field1) - getOrder(field2))
      }))

    // Clone input data
    // TODO: When data changes, formData should be updated
    let formData = clownface({ dataset: RDF.dataset([...this.data.dataset]), term: this.data.term })

    // Create
    if (!this.data.term) {
      const resource = RDF.blankNode()
      formData = formData.node(resource)

      const resourceType = this.shape.out(sh.targetClass).term
      formData.addOut(rdf.type, resourceType)
    }

    return {
      activeTab: 0,
      sections,
      formData,
      validator: new Validator(this.shape.dataset),
      validationReport: null
    }
  },

  methods: {
    onSubmit () {
      this.$emit('submit', this.formData)
    },

    onChange (fieldShape, newValue) {
      // TODO: Handle other types of path
      const path = fieldShape.out(sh.path).term
      this.formData.deleteOut(path).addOut(path, RDF.literal(newValue))

      this.validationReport = this.validator.validate(this.formData.dataset)
    }
  }
}
</script>
