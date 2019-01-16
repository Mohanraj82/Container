<template>
  <div>
  <table>
  <tr>
  <td>
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
    <div v-if="showTracking">
      <md-table  v-model="searched" md-sort="name" md-sort-order="asc" md-card md-fixed-header >
        <md-table-toolbar>
          <div class="md-toolbar-section-start filter-btn">
            <md-menu md-align-trigger md-size="big">
              <md-button md-menu-trigger><md-icon>filter_list</md-icon>Inventory Details: <span style="color:#64dd17;">{{containerFilter}}</span></md-button>
              <md-menu-content>
			  <md-menu-item  @click="changefilter('container')">
                  <md-icon>present_to_all</md-icon>
                  <span>Container</span>
              </md-menu-item>
			  <md-menu-item  @click="changefilter('containerhistory')">
                  <md-icon>present_to_all</md-icon>
                  <span>Container History</span>
              </md-menu-item>
              </md-menu-content>
            </md-menu>
          </div>
         
        </md-table-toolbar>

        <div v-if="!searched.length">
          <h2 class="loading" v-if="!theBar">We cannot find any records : (</h2>
        </div>
		
		<md-table-row v-if="containerFilter == 'container'"  slot="md-table-row" v-for="(se, i) in searched" :key="i" @click="queryRecord1(se)">
          <md-table-cell md-label="No." md-numeric>{{ i + 1 }}</md-table-cell>
          <md-table-cell md-label="Container Type" md-sort-by="payload.inputs.inventoryType">{{ se.payload.inputs.inventoryType }}</md-table-cell>
          <md-table-cell md-label="Container Qty" md-sort-by="payload.inputs.inventoryQty">{{ se.payload.inputs.inventoryQty }}</md-table-cell>
		  <md-table-cell md-label="Entities" md-sort-by="payload.inputs.masterCode">{{ se.payload.inputs.masterCode}}</md-table-cell>
        </md-table-row>
		
		<md-table-row v-if="containerFilter == 'containerhistory'" slot="md-table-row" v-for="(se, i) in searched" :key="i" @click="queryRecord1(se)">
          <md-table-cell md-label="No." md-numeric>{{ i + 1 }}</md-table-cell>
          <md-table-cell md-label="Container Type" md-sort-by="payload.inputs.inventoryType">{{ se.payload.inputs.inventoryType }}</md-table-cell>
          <md-table-cell md-label="Container Qty" md-sort-by="payload.inputs.inventoryQty">{{ se.payload.inputs.inventoryQty }}</md-table-cell>
		  <md-table-cell md-label="Entities" md-sort-by="payload.inputs.masterCode">{{ se.payload.inputs.masterCode}}</md-table-cell>
          <md-table-cell md-label="Timestamp" md-sort-by="timestamp">{{ (new Date(se.timestamp)).toLocaleString() }}</md-table-cell>
        </md-table-row>

      </md-table>
    </div>
	</td>
	<td>
	<div v-if="showTracking">
      <md-table  v-model="searched" md-sort="name" md-sort-order="asc" md-card md-fixed-header >
        <md-table-toolbar>
          <div class="md-toolbar-section-start filter-btn">
            <md-menu md-align-trigger md-size="big">
              <md-button md-menu-trigger><span style="color:#64dd17;">Total Inventory</span></md-button>
              <md-menu-content>
			  <md-menu-item  @click="changefilter('container')">
                  <md-icon>present_to_all</md-icon>
                  <span>Container</span>
              </md-menu-item>
			  <md-menu-item  @click="changefilter('containerhistory')">
                  <md-icon>present_to_all</md-icon>
                  <span>Container History</span>
              </md-menu-item>
              </md-menu-content>
            </md-menu>
          </div>
         
        </md-table-toolbar>

        <div v-if="!searched1.length">
          <h2 class="loading" v-if="!theBar">We cannot find any records : (</h2>
        </div>
		
		<md-table-row  slot="md-table-row" v-for="(se, i) in searched1" :key="i" @click="queryRecord1(se)">
          <md-table-cell md-label="No." md-numeric>{{ i + 1 }}</md-table-cell>
          <md-table-cell md-label="Container Type" md-sort-by="inventoryType">{{ se.inventoryType }}</md-table-cell>
          <md-table-cell md-label="Total Container Qty" md-sort-by="inventoryQty">{{ se.inventoryQty }}</md-table-cell>
        </md-table-row>


      </md-table>
    </div>
	</td>
	</tr>
	</table>
  </div>
</template>

<script>
import simbaApi from './gateways/simba-api'
import saveAs from 'file-saver'
import b64toBlob from 'b64-to-blob'

export default {
  name: 'Get',
  mounted () {
    if (!localStorage.getItem('role')) {
      this.showWarning = true
      this.showTracking = false
      return
    }
    this.showWarning = false
    this.showTracking = true
    this.getRecords()
  },
  data: () => ({
    searchBar: false,
    searchCategory: null,
    searchBy: null,
    showTracking: false,
    showWarning: false,
    showTxn: false,
    showBundle: false,
    search: null,
    methodName: null,
    queryMethodName: null,
    containerFilter: 'container',

    searched: [],
	searched1: [],
    showDetailsDialog: false,
    theBar: true,

    inventoryType: null,
    inventoryQty: null,
    number: null,
    quantity: null,
    supplier: null,
    smartTrackTxnNo: null,
    tmLoadNo: null,
    bol: null,
    poNo: null,
    partNo: null,
    destinationServiceCenter: null,

    txnFrom: null,
    txnTo: null,
    txnHash: null,
    txnStatus: null,
    gasUsed: null,

    bundleHash: null,
    fileSize: null,
    fileName: null,
    imgData: null,
    fileData: null,
    mimetype: null,

    isImg: null,
    isFileUnavailable: null
  }),
  methods: {
    triggerSearch (category) {
      this.searchBar = true
      this.searchBy = 'Search by ' + category
      this.searchCategory = category
      this.search = ''
    },
    changefilter (filter) {
      this.search = null
      this.containerFilter = filter
      this.theBar = true
      this.searchCategory = null
      this.searchBy = null
      this.searchBar = false

      switch (filter) {
        case 'container':
          this.getByCondition('containerMaster')
          this.methodName = 'containerMaster'
          break
		case 'containerhistory':
          this.getByCondition('containerMasterHistory')
          this.methodName = 'containerMaster'
          break
        case 'ship':
          this.getByCondition('shipCleanContainer')
          this.methodName = 'shipCleanContainer'
          break
        case 'receipt':
          this.getByCondition('shipDirtyContainer')
          this.methodName = 'shipDirtyContainer'
          break
        case 'recyeled':
          this.getByCondition('recycleContainer')
          this.methodName = 'recycleContainer'
          break
        case 'all':
          this.getRecords()
          break
      }
    },
    getByCondition (condition) {
      this.searched = null
	  this.searched1 = null
	  var methName=null
	  if(condition=="containerMasterHistory") {
		methName=condition
		condition='containerMaster'
	  }
      let self = this
      try {
        simbaApi.getData(condition + '/?no_page=1/')
          .then(function (response) {
			var containerMasters=response.data.results
		  var Remove_duplicate_Value = [];
		  var finalcontainerMasters=[];
          for(var i =0; i < containerMasters.length; i++) {
			  if(Remove_duplicate_Value.indexOf(containerMasters[i].payload.inputs.masterCode+containerMasters[i].payload.inputs.inventoryType) === -1) {
				  if (containerMasters[i].payload.inputs.inventoryType=='Steel Tub' || containerMasters[i].payload.inputs.inventoryType=='HalfTub') {
				     finalcontainerMasters.push(containerMasters[i])
				     Remove_duplicate_Value.push(containerMasters[i].payload.inputs.masterCode+containerMasters[i].payload.inputs.inventoryType)
				  }
			  }
          }
            var steelSumQty=0
		  var halfSumQty=0
		  var sumcontainerMasters=[]
		   sumcontainerMasters= [
                    {
                        inventoryType: 'Steel Tub',
                        inventoryQty: 0
                    },
                    {
                        inventoryType: 'HalfTub',
                        inventoryQty: 0
                    }
                ]

		  for(var i =0; i < finalcontainerMasters.length; i++) {
			 if (finalcontainerMasters[i].payload.inputs.inventoryType=='Steel Tub' ) {
					steelSumQty=steelSumQty+finalcontainerMasters[i].payload.inputs.inventoryQty
				  }
			 else if (finalcontainerMasters[i].payload.inputs.inventoryType=='HalfTub') {
				 halfSumQty=halfSumQty+finalcontainerMasters[i].payload.inputs.inventoryQty
			 }
		  }

			  sumcontainerMasters[0].inventoryQty=steelSumQty
			  sumcontainerMasters[1].inventoryQty=halfSumQty			  
            self.theBar = false
			self.searched1 = sumcontainerMasters
			if(methName=="containerMasterHistory")
                self.searched = containerMasters
			else
				self.searched = finalcontainerMasters
				
          })
      } catch (e) {
        this.errors.push(e)
      }
    },
    queryRecord (res) {
      this.showDetailsDialog = true
      this.cleanAll()
      this.findTxn(res.id)
    },
    cleanAll () {
      this.inventoryType = null
      this.inventoryQty = null
      this.number = null
      this.quantity = null
      this.supplier = null
      this.smartTrackTxnNo = null
      this.tmLoadNo = null
      this.bol = null
      this.poNo = null
      this.partNo = null
      this.destinationServiceCenter = null
      this.queryMethodName = null

      this.txnFrom = null
      this.txnTo = null
      this.txnHash = null
      this.txnStatus = null
      this.gasUsed = null

      this.bundleHash = null
      this.fileSize = null
      this.fileName = null
      this.imgData = null
      this.fileData = null
      this.mimetype = null

      this.isImg = null
      this.showBundle = false
      this.isFileUnavailable = null
    },
    findTxn (id) {
      let self = this
      try {
        simbaApi.getData('transaction/' + id + '/')
          .then(function (res) {
            self.queryMethodName = res.data.payload.method
            self.setBasic(res.data.payload.inputs)
            self.setFileBundle(id)
            if (res.data.receipt == null) {
              self.showTxn = false
            } else {
              self.setTxn(res)
            }
          })
      } catch (e) {
        this.errors.push(e)
      }
    },
    setBasic (obj) {
      for (let k in obj) {
        if (obj.hasOwnProperty(k)) {
          this[k] = obj[k]
        }
      }
    },
    setTxn (res) {
      this.showTxn = true
      this.txnFrom = res.data.receipt.from
      this.txnTo = res.data.receipt.to
      this.txnHash = res.data.receipt.transactionHash
      this.txnStatus = res.data.receipt.status
      this.gasUsed = res.data.receipt.gasUsed
    },
    setFileBundle (id) {
      if (id == null) {
        return
      }
      let self = this
      try {
        simbaApi.getData('transaction/' + id + '/bundle/')
          .then(function (res) {
            if (res.data.bundle_hash) {
              console.log(res)
              self.showBundle = true
              self.bundleHash = res.data.bundle_hash
              self.fileSize = res.data.manifest[0].size
              self.fileName = res.data.manifest[0].name
              self.mimetype = res.data.manifest[0].mimetype

              let isImg = (/\.(gif|jpg|jpeg|tiff|png)$/i).test(res.data.manifest[0].name)
              if (isImg) {
                self.isImg = true
                self.imgData = 'data:image/png;base64, ' + res.data.manifest[0].data
              }
              self.fileData = res.data.manifest[0].data
            } else {
              self.showBundle = false
            }
          })
      } catch (e) {
        this.errors.push(e)
        this.isFileUnavailable = true
      }
    },
    downloadToLocal () {
      let content = this.fileData
      let filename = this.fileName
      let mimeString = this.mimetype

      let decoded = b64toBlob(content, mimeString)

      let blob = new Blob([decoded])

      saveAs(blob, filename)
    },
    searchWithApi () {
      let self = this
      let url = null
      if (this.containerFilter === 'all') {
        url = 'transaction/?'
      } else {
        url = this.methodName + '/?'
      }
      try {
        simbaApi.getData(url + this.searchCategory + '_contains=' + this.search)
          .then(function (response) {
            self.searched = response.data.results
          })
      } catch (e) {
        this.errors.push(e)
      }
    },
    getRecords (e) {
      this.searched = null
	  this.searched1 = null
      let self = this
      try {
        simbaApi.getData('containerMaster/?no_page=1/')
          .then(function (response) {
			  var containerMasters=response.data.results
		  var Remove_duplicate_Value = [];
		  var finalcontainerMasters=[];
          for(var i =0; i < containerMasters.length; i++) {
			  if(Remove_duplicate_Value.indexOf(containerMasters[i].payload.inputs.masterCode+containerMasters[i].payload.inputs.inventoryType) === -1) {
				  if (containerMasters[i].payload.inputs.inventoryType=='Steel Tub' || containerMasters[i].payload.inputs.inventoryType=='HalfTub') {
				     finalcontainerMasters.push(containerMasters[i])
				     Remove_duplicate_Value.push(containerMasters[i].payload.inputs.masterCode+containerMasters[i].payload.inputs.inventoryType)
				  }
			  }
          }
		  var steelSumQty=0
		  var halfSumQty=0
		  var sumcontainerMasters=[]
		   sumcontainerMasters= [
                    {
                        inventoryType: 'Steel Tub',
                        inventoryQty: 0
                    },
                    {
                        inventoryType: 'HalfTub',
                        inventoryQty: 0
                    }
                ]

		  for(var i =0; i < finalcontainerMasters.length; i++) {
			 if (finalcontainerMasters[i].payload.inputs.inventoryType=='Steel Tub' ) {
					steelSumQty=steelSumQty+finalcontainerMasters[i].payload.inputs.inventoryQty
				  }
			 else if (finalcontainerMasters[i].payload.inputs.inventoryType=='HalfTub') {
				 halfSumQty=halfSumQty+finalcontainerMasters[i].payload.inputs.inventoryQty
			 }
		  }

			  sumcontainerMasters[0].inventoryQty=steelSumQty
			  sumcontainerMasters[1].inventoryQty=halfSumQty			  
            self.theBar = false
			self.searched1 = sumcontainerMasters
            self.searched = finalcontainerMasters
          })
      } catch (e) {
        this.errors.push(e)
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
  .search-btn {
    margin-bottom: -8px;
    margin-left: 10px;
  }
  .detailsDialog {
    max-width: 600px;
  }
  .txn-details {
    margin: 15px;
  }
  .get-title {
    margin: 20px;
  }
  .options {
    margin-top: 40px;
  }
  .audit-table  {
    margin-top: 40px;
    height: 550px;
  }
  .md-field {
    max-width: 300px;
    height: auto;
  }
  .md-progress-bar {
    margin: 24px;
  }
  .loading {
    margin-left: 10px;
    text-align: center;
  }
  .dialog {
    min-height: 400px;
  }
  .break {
    word-break: break-all;
  }
  .api-bar {
    margin-top: -18px;
  }
  .search-bar {
    margin-left: 15px;
    margin-top: -10px;
    margin-right: 20px;
    margin-bottom: 10px;
  }
  .bundle-img {
    display: block;
    max-height:300px;
    width: auto;
    height: auto;
  }
</style>
