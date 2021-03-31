<template>
  <q-page style="margin-top:10px;" class="row justify-center">
    <div style="width: 400px; max-width: 90vw; " >
      <h4>温湿度监测系统</h4>
      <q-field icon="short_text">
        <q-input v-model="order_number" placeholder="请输入订单号" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="current_location" placeholder="点击查询该订单运送车所在位置" @click="showall"/>
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="list" placeholder="查询该订单物品清单" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="temperature" placeholder="当前温度" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="humidity" placeholder="当前湿度" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="temhumai" placeholder="点击查询湿度智能判断" @click="showhumidity"/>
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="temperatureai" placeholder="点击查询温度智能判断" @click="showtemperature"/>
      </q-field>
    </div>
  </q-page>
</template>

<style>
</style>

<script>
import base from '../mixins/base'
export default {
  name: 'Thmanage',
  data () {
    return {
      order_number: '',
      current_location: '',
      list: '',
      temperature: '',
      humidity: '',
      temhumai: '',
      temperatureai: ''
    }
  },
  mixins: [base],
  methods: {
    showall () {
      this.$axios.get('/api/vi/humiwarn', {
        params: {
          order_no: this.order_number
        }
      }).then(res => {
        console.log(res)
        if (res.status !== 200) {
          this.notifyWarn('请求错误')
          return
        }
        this.current_location = res.data.culocation
        this.list = res.data.data
        this.temperature = res.data.temperature
        this.humidity = res.data.humidity
      })
    },
    showhumidity () {
      this.$axios.get('/api/vi/humiwarn', {
        params: {
          order_no: this.order_number
        }
      }).then(res => {
        console.log(res)
        if (res.status !== 200) {
          this.notifyWarn('请求错误')
          return
        }
        this.temhumai = res.data.message
      })
    },
    showtemperature () {
      this.$axios.get('/api/vi/thwarn', {
        params: {
          order_no: this.order_number
        }
      }).then(res => {
        console.log(res)
        if (res.status !== 200) {
          this.notifyWarn('请求错误')
          return
        }
        this.temperatureai = res.data.message
      })
    }
  }
}
</script>
