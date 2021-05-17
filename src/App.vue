<template>
  <div>
    <ABIExplorer
      :name="'CupController'"
      :address="'0x2f61D70241437Ad20cF1fbF09affF040DAdaB661'"
      :dataset="CupController"
      :network="'BSC Mainnet'"
    />
    <ABIExplorer
      :name="'CupVault'"
      :address="'0x585a5C1167b5b037fE0447d09CcC7c6B785d1d23'"
      :dataset="CupVault"
      :network="'BSC Mainnet'"
    />
    <ABIExplorer
      :name="'CupStrategyBUSDUSDTLPCake'"
      :address="'0x1aeE312C06AB5F6BBf07b646797100fD435442F7'"
      :dataset="CupStrategyBUSDUSDTLPCake"
      :network="'BSC Mainnet'"
    />
  </div>
</template>

<script>
import ABIExplorer from './components/ABIExplorer.vue';
import CupController from './assets/abi/CupController.json'
import CupVault from './assets/abi/CupVault.json'
import CupStrategyBUSDUSDTLPCake from './assets/abi/CupStrategyBUSDUSDTLPCake.json'

export default {
  name: 'App',
  data: function() {
    return {
      CupController,
      CupVault,
      CupStrategyBUSDUSDTLPCake
    }
  },
  components: {
    ABIExplorer
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
