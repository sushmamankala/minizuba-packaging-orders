<template>
  <div class="main-body">
    <h1>Minizuba Packaging Orders</h1>
    <label>Filter by Quantity: </label>
    <InputNumber class="quantity-field" v-model="quantityFilter" placeholder="Enter quantity" :min="1" :max="maxPackageTypeId"></InputNumber>
    <Button @click="init()" type="primary" :disabled="this.dataMap.size > 0" :title="this.dataMap.size > 0 ? 'Loading data' : ''">Fetch Orderlines</Button>

    <Table ref="iTable" :columns="columns" :data="tableData" :key="tableRefresh" :height="500" :loading="loadingTable" style="margin-top: 10px;"></Table>
    <Page :total="orderlines.length" :current="currentPage" :page-size="100" @on-change="loadMoreRecords" />
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      loadingTable: false,
      dataMap: new Map(),
      currentPage: 1,
      tableRefresh: 0,
      maxPackageTypeId: 14,
      quantityFilter: null,
      columns: [
        {
          title: 'OrderLineID',
          key: 'OrderLineID'
        },
        {
          title: 'OrderID',
          key: 'OrderID'
        },
        {
          title: 'StockItemID',
          key: 'StockItemID'
        },
        {
          minWidth: 250,
          title: 'Description',
          key: 'Description'
        },
        {
          title: 'PackageTypeID',
          key: 'PackageTypeID'
        },
        {
          title: 'Quantity',
          key: 'Quantity'
        },
        {
          title: 'UnitPrice',
          key: 'UnitPrice'
        }
      ],
      tableData: [],
      orderlines: [],
      batchSize: 50, // Number of records to load at a time
    };
  },
  created () {
    this.init()
  },
  methods: {
    init() {
      this.loadingTable = true
      for (let i = 1; i <= this.maxPackageTypeId; i++) {
        this.dataMap.set(i, null)
      }
      this.getOrderLines();
    },
    loadMoreRecords(pageNumber) {
      this.currentPage = pageNumber ? pageNumber : this.currentPage
      this.tableData = this.orderlines.slice((this.currentPage * 100)-100, (this.currentPage * 100))
      this.tableRefresh++
    },
    getOrderLines () {
      this.orderlines = []
      this.tableData = []
      this.currentPage = 1
      for(let i = 1; i <= this.maxPackageTypeId; i++) {
        this.getOrderLine(i, this.quantityFilter)
      }
    },
    async getOrderLine (id, qty) {
      try {
        let response = await axios.get(`https://minizuba-fn.azurewebsites.net/api/orderlines?type_id=${id}${qty != null ? '&quantity='+qty: ''}`);
        this.dataMap.set(id, response.data)
      } catch (e) {
        this.dataMap.delete(id)
      }
      this.updateData()
    },
    updateData () {
      for (let [item, value] of this.dataMap) {
        if (value == null) return
        this.orderlines.push(...value)
        this.dataMap.delete(item)
        if (!this.tableData.length) {
          this.loadMoreRecords(item)
          this.loadingTable = false
        }
      }
    }
  },
};
</script>

<style scoped>
  .main-body {
    padding: 4px 8px 0 8px;
  }
  .quantity-field {
    width: 15%;
    margin-right: 8px;
  }
</style>
