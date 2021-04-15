<template>
  <q-page style="margin-top:10px;" class="row justify-center">
    <div style="width: 400px; max-width: 90vw; " >
      <h4>配送时间预警</h4>
      <q-field icon="short_text">
        <q-input v-model="tm_location" placeholder="点击获取仓库地址" @click="showtm_location"/>
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="tm_list" placeholder="请输入订单号" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="tm_finallocation" placeholder="点击获取配送目的地" @click="showtm_finallocation"/>
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="tm_currentlocation" placeholder="点击获取当前订单配送所在位置" @click="showtm_currentlocation"/>
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="tm_miles3" placeholder="点击获取当前配送所在位置与目的地之间的距离" @click="showmessage2"/>
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="tm_miles4" placeholder="当前配送所在位置到目的地的时间预计" />
      </q-field>
    </div>
  </q-page>
</template>

<style>
</style>

<script>
import base from '../mixins/base'
export default {
  name: 'Timemanage',
  data () {
    return {
      tm_location: '',
      tm_list: '',
      tm_finallocation: '',
      tm_currentlocation: '',
      tm_miles3: '',
      tm_miles4: ''
    }
  },
  mixins: [base],
  methods: {
    showtm_location () {
      this.tm_location = '广州市钱大妈总部'
    },
    showtm_finallocation () {
      this.$axios.get('api/vi/getdestination', {
        params: {
          order_no: this.tm_list
        }
      }).then(res => {
        console.log(res)
        if (res.status !== 200) {
          this.notifyWarn('请求错误')
          return
        }
        this.tm_finallocation = res.data.data
      })
    },
    showtm_currentlocation () {
      this.$axios.get('/api/vi/humiwarn', {
        params: {
          order_no: this.tm_list
        }
      }).then(res => {
        console.log(res)
        if (res.status !== 200) {
          this.notifyWarn('请求错误')
          return
        }
        this.tm_currentlocation = res.data.culocation
      })
    },
    showmessage2 () {
      this.$axios.get('/api/v1/time', {
        params: {
          order_no: this.tm_list
        }
      }).then(res => {
        console.log(res)
        if (res.status !== 200) {
          this.notifyWarn('请求错误')
          return
        }
        this.tm_miles3 = res.data.data.distance
        this.tm_miles4 = res.data.data.time
      })
    }
  }
}
</script>
