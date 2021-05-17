<template>
  <div>
    <button @click=""></button>
  </div>
</template>

<script>
export default {

  data: function() {
    return {
      provider: null
    }
  },

  beforeMount() {
    this.provider = window.ethereum
  }
}
</script>

<style>

</style>