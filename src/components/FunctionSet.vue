<template>
  <div>
    <div v-if="abiSpec.type == 'function'">
      <div class="header">
        <span
          v-if="functionType == 'view' || functionType == 'pure'"
          style="color: blue"
        >{{ abiSpec.name }}</span>
        <span 
          v-else
          style="color: red"
        >{{ abiSpec.name }}</span>
      </div>

      <div
        v-for="(input,index) in abiSpec.inputs"
        :key="input"
      >
        <div class="input-header">
          <span v-if="input.name !== ''">{{ input.name }}</span>
          <span
            style="font-style:italic"
            v-else
          >in_arg</span>
          ({{ input.internalType }})
        </div>
        <div>
          <input
            type="text"
            :placeholder="input.type"
            class="input-box"
            v-model="inputParams[index]"
          >
        </div>
      </div>

      <div v-if="functionType == 'view' || functionType == 'pure'">
        <button
          @click="callContract()"
          class="call-button"
        >
          Call
        </button>
      </div>

      <div v-else>
        <button
          @click="sendContract()"
          class="call-button"
        >
          Send
        </button>
      </div>
  
      <div
        v-for="(output) in abiSpec.outputs"
        :key="output"
        class="output"
      >
        >  <span v-if="output.name !== ''">{{ output.name }}:</span> {{ output.internalType }}
      </div>

      <div v-if="formattedOutput.length > 0">
        <h4>Response</h4>
        <div
          v-for="(output, index) in formattedOutput"
          :key="output"
          class="output"
        >
          <span v-if="output.name !== ''">{{ output.name }}: </span>
          <span style="color: green">{{ formattedOutput[index].value }}</span> ({{ output.internalType }})
        </div>
      </div>
    </div>

    <div v-else>
      <div class="header">
        <span
          style="color: #ff9900"
        >constructor</span>
        <div
          v-for="input in abiSpec.inputs"
          :key="input"
        >
          <div class="input-header">
            <span v-if="input.name !== ''">{{ input.name }}</span>
            <span
              style="font-style:italic"
              v-else
            >in_arg</span>
            ({{ input.internalType }})
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ethers } from 'ethers';

export default {

  mounted: async function() {
    this.functionType = this.abiSpec.stateMutability
    
    const provider = new ethers.providers.Web3Provider(window.ethereum)
    const signer = provider.getSigner()
    this.contract = new ethers.Contract(this.address, [this.abiSpec], signer)

    const inputTypes = this.abiSpec.inputs.map(x => x.type);
    this.methodSignature = `${this.abiSpec.name}(${inputTypes.toString()})`
  },

  data: function() {
    return {
      functionType: 'pure',
      inputParams: [],
      outputParams: [],
      contract: null,
      methodSignature: ''
    }
  },

  props: {
    abiSpec: Object,
    address: String
  },


  methods: {

    async test() {
      fetch('https://mlt-api.koinage.cc/api/player/info?address=TTyDtAADgAkCnuPjDhEc1P3kFK4X4wWrSk')
        .then(response => response.json())
        .then(data => console.log(data));
    },

    async callContract() {
      this.contract[this.methodSignature](...this.inputParams)
        .then(res => {
          const array = [res]
          this.outputParams = [...this.abiSpec.outputs]
          for (let i = 0; i < this.outputParams.length; i++) {
            this.outputParams[i].value = array[i];
          }
          console.log(this.outputParams);
          // this.outputParams.push(res)
        })
        .catch(err => {
          this.outputParams = [{}]
          this.outputParams[0].value = `Code: ${err.code}, Message: ${err.message}`
        })
    },

    async sendContract() {
      this.contract[this.methodSignature](...this.inputParams)
        .then(response => {
          response.wait(1)
            .then(() => {
              this.outputParams = [...this.abiSpec.outputs]
              this.outputParams[0].value = true
            })
            .catch(err => {
              this.outputParams = [{}]
              if (err?.data) {
                this.outputParams[0].value = `Code: ${err.data.code}, Message: ${err.data.message}`
              }
                this.outputParams[0].value = `Code: ${err.code}, Message: ${err.message}`
              })
        })
        .catch(err => {
          this.outputParams = [{}]
          if (err?.data) {
            this.outputParams[0].value = `Code: ${err.data.code}, Message: ${err.data.message}`
          }
            this.outputParams[0].value = `Code: ${err.code}, Message: ${err.message}`
        })
    }
  },

  computed: {

    formattedOutput() {
      return this.outputParams.map(x => {
        x.value = x.value.toString();
        return x;
      })
    }
  }

}
</script>

<style>
  .input-box {
    width: 100%;
    border: 1px solid #909090;
    border-radius: 3px;
    height: 30px;
  }

  .call-button {
    margin-top: 10px;
    margin-bottom: 10px;
    height: 28px;
    border-radius: 3px;
    border: 1px solid;
  }

  .header {
    margin-bottom: 10px;
  }

  .input-header {
    margin: 8px;
  }

  .output {
    font-style: italic;
    font-size: small;
  }
</style>
