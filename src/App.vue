<template>
  <div id="app">
    <h1></h1>
    <h2>Cold Siganature</h2>
    <table>
      <tbody>
        <tr>
          <td>Chain Id:</td>
          <td><input v-model='chainId' size='80'/></td>
        </tr>
        <tr>
          <td>Private Key:</td>
          <td><input v-model='privateKey' size='80' @keyup='updatePublicKey'/>
          <br/>
          {{publicKey}}
          </td>
        </tr>
        <tr>
          <td>Transaction Buffer(Hex):</td>
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
import {ChainStore, FetchChain, PrivateKey, TransactionHelper, Aes, TransactionBuilder, ops} from "bitsharesjs";
import {ChainConfig} from "bitsharesjs-ws";
import {extend} from 'lodash'
import * as jsonFormat from 'json-format'
import * as ByteBuffer from 'bytebuffer'
console.log(ChainConfig)
ChainConfig.address_prefix = 'BTS'
export default {
  name: 'app',
  data: function () {
    return {
      privateKey:'',
      publicKey:'',
      trx:'' ,
      signed:'',
      formatted:'',
      chainId:'4018d7844c78f6a6c41c6a552b898022310fc5dec06da467ee7905a8dad512c8'
    }
  },
  methods: {
    updatePublicKey: function() {
      var prvKey = PrivateKey.fromWif(this.privateKey)
      this.publicKey = prvKey.toPublicKey().toString('BTS')
    },
    signIt: function() {
      try {
        // var trx1 = JSON.parse(this.trx)
        var tx = new TransactionBuilder()
        // extend(tx, trx1)
        var tx = extend(tx, ops.transaction.fromHex(this.trx))
        tx.tr_buffer = new Buffer(this.trx, 'hex')
        console.log(tx)
        var prvKey = PrivateKey.fromWif(this.privateKey)
        console.log(prvKey)
        tx.add_signer(prvKey)
        tx.sign(this.chainId)
        this.signed = JSON.stringify(tx.toObject())
      }catch(e) {
        console.error(e)
      }
      // alert(`sign ${this.privateKey}, ${this.trx}`)
    },
    formatJson:function (txt) {
      var obj = ops.transaction.toObject(ops.transaction.fromHex(this.trx))
      console.log(jsonFormat(obj))
      return jsonFormat(obj)
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
