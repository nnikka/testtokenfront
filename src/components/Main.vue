<template>
  <div class="hello">
    <AppLoader v-if="loading"></AppLoader>
    <h1>Test Token</h1>
    <br>
    <br>
    <div class="info-row">
      Creator addres: <strong> {{creator}} </strong>
    </div>
    <div class="info-row">
      <button @click="getDecimals()">
        Refresh
      </button>
      <span>
        Decimals: <strong>{{decimals}}</strong>
      </span>
    </div>
    <div class="info-row">
      <button @click="getName()">
        Refresh
      </button>
      <span>
        Name: <strong>{{name}}</strong>
      </span>
    </div>
    <div class="info-row">
      <button @click="getSymbol()">
        Refresh
      </button>
      <span>
        Symbol: <strong>{{symbol}}</strong>
      </span>
    </div>
    <div class="info-row">
      <button @click="getTotalSupply()">
        Refresh
      </button>
      <span>
        Total Supply: <strong>{{totalSupply}}</strong>
      </span>
    </div>
    <div class="separator"></div>
    <div class="info-row">
      <span>Enter address and get balance</span>
      <input type="text" placeholder="address" v-model="getBalanceOfAddress" />
      <button @click="balanceOf()">Get balance</button>
    </div>
    <div class="info-row">
      <span>Allowance</span>
      <input type="text" placeholder="owner address" v-model="getAllowanceOwner" />
      <input type="text" placeholder="spender address"  v-model="getAllowanceSpender" />
      <button @click="getAllowance()">Get Allowance</button>
    </div>
    <div class="info-row">
      <span>Transfers</span>
      <input type="text" placeholder="reciever address" v-model="transferAddress" />
      <input type="text" placeholder="value"  v-model="transferValue" />
      <button @click="transfer()">Transfer</button>
    </div>
    <div class="info-row">
      <span>Approve</span>
      <input type="text" placeholder="spender address" v-model="approveSpenderAddres" />
      <input type="text" placeholder="value"  v-model="approveValue" />
      <button @click="approve()">Approve</button>
    </div>
    <div class="info-row">
      <span>Increase approval</span>
      <input type="text" placeholder="spender address" v-model="increaseApprovalSpenderAddress" />
      <input type="text" placeholder="value"  v-model="increaseApprovalValue" />
      <button @click="increaseApproval()">Increase</button>
    </div>
    <div class="info-row">
      <span>Decrease approval</span>
      <input type="text" placeholder="spender address" v-model="decreaseApprovalSpenderAddress" />
      <input type="text" placeholder="value"  v-model="decreaseApprovalValue" />
      <button @click="decreaseApproval()">Decrease</button>
    </div>
    <div class="info-row">
      <span>Transfer from (allowed)</span>
      <input type="text" placeholder="address from" v-model="transferFromAddressFrom" />
      <input type="text" placeholder="address to"  v-model="transferFromAddressTo" />
      <input type="text" placeholder="value"  v-model="transferFromValue" />
      <button @click="transferFrom()">Transfer From</button>
    </div>
  </div>
</template>

<script>
import web3 from '../web3'
import contract from '../contract'
import Loader from './Loader'

export default {
  name: 'Main',
  data () {
    return {
      loading: true,
      creator: "",
      decimals: 0,
      name: "",
      symbol: "",
      totalSupply: 0,
      getBalanceOfAddress: "",
      getAllowanceOwner: "",
      getAllowanceSpender: "",
      transferAddress: "",
      transferValue: 0,
      approveSpenderAddres: "",
      approveValue: 0,
      increaseApprovalSpenderAddress: "",
      increaseApprovalValue: 0,
      decreaseApprovalSpenderAddress: "",
      decreaseApprovalValue: 0,
      transferFromAddressFrom: "",
      transferFromAddressTo: "",
      transferFromValue: 0
    }
  },
  beforeMount() {
    this.getCreator()
    this.getDecimals()
    this.getName()
    this.getSymbol()
    this.getTotalSupply()
    this.loading = false
  },
  methods: {
    async getCreator() {
      this.loading = true
      const creator = await contract.methods.creator().call()
      this.loading = false
      this.creator = creator
    },
    async getDecimals() {
      this.loading = true
      const decimals = await contract.methods.decimals().call()
      this.loading = false
      this.decimals = decimals
    },
    async getName() {
      this.loading = true
      const name = await contract.methods.name().call()
      this.loading = false
      this.name = name
    },
    async getSymbol() {
      this.loading = true
      const symbol = await contract.methods.symbol().call()
      this.loading = false
      this.symbol = symbol
    },
    async getTotalSupply() {
      this.loading = true
      const totalSupply = await contract.methods.totalSupply().call()
      this.loading = false
      this.totalSupply = totalSupply
    },
    async balanceOf() {
      this.loading = true
      const returnedBalance = await contract.methods.balanceOf(this.getBalanceOfAddress).call()
      this.loading = false
      alert(this.getBalanceOfAddress + " balance on this addres is " + returnedBalance)
      this.getBalanceOfAddress = ""
    },
    async getAllowance() {
      this.loading = true
      const returnedAllowance = await contract.methods.allowance(this.getAllowanceOwner, this.getAllowanceSpender).call()
      this.loading = false
      if (returnedAllowance) {
        alert("Value " + returnedAllowance)
      } else {
        alert("False")
      }
      this.getAllowanceOwner = ""
      this.getAllowanceSpender = ""
    },
    async transfer() {
      this.loading = true
      const accounts = await web3.eth.getAccounts()
      const transferResult = await contract.methods.transfer(this.transferAddress, this.transferValue).send({
        from: accounts[0],
        gas: '1000000'
      })
      this.loading = false
      if (transferResult) {
        alert("Transfer " + this.transferValue + " from address '" + this.creator + "' to address '" + this.transferAddress + "'")
      } else {
        alert("Something went wrong!")
      }
      this.transferAddress = ""
      this.transferValue = 0
    },
    async approve() {
      this.loading = true
      const accounts = await web3.eth.getAccounts()
      const approveResult = await contract.methods.approve(this.approveSpenderAddress, this.approveValue).send({
        from: accounts[0],
        gas: '1000000'
      })
      this.loading = false
      if (approveResult) {
        alert("Approved: address '" + this.approveSpenderAddres + "', value: " + this.approveValue)
      } else {
        alert("Something went wrong!")
      }
      this.approveSpenderAddres = ""
      this.approveValue = 0
    },
    async increaseApproval() {
      this.loading = true
      const accounts = await web3.eth.getAccounts()
      const increaseApprovalResult = await contract.methods.increaseApproval(this.increaseApprovalSpenderAddress, this.increaseApprovalValue).send({
        from: accounts[0],
        gas: '1000000'
      })
      this.loading = false
      if (increaseApprovalResult) {
        alert("Increase approval: address '" + this.increaseApprovalSpenderAddress + "', value: " + this.increaseApprovalValue)
      } else {
        alert("Something went wrong!")
      }
      this.increaseApprovalSpenderAddress = ""
      this.increaseApprovalValue = 0
    },
    async decreaseApproval() {
      this.loading = true
      const accounts = await web3.eth.getAccounts()
      const decreaseApprovalResult = await contract.methods.decreaseApproval(this.decreaseApprovalSpenderAddress, this.decreaseApprovalValue).send({
        from: accounts[0],
        gas: '1000000'
      })
      this.loading = false
      if (decreaseApprovalResult) {
        alert("Decrease approval: address '" + this.decreaseApprovalSpenderAddress + "', value: " + this.decreaseApprovalValue)
      } else {
        alert("Something went wrong!")
      }
      this.decreaseApprovalSpenderAddress = ""
      this.decreaseApprovalValue = 0
    },
    async transferFrom() {
      this.loading = true
      const accounts = await web3.eth.getAccounts()
       const transferFromResult = await contract.methods.transferFrom(this.transferFromAddressFrom, this.transferFromAddressTo, this.transferFromValue).send({
        from: accounts[0],
        gas: '10000000'
      })
      if (transferFromResult) {
        alert("Transfer from address '" + this.transferFromAddressFrom + "', to address '" + this.transferFromAddressTo + "', Value: " + this.transferFromValue)
      } else {
        alert("Something went wrong!")
      }
      this.loading = false
      this.transferFromAddressFrom = ""
      this.transferFromAddressTo = ""
      this.transferFromValue = 0
    }
  },
  components: {
    AppLoader: Loader,
  }
}
</script>

<style>
  .info-row {
    margin-top: 20px;
  }
  .separator {
    width: 100%;
    height: 2px;
    background: black;
    margin: 20px;
  }
</style>
