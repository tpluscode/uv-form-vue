<template>
  <form class="modal-card">
    <header class="modal-card-head">
      <h2 class="modal-card-title">Search</h2>
    </header>
    <section class="modal-card-body">
      <b-field label="Terms">
        <b-input required ref="terms" />
      </b-field>
      <b-field>
        <b-button type="is-primary" icon-left="search">Search</b-button>
      </b-field>
    </section>
  </form>
</template>

<script>
export default {
  mounted () {
    this.$refs.terms.focus()
  }
}
</script>
