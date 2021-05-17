<template>
  <div>
    <h1>{{ name }}</h1>
    <h2>{{ address }} ({{ network }})</h2>
    <div
      v-for="data in sortedData"
      :key="data"
      class="divider"
    >
      <FunctionSet
        :abi-spec="data" 
        :address="address"
        :provider="provider"
      />
    </div>
  </div>
</template>

<script>
import FunctionSet from './FunctionSet.vue';

export default {

  mounted: async function() {
    await window.ethereum.request({ method: 'eth_requestAccounts' })
  },

  data: function() {
    return {};
  },

  computed: {

    sortedData() {
      let copyArr = [...this.dataset]
      return copyArr.sort(x => x.name)
    }
  },

  props: {
    address: String,
    name: String,
    dataset: Object,
    network: String
  },

  components: {
    FunctionSet
  }
}
</script>

<style>

  .divider {
    text-align: left;
    border: 1px solid #616060;
    border-radius: 15px;
    margin: 10px;
    padding: 16px;
  }

  h2 {
    color: gray;
  }
</style>