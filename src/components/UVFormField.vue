<template>
  <div class="field">
    <label v-if="fieldLabel" :for="fieldId" class="label">{{ fieldLabel }}</label>

    <div class="control">
      <div v-if="fieldType === 'select'" class="select">
        <b-select :id="fieldId" :value="fieldValue" @change="onChange">
          <option></option>
          <option v-for="(choice, index) in choices" :key="index" :value="choice.value">
            {{ choice.label }}
          </option>
        </b-select>
      </div>
      <div v-else-if="fieldType === 'checkbox'">
        <div class="checkbox">
          <input :id="fieldId" :type="fieldType" :checked="fieldValue" :required="isRequired" @change="onChange" />
        </div>
      </div>
      <div v-else-if="fieldType === 'text'">
        <textarea class="textarea" :id="fieldId" :value="fieldValue" :required="isRequired" @change="onChange" />
      </div>
      <input v-else :id="fieldId" :type="fieldType" :value="fieldValue" class="input" :required="isRequired" @change="onChange" />
    </div>

    <p v-if="validationMessage" class="help is-danger">{{ validationMessage }}</p>
  </div>
</template>

<script>
import { Clownface } from 'clownface'
import RDF from '@rdfjs/data-model'
import { rdfs } from '@tpluscode/rdf-ns-builders'

export default {
  name: 'UVFormField',
  components: {},
  props: {
    shape: { type: Clownface, required: true },
    data: { type: Clownface, required: true },
    validationResults: { required: true },
    changeCallback: { required: true }
  },

  data () {
    const label = this.shape.out(rdfs.label).value
    const description = this.shape.out(rdfs.comment).value
    // const minCount = this.shape.out(sh.minCount).value || 0
    // const maxCount = this.shape.out(sh.maxCount).value || Infinity

    const validationMessage = this.validationResults && this.validationResults.map(vr => vr.message).join(', ')

    return {
      fieldType: 'string',
      fieldId: '',
      fieldLabel: label || description || '',
      fieldValue: this.data.value || '',
      // isRequired: minCount > 0,
      isRequired: false,
      choices: [],
      validationMessage
    }
  },

  methods: {
    onChange (e) {
      if (e.target.value) {
        this.changeCallback(RDF.literal(e.target.value))
      } else {
        this.changeCallback(null)
      }
    }
  }
}
</script>
