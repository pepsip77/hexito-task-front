<template>
  <input v-model.number="amount" placeholder="Amount">
  <span class="error">{{ errors.amount || '' }}</span>
  <select v-model="currencyFrom">
    <option :value="null" disabled>Select currency</option>
    <option value="EUR">EUR</option>
    <option value="USD">USD</option>
    <option value="PLN">PLN</option>
  </select>
  <span class="error">{{ errors.currencyFrom || '' }}</span>
  <input v-model="currencyTo" placeholder="Crypto symbol">
  <span class="error">{{ errors.currencyTo || '' }}</span>
  <button @click="convert()">Convert</button>
  <span class="error">{{ serverError || '' }}</span>
  <span>{{ result || '' }}</span>
</template>

<script>
  import axios from "axios";
  import cfg from "../config/app.js";

  export default {
    name: "CryptoConverter",
    data() {
      return {
        amount: 0,
        currencyFrom: null,
        currencyTo: '',
        errors: [],
        serverError: '',
        result: '',
      }
    },
    methods: {
      convert() {
        this.errors = [];
        this.serverError = '';
        this.result = '';
        const requestParams = {
          amount: this.amount,
          currencyFrom: this.currencyFrom,
          currencyTo: this.currencyTo
        };

        axios.get(cfg.converterUrl, {
          params: requestParams
        })
        .then(response => {

          this.result = response.data.result
        })
        .catch(err => {
          if (err.response.data.errors) {
            let errors = err.response.data.errors;

            Object.keys(errors).forEach((key, index) => errors[key] = errors[key][0]);
            this.errors = errors;
          } else {
            this.serverError = err.response.data.message || '';
          }
        })
      }
    }
  }
</script>

<style scoped>
  input, select, button {
    display: block;
    margin: 0.75rem 0;
    background-color: rgba(24,24,24,0.85);
    color: rgba(151,171,177,0.85);
    border-color: #282828;
    border-radius: 5%;
    width: 200px;
  }

  .error {
    color: #fd1d1d;
  }
</style>
