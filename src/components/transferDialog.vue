<template>
  <q-card flat bordered class="q-ma-md q-pa-md">
      <div>Transfer {{ tokenName }}</div>
      <div><q-input label="To account" v-model="toAccount" /></div>
      <div><q-input label="Amount" v-model="amount" /></div>
      <div class="text-center text-caption">Available: <span class="cursor-pointer text-bold" @click="amount = parseFloat(balance)">{{ balance }}</span> {{ tokenName }}</div>
      <div><q-input label="Memo" v-model="memo" /></div>
      <div class="text-center q-ma-md">
        <div v-if="log !== ''"><q-icon name="error" color="red" v-if="err" />{{ this.log }}</div>
        <q-btn dense label="Send" icon="send" color="primary" @click="transfer()" />
      </div>
  </q-card>
</template>
<script>

export default {
  name: 'transferDialog',
  data () {
    return {
      toAccount: '',
      amount: 0.000,
      memo: '',
      err: false,
      log: ''
    }
  },
  props: {
    tokenName: {
      type: String,
      required: true
    },
    network: {
      type: String,
      required: false,
      default: 'hive'
    },
    balance: {
      type: Number,
      required: true
    },
    username: {
      type: String,
      required: true
    },
    precision: {
      type: Number,
      required: false,
      default: 3
    }
  },
  methods: {
    transfer () {
      if (this.network === 'hive') {
        this.transferHiveKeychain()
      } else if (this.network === 'hiveEngine') {
        this.transferHiveEngineKeychain()
      }
    },
    transferHiveKeychain () {
      window.hive_keychain.requestTransfer(this.username, this.toAccount, parseFloat(this.amount).toFixed(this.precision), this.memo, this.tokenName, function (response, err) {
        if (response.success === true) {
          this.err = false
          this.log = response.message
        }
        if (response.success === false) {
          this.err = true
          this.log = response.message
        }
      }.bind(this))
    },
    transferHiveEngineKeychain () {
      // todo
      var json = '{ "contractName": "tokens", "contractAction": "transfer", "contractPayload": { "symbol": "' + this.tokenName + '", "to": "' + this.toAccount + '", "quantity": "' + this.amount + '", "memo": "' + this.memo + '" } }'
      window.hive_keychain.requestCustomJson(this.username, 'ssc-mainnet-hive', 'Active', json, 'Transfer ' + this.amount + ' ' + this.tokenName + ' to ' + this.toAccount, function (response) {
        if (response.success === true) {
          this.err = false
          this.log = response.message
        } else {
          this.err = true
          this.log = response.message
        }
      }.bind(this))
    }
  }
}
</script>
