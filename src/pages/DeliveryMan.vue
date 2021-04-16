<template>
  <q-page style="margin-top:10px;" padding class="docs-table">
    <div class="row justify-center" >
      <h4>配送员信息</h4>
    </div>
    <q-table
      :data="orders"
      :columns="columns"
      row-key="id"
      color="secondary"
      :pagination.sync="serverPagination"
      @request="request"
      :loading="loading"
    >
      <template slot="top-right" slot-scope="props">
        <q-btn v-if="props!=null" title="刷新信息" round color="blue" @click="refreshItems" icon="refresh" />
      </template>
      <q-td slot="body-cell-OrderNumber" slot-scope="props" :props="props">
        <a href="javascript:" @click.prevent="goPage('/order-detail/'+props.row.id)">{{props.value}}</a>
      </q-td>
    </q-table>
  </q-page>
</template>

<style>
</style>

<script>
  import base from '../mixins/base'
  export default {
    name: 'DeliveryMan',
    data () {
      return {
        table_page_size: 100,
        loading: false,
        orders: [],
        serverPagination: {
          page: 1,
          rowsPerPage: 100,
          rowsNumber: 10 // specifying this determines pagination is server-side
        },
        columns: [
          {
            name: 'orderNumber',
            required: true,
            label: '订单号',
            align: 'center',
            field: 'orderNumber',
            sortable: false
          },
          {
            name: 'culocation',
            required: true,
            label: '当前位置',
            align: 'center',
            field: 'culocation',
            sortable: false
          },
          {
            name: 'destination',
            required: true,
            label: '目的地',
            align: 'center',
            field: 'destination',
            sortable: false
          },
          {
            name: 'name',
            required: true,
            label: '配送员',
            align: 'center',
            field: 'name',
            sortable: false
          },
          {
            name: 'phone',
            required: true,
            label: '电话',
            align: 'center',
            field: 'phone',
            sortable: false
          },
          {
            name: 'carNo',
            required: true,
            label: '车牌号',
            align: 'center',
            field: 'carNo',
            sortable: false
          }
        ]
      }
    },
    mixins: [base],
    mounted () {
      this.loadHistoryOrders()
    },
    methods: {
      refreshItems () {
        this.loadHistoryOrders()
      },
      request (props) {
        this.serverPagination = props.pagination
        this.loadHistoryOrders()
      },
      loadHistoryOrders () {
        var params = {}
        params.page = this.serverPagination.page
        params.pageSize = this.serverPagination.rowsPerPage
        this.loading = true
        this.$axios.get('/api/v1/delivery', {
          params: params
        }).then(response => {
          this.orders = response.data.data
          this.serverPagination.page = response.data.currentPage
          this.serverPagination.rowsNumber = response.data.total
          this.serverPagination.rowsPerPage = response.data.pageSize
          this.loading = false
        }).catch(e => {

        })
      },
      newOrder () {
        this.$axios.post('/api/v1/order/new').then(response => {
          if (response.data.ok === true) {
            this.notifySuccess(response.data.message)
            this.goPage('/order-detail/' + response.data.data)
          }
        }).catch(e => {

        })
      }
    }
  }
</script>
