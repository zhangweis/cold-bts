<template>
  <div id="app">
    <h1></h1>
    <h2>Cold Siganature</h2>
    <table>
      <tbody>
        <tr>
          <td>Private Key:</td>
          <td><input v-model='privateKey' size='80'/></td>
        </tr>
        <tr>
          <td>Transaction JSON:</td>
          <td>
            <textarea rows="10" cols='80' v-model='trx' @keyup='formatted = formatJson(trx)'>
            </textarea>
          </td>
        </tr>
        <tr>
          <td>Formatted Transaction:</td>
          <td>
            <pre>{{formatted}}</pre>
          </td>
        </tr>
        <tr>
          <td>Signed Trx:</td>
          <td><textarea rows="20" cols='80' v-model='signed' readonly='true'/></td>
        </tr>
        <tr>
          <td colspan="2" style='text-align: center'>
            <button @click='signIt'>Sign</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import {ChainStore, FetchChain, PrivateKey, TransactionHelper, Aes, TransactionBuilder} from "bitsharesjs";
import {extend} from 'lodash'
import * as jsonFormat from 'json-format'
import * as ByteBuffer from 'bytebuffer'
export default {
  name: 'app',
  data () {
    return {
      privateKey:'',
      trx:``      ,
      signed:'',
      formatted:''
    }
  },
  methods: {
    signIt() {
      try {
        var trx1 = JSON.parse(this.trx)
        var tx = new TransactionBuilder()
        extend(tx, trx1)
        tx.tr_buffer = new Buffer(tx.tr_buffer, 'hex')
        console.log(tx)
        var prvKey = PrivateKey.fromWif(this.privateKey)
        console.log(prvKey)
        tx.add_signer(prvKey)
        tx.sign(trx1.chain_id)
        this.signed = JSON.stringify(tx.toObject())
      }catch(e) {
        console.error(e)
      }
      // alert(`sign ${this.privateKey}, ${this.trx}`)
    },
    formatJson(txt) {
      console.log(jsonFormat(JSON.parse(txt)))
      return jsonFormat(JSON.parse(txt))
    }
  }
}
</script>

<style>
textarea, input, #app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  font-size: 16px;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
td {
  text-align: left
}
</style>
