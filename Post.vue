<template>
  <div>
    <div v-if="showWarning">
      <md-card class="my-card" md-with-hover>
        <md-ripple>
          <md-card-header>
            <md-card-header-text>
              <div class="md-title">Warning</div>
              <div class="md-subhead">Please go back to Home Page</div>
            </md-card-header-text>
            <md-card-media>
              <md-icon class="md-size-3x">
                warning
              </md-icon>
            </md-card-media>
          </md-card-header>
          <md-card-content>
            You need to select a party to begain
          </md-card-content>

        </md-ripple>
      </md-card>
    </div>

    <div v-if="!showWarning">
      <!--md-dialog class="wallet" :md-click-outside-to-close=false :md-active.sync="isSigning">
        <md-toolbar class="md-accent md-dense">
          <h3 class="md-title">Signing this Transaction</h3>
        </md-toolbar>

        <div class="wallet-content">
          <div class="md-caption">Please make sure you have enough balance,</div>
          <div class="md-caption">otherwise the transaction will be failed!!!</div>
          <br>
          <div class="md-layout md-alignment-center-left">
            <div class="md-layout-item md-size-60">
              <md-field>
                <label>Balance in Rinkeby</label>
                <md-input v-model="accountBalance" readonly></md-input>
              </md-field>
            </div>

            <md-button class="md-icon-button" @click="getCurrentWallet">
              <md-icon>
                refresh
                <md-tooltip md-direction="top">Refresh</md-tooltip>
              </md-icon>
            </md-button>

            <md-button class="md-icon-button" href="https://faucet.rinkeby.io/" target="_blank">
              <md-icon>
                input
                <md-tooltip md-direction="top">Deposit</md-tooltip>
              </md-icon>
            </md-button>
          </div>
          <br>
          <br>
          <br>
        </div>
        <md-dialog-actions class="md-layout">
            <md-button class="md-accent md-raised" @click="unlockWallet">Sign</md-button>
        </md-dialog-actions>
      </md-dialog-->

      <div class="post-form">
        <md-card class="md-layout-item md-size-50 md-large-size-50 md-medium-size-70 md-small-size-90 md-xsmall-size-100">
          <md-card-header>
            <div v-if="showSC" class="title">Service Center</div>
            <div v-if="showSU" class="title">CAT Supplier (ITAMCO)</div>
            <div v-if="showMF" class="title">CAT</div>
          </md-card-header>

          <md-card-content class="post-card">
            <div v-if="showSC" class="md-layout-item md-size-100">
              <md-radio v-model="scAction" value="ship">
                <strong style="color:#64dd17;">Ship</strong>
              </md-radio>
              <md-radio v-model="scAction" value="recycle">
                <strong style="color:#64dd17;">Receipt</strong>
              </md-radio>
            </div>
			
             <div v-if="showSU" class="md-layout-item md-size-100">
              <md-radio v-model="scAction" value="ship">
                <strong style="color:#64dd17;">Ship</strong>
              </md-radio>
              <md-radio v-model="scAction" value="recycle">
                <strong style="color:#64dd17;">Receipt</strong>
              </md-radio>
			   <!--<md-radio v-model="scAction" value="order">
                <strong style="color:#64dd17;">Order</strong>
              </md-radio> -->
            </div>
			
			 <div v-if="showMF" class="md-layout-item md-size-100">
              <md-radio v-model="scAction" value="ship">
                <strong style="color:#64dd17;">Ship</strong>
              </md-radio>
              <md-radio v-model="scAction" value="recycle">
                <strong style="color:#64dd17;">Receipt</strong>
              </md-radio>
            </div>

            <div v-if="scAction == 'ship'  " >
              <div class="md-layout md-gutter">
                <div class="md-layout-item md-size-33">
                  <md-field v-b-tooltip.hover title="Physical Description of returnable Container">
                    <label for="inventoryType">Inventory Type</label>
                    <md-input name="inventoryType " id="inventoryType" v-model="form1.inventoryType" :disabled="sending" />
                  </md-field>
                </div>
				 <div class="md-layout-item md-size-33">
				<md-field v-b-tooltip.hover title="unique key in system for Inventory Type" >
                    <label>Number</label>
                    <md-input name="number" id="number" v-model="form1.number" :disabled="sending" />
                  </md-field>
                </div>
				 <div class="md-layout-item md-size-33">
                   <md-field v-b-tooltip.hover title="The qty being shipped or received">
                    <label>Quantity</label>
                    <md-input name="quantity" id="quantity" v-model="form1.quantity" :disabled="sending" />
                  </md-field>
                </div>
               <!-- <div class="md-layout-item md-size-50">
                  <md-field>
                    <label for="inventoryQty">Inventory Quantity</label>
                    <md-input name="inventoryQty" id="inventoryQty" v-model="form1.inventoryQty" :disabled="sending" />
                  </md-field>
                </div> -->
              </div>
              <div class="md-layout md-gutter">
			  <div  v-if="showSC" class="md-layout-item md-size-33">
                  <md-field v-b-tooltip.hover title="A unique number generated by ST for each transaction">
                    <label>Smart Track Txn No.</label>
                    <md-input name="smartTrackTxnNo" id="smartTrackTxnNo" v-model="form1.smartTrackTxnNo" :disabled="sending" />
                  </md-field>
                </div>
				 <div v-if="showSC" class="md-layout-item md-size-33">
                   <md-field v-b-tooltip.hover title="Supplier">
                    <label>Supplier</label>
                    <md-input name="supplier" id="supplier" v-model="form1.supplier" :disabled="sending" />
                  </md-field>
                </div>
                <div class="md-layout-item md-size-33">
                   <md-field v-b-tooltip.hover title="A detailed list of a shipment of goods in the form of a receipt given by the carrier to the person consigning the goods">
                    <label>BOL</label>
                    <md-input name="bol" id="bol" v-model="form1.bol" :disabled="sending" />
                  </md-field>
                </div>
							   <div v-if="showSU" class="md-layout-item md-size-33">
                   <md-field v-b-tooltip.hover title="Receiving Facility">
                    <label>Destination Facility (Optional) </label>
                    <md-input name="supplier" id="supplier" v-model="form1.supplier" :disabled="sending" />
                  </md-field>
                </div>  
               
              </div>

              <div class="md-layout md-gutter">
			   <div class="md-layout-item md-size-33">
                  <md-field v-b-tooltip.hover title="what the container will be/was used for - from the associated PO">
                    <label>Part No. (optional)</label>
                    <md-input name="partNo" id="partNo" v-model="form1.partNo" :disabled="sending" />
                  </md-field>
                </div>
                <div class="md-layout-item md-size-33">
                   <md-field v-b-tooltip.hover title="corresponding PO for the part(s) the returnable container will be used for">
                    <label>PO No. (optional)</label>
                    <md-input name="poNo" id="poNo" v-model="form1.poNo" :disabled="sending" />
                  </md-field>
                </div>
                <div class="md-layout-item md-size-33">
                   <md-field v-b-tooltip.hover title="unique # in Caterpillar Transportation Management system">
                    <label>Transportation Load No. (optional)</label>
                    <md-input name="tmLoadNo" id="tmLoadNo" v-model="form1.tmLoadNo" :disabled="sending" />
                  </md-field>
                </div>
              </div>

              <div class="md-layout md-gutter">
                
              </div>

            </div>
			
            <div v-if="scAction == 'recycle'">
              <div class="md-layout md-gutter">
                <div class="md-layout-item md-size-33">
                   <md-field v-b-tooltip.hover title="Physical Description of returnable Container">
                    <label for="inventoryType">Inventory Type</label>
                    <md-input name="inventoryType" id="inventoryType" v-model="form2.inventoryType" :disabled="sending" />
                  </md-field>
                </div>
				<div class="md-layout-item md-size-33">
				<md-field v-b-tooltip.hover title="unique key in system for Inventory Type" >
                    <label>Number</label>
                    <md-input name="number" id="number" v-model="form2.number" :disabled="sending" />
                  </md-field>
                </div>
                <div class="md-layout-item md-size-33">
                    <md-field v-b-tooltip.hover title="The qty being shipped or received">
                    <label>Quantity</label>
                    <md-input name="quantity" id="quantity" v-model="form2.quantity" :disabled="sending" />
                  </md-field>
                </div>
              </div>
			  
			  <div class="md-layout md-gutter">
                <div class="md-layout-item md-size-33">
                   <md-field v-b-tooltip.hover title="A detailed list of a shipment of goods in the form of a receipt given by the carrier to the person consigning the goods">
                    <label>BOL</label>
                    <md-input name="bol" id="bol" v-model="form2.bol" :disabled="sending" />
                  </md-field>
                </div>
                <div class="md-layout-item md-size-33">
                   <md-field v-b-tooltip.hover title="corresponding PO for the part(s) the returnable container will be used for">
                    <label>PO No. (optional)</label>
                    <md-input name="poNo" id="poNo" v-model="form2.poNo" :disabled="sending" />
                  </md-field>
                </div>
                <div class="md-layout-item md-size-33">
                   <md-field v-b-tooltip.hover title="unique # in Caterpillar Transportation Management system">
                    <label>Transportation Load No. (optional)</label>
                    <md-input name="tmLoadNo" id="tmLoadNo" v-model="form2.tmLoadNo" :disabled="sending" />
                  </md-field>
                </div>
              </div>

              <div class="md-layout md-gutter">
                <div v-if="showSV" class="md-layout-item md-size-50" >
                   <md-field v-b-tooltip.hover title="Service Center">
                    <label>DestinationServiceCenter</label>
                    <md-input name="supplier" id="supplier" v-model="form2.supplier" :disabled="sending" />
                  </md-field>
                </div>
				<div v-if="showSC" class="md-layout-item md-size-50">
                   <md-field v-b-tooltip.hover title="A unique number generated by ST for each transaction">
                    <label>Smart Track Txn No.</label>	
                    <md-input name="smartTrackTxnNo" id="smartTrackTxnNo" v-model="form2.smartTrackTxnNo" :disabled="sending" />
                  </md-field>
                </div>
                <div v-if="showSU" class="md-layout-item md-size-50">
                   <md-field v-b-tooltip.hover title="A unique number generated by ST for each transaction">
                    <label>Smart Track Txn No. (optional)</label>	
                    <md-input name="smartTrackTxnNo" id="smartTrackTxnNo" v-model="form2.smartTrackTxnNo" :disabled="sending" />
                  </md-field>
                </div>
				 <div v-if="showMF" class="md-layout-item md-size-33">
                  <md-field v-b-tooltip.hover title="what the container will be/was used for - from the associated PO">
                    <label>Part No. (optional)</label>
                    <md-input name="partNo" id="partNo" v-model="form2.partNo" :disabled="sending" />
                  </md-field>
                </div>
              </div>
			  </div>
			  
			  
			   <div v-if="scAction == 'order'">
              <div class="md-layout md-gutter">
                <div class="md-layout-item md-size-50">
                  <md-field>
                    <label for="inventoryType">Inventory Type</label>
                    <md-input name="inventoryType" id="inventoryType" v-model="form2.inventoryType" :disabled="sending" />
                  </md-field>
                </div>
                <div class="md-layout-item md-size-33">
                  <md-field>
                    <label>Quantity</label>
                    <md-input name="quantity" id="quantity" v-model="form2.quantity" :disabled="sending" />
                  </md-field>
                </div>
              </div>			  
			  
          	 </div>
			


          </md-card-content>

          <md-progress-bar class="md-accent" md-mode="indeterminate" v-if="sending"/>

          <md-card-actions>
            <md-button v-if="scAction == 'ship'" type="submit" class="md-accent md-raised" :disabled="!(form1.inventoryType && form1.number && form1.quantity   && form1.bol)"  v-on:click="saveRecord()">Submit</md-button>
            <md-button v-if="scAction == 'recycle'" type="submit" class="md-accent md-raised" :disabled="!(form2.inventoryType && form2.quantity && form2.number   && form2.bol )"  v-on:click="saveRecord()">Submit</md-button>
			<md-button v-if="scAction == 'order'" type="submit" class="md-accent md-raised" :disabled="!(form2.inventoryType && form2.quantity)"  v-on:click="saveRecord()">Submit</md-button>
          </md-card-actions>
        </md-card>

          <md-snackbar :md-active.sync="recordSaved">The transaction was Posted, sign with your wallet now!!!</md-snackbar>
          <md-snackbar :md-active.sync="recordSigned">The transaction was Signed, Congratulations!!!</md-snackbar>
          <md-snackbar :md-active.sync="noWalletLogged">Please click the wallet button on the top right corner to login!!!</md-snackbar>

      </div>
    </div>

  </div>
</template>

<script>
import { localstorage } from './mixins/localstorage'
import ethers from 'ethers'
import simbaApi from './gateways/simba-api'

export default {
  name: 'post',
  mounted () {
    if (!localStorage.getItem('role')) {
      this.showWarning = true
      return
    }
    this.changePost(localStorage.getItem('role'))
  },
  mixins: [localstorage],

  data: () => ({
    scAction: null,
    sending: false,
    recordSaved: false,
    recordSigned: false,
    noWalletLogged: false,
    accountBalance: null,
    txnId: null,
    unsignedTxn: null,
    //// isSigning: false,
  showSC: false,
    showSU: false,
    showMF: false,
    showWarning: true,

    form1: {
      inventoryType: null,
      inventoryQty: null,
      number: null,
      quantity: null,
      supplier: null,
      smartTrackTxnNo: null,
      tmLoadNo: null,
      bol: null,
      poNo: null,
      partNo: null
    },

    form2: {
      inventoryType: null,
      inventoryQty: null,
      number: null,
      quantity: null,
      supplier: null,
      smartTrackTxnNo: null,
      tmLoadNo: null,
      bol: null,
      poNo: null,
      partNo: null
    },
	
	form3: {
      inventoryType: null,
      quantity: null
    },

    attachment: null
  }),
  methods: {
    getCurrentWallet () {
      this.unlockWallet()
      //// let address = this.getAddress()
    //// this.getBalance(address)
  },
    //// getBalance (address) {
  //// let self = this
  //// let providers = ethers.providers
  //// let provider = providers.getDefaultProvider('rinkeby')
  //// provider.getBalance(address).then(function (balance) {
  //// let etherString = ethers.utils.formatEther(balance)
  //// self.accountBalance = etherString + ' ETH'
  //// self.isSigning = true
  //// })
  //// },
  unlockWallet () {
      try {
        let mnemonic = this.getWallet()
        this.sendTxn(mnemonic)
      } catch (e) {
        console.log(e)
      }
    },
    sendTxn (mnemonic) {
      let wallet = ethers.Wallet.fromMnemonic(mnemonic)
      let signedTxn = wallet.sign(this.unsignedTxn)
      let self = this
      let txnId = this.txnId
      let payload = {
        'payload': signedTxn
      }
      try {
        simbaApi.signTxn('transaction/' + txnId + '/', payload).then(function () {
          self.recordSigned = true
          self.isSigning = false
        })
      } catch (e) {
        console.log(e)
      }
    },
    changePost (post) {
      switch (post) {
        case 'sc':
          this.showSC = true
          this.showSU = false
          this.showMF = false
          this.showWarning = false
          break
        case 'su':
          this.showSC = false
          this.showSU = true
          this.showMF = false
          this.showWarning = false
          break
        case 'mf':
          this.showSC = false
          this.showSU = false
          this.showMF = true
          this.showWarning = false
          break
      }
    },
    clearForm () {
      this.form1.inventoryType = null
      this.form1.inventoryQty = null
      this.form1.number = null
      this.form1.quantity = null
      this.form1.supplier = null
      this.form1.smartTrackTxnNo = null
      this.form1.tmLoadNo = null
      this.form1.bol = null
      this.form1.poNo = null
      this.form1.partNo = null

      this.form2.inventoryType = null
      this.form2.quantity = null
      this.form2.supplier = null
      this.form2.smartTrackTxnNo = null
      this.form2.tmLoadNo = null
      this.form2.bol = null
      this.form2.poNo = null

	  
	  this.form3.inventoryType = null
      this.form3.quantity = null

      this.attachment = null
    },
    saveRecord (e) {
      if (!this.getWallet()) {
        this.noWalletLogged = true
        return
      }
      this.sending = true

      let bodyFormData = new FormData()
      bodyFormData.append('from', this.getAddress())
	  
	  let bodyFormData1 = new FormData()
	  bodyFormData1.append('from', this.getAddress())
	  
	  let bodyFormData2 = new FormData()
	  bodyFormData2.append('from', this.getAddress())

      if (this.attachment) {
        bodyFormData.append('file[0]', document.getElementById('attachment').files[0])
      }

      let method = null
	  let masterCd= null
	  let method1= null

      if (this.showSC) {
        if (this.scAction === 'recycle') {
          method = 'shipDirtyContainer'
		  masterCd='ServiceCenter'
		  method1='ADD'
        } else if (this.scAction === 'ship') {
          method = 'shipCleanContainer'
		   masterCd='ServiceCenter'
		  method1='SUB'
        } else {
          return
        }
      } else if (this.showSU) {
        if (this.scAction === 'recycle') {
          method = 'shipDirtyContainer'
		   masterCd='ITAMCO'
		  method1='ADD'
        } else if (this.scAction === 'ship') {
          method = 'shipCleanContainer'
		  masterCd='ITAMCO'
		  method1='SUB'
        } else if (this.scAction === 'order'){
          method = 'shipContainerWithParts'
		  masterCd='ITAMCO'
		  method1=''
        } else {
          return
        }
		
      } else if (this.showMF) {
       if (this.scAction === 'recycle') {
          method = 'shipDirtyContainer'
		  masterCd='CAT'
		  method1='ADD'
        } else if (this.scAction === 'ship') {
          method = 'shipCleanContainer'
		  masterCd='CAT'
		  method1='SUB'
        } else {
          return
        }
      } else {
        return
      }

      if (method === 'shipDirtyContainer') {
        for (let k in this.form2) {
          if (this.form2.hasOwnProperty(k)) {
            bodyFormData.append(k, this.form2[k])
          }
        }
		bodyFormData.append('inventoryQty', bodyFormData.get('quantity'))
      } else if (method === 'shipCleanContainer'){
        for (let k in this.form1) {
          if (this.form1.hasOwnProperty(k)) {
            bodyFormData.append(k, this.form1[k])
          }
        }
		bodyFormData.append('inventoryQty', bodyFormData.get('quantity'))
      }
	  else {
        for (let k in this.form2) {
          if (this.form2.hasOwnProperty(k)) {
            bodyFormData.append(k, this.form2[k])
          }
        }
      }

      let self = this
      try {
		simbaApi.getData('containerMaster/?masterCode=CONT-WACO&inventoryType=PALLET&no_page=1/').then(function (response) {
          for (let i = 0; i < response.data.results.length; i++) {
			     if(response.data.results[i].payload.inputs.masterCode==masterCd && response.data.results[i].payload.inputs.inventoryType==bodyFormData.get('inventoryType') ) {
				 bodyFormData1.append('masterCode', masterCd)
				 bodyFormData1.append('inventoryType', bodyFormData.get('inventoryType'))
				 if(method1=='SUB')
				   bodyFormData1.append('inventoryQty', response.data.results[i].payload.inputs.inventoryQty-parseInt(bodyFormData.get('quantity')))
			     else
				   bodyFormData1.append('inventoryQty', response.data.results[i].payload.inputs.inventoryQty+parseInt(bodyFormData.get('quantity')))
				  simbaApi.postData('containerMaster' + '/', bodyFormData1).then(function (res) {
				  self.txnId = res.data.id
				  self.unsignedTxn = res.data.payload.raw
				  self.getCurrentWallet()
				  self.recordSaved = true
				  self.sending = false
				  self.clearForm()
        })
		break
		}

		         
		}})
		if (method!='shipContainerWithParts') {
        simbaApi.postData(method + '/', bodyFormData).then(function (res) {
          self.txnId = res.data.id
          self.unsignedTxn = res.data.payload.raw
          self.getCurrentWallet()
          self.recordSaved = true
          self.sending = false
          self.clearForm()
        })}
      } catch (e) {
        console.log(e)
      }
    }
  }
}
</script>

<style scoped>

  .my-card {
    margin-top: 40px;
    width: 300px;
    display: inline-block;
    vertical-align: top;
    margin-bottom: 20px;
  }
  .md-progress-bar {
    position: absolute;
    top: 0;
    right: 0;
    left: 0;
  }
  .post-form {
    margin-top: 40px;
    height: 550px;
  }
  .title {
    font-size: 20px;
    margin-left: 10px;
    margin-top: 7px;
  }
  .post-card {
    margin: 10px;
  }
  .options {
    margin-top: 40px;
  }
  .wallet {
    min-width: 320px;
  }
  .wallet-content {
    margin: 20px;
  }
</style>
