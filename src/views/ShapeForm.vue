<template>
  <div class="columns">
    <div class="column">
      <h2 class="title is-size-4">Form</h2>
      <UVForm v-if="shape" :shape="shape" :data="data" @submit="onSubmit" />
      <p v-else>Not loaded</p>
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
            <tr v-for="({subject, predicate, object}, index) in data.dataset.quads" :key="index">
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
import RDF from '@rdfjs/dataset'
import UVForm from '@/components/UVForm.vue'

export default {
  name: 'ShapeForm',
  components: { UVForm },

  data () {
    return {
      shape: null,
      data: null
    }
  },

  async mounted () {
    const response = await fetch('object-shape.ttl')
    const shapes = await response.dataset()
    this.shape = clownface({ dataset: shapes })
      .node(sh.NodeShape)
      .in(rdf.type)

    this.data = clownface({ dataset: RDF.dataset() })

    // const resource = RDF.blankNode()
    // const data = clownface({ dataset: RDF.dataset(), term: resource })
    // this.data = data.node(resource)
    //   .addOut(rdf.type, this.shape.out(sh.targetClass).term)
    //   .addOut(RDF.namedNode('http://purl.org/dc/elements/1.1/identifier'), RDF.literal('Hoy'))
  },

  computed: {
    dataDisplay () {
      if (!this.data) return 'No data'

      return [...this.data.dataset.quads]
        .map(({ subject, predicate, object }) => `${subject.value} ${predicate.value} ${object.value}`)
    }
  },

  methods: {
    onSubmit (newData) {
      this.data = newData
    }
  }
}
</script>
