<template>
  <div class="columns">
    <div class="column">
      <h2 class="title is-size-4">Form</h2>
      <div ref="form"></div>
    </div>
    <div class="column is-size-7">
      <h2 class="title is-size-4">Data</h2>
      <div v-if="data">
        Pointer: {{ data.term ? data.term.value : '-' }}
        <table class="table is-fullwidth">
          <thead>
            <th>subject</th>
            <th>predicate</th>
            <th>object</th>
          </thead>
          <tbody>
            <tr v-for="({subject, predicate, object}, index) in storedQuads" :key="index">
              <td>{{ subject.value }}</td>
              <td>{{ predicate.value }}</td>
              <td>{{ object.value }}</td>
            </tr>
          </tbody>
        </table>
      </div>
      <p v-else>No data</p>
    </div>
  </div>
</template>

<script>
import fetch from '@rdfjs/fetch'
import clownface from 'clownface'
import { rdf, sh } from '@tpluscode/rdf-ns-builders'
import { uvForm } from '@tpluscode/uv-form/src/uvForm'
import { Matcher } from '@tpluscode/uv-form/example/matcher'
import Validator from 'rdf-validate-shacl'
import RDF from '@rdfjs/dataset'
import UVForm from '@/components/UVForm.vue'
import Vue from 'vue'

var FormClass = Vue.extend(UVForm)

class VueRenderer {
  constructor () {
    this.form = new FormClass()
  }

  append (params) {
    this.form.addField(params)
  }

  getResult () {
    return this.form
  }
}

export default {
  name: 'ShapeForm',

  data () {
    return {
      shape: null,
      data: null,
      storedQuads: null,
      changeListener: null,
      validator: null,
      renderer: new VueRenderer()
    }
  },

  async mounted () {
    const response = await fetch('object-shape.ttl')
    const shapes = await response.dataset()
    this.shape = clownface({ dataset: shapes })
      .node(sh.NodeShape)
      .in(rdf.type)

    this.data = clownface({ dataset: RDF.dataset(), term: RDF.namedNode('http://example.com/') })

    this.validator = new Validator(shapes)

    const { result, changeListener } = this.runForm()
    this.changeListener = changeListener

    this.changeListener.onChange(() => {
      this.runForm()
    })

    const form = result
    form.$mount()
    form.$on('submit', () => {
      this.storedQuads = [...this.data.dataset]
      this.runForm({ validationReport: this.validator.validate(this.data.dataset) })
    })
    this.$refs.form.appendChild(form.$el)
  },

  methods: {
    runForm ({ validationReport } = {}) {
      return uvForm({
        shapePointer: this.shape,
        resource: this.data,
        renderer: this.renderer,
        matcher: new Matcher(),
        changeListener: this.changeListener,
        validationReport
      })
    }
  }
}
</script>
