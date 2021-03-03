<template>
  <q-page style="margin-top:10px;" class="row justify-center">
    <div style="width: 400px; max-width: 90vw; " >
      <h4>预警系统</h4>
      <q-field icon="short_text">
        <q-input v-model="wh_location" placeholder="仓库地址" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="season" placeholder="农产品种类(肉类、蔬菜类、水果类)" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="season1" placeholder="变质风险（低、中、高）" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="season2" placeholder="储存方式（冷藏、常温、高温）" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="car_location" placeholder="点击定位车辆当前位置" @click="showcar_location" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="destination" placeholder="配送目的地" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="miles1" placeholder="仓库与目的地之间的距离" @click="show_miles1" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="time1" placeholder="仓库到目的地的时间预计" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="miles2" placeholder="当前车辆位置与目的地之间的距离" @click="show_miles2" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="time2" placeholder="当前车辆位置到目的地的时间预计" />
      </q-field>
      <q-field>
        <q-btn @click="wisdom" icon="description" color="blue-7" label="> 智慧预警查看 <" />
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
      expected_cooking_time: 20,
      season: '',
      season1: '',
      season2: '',
      wh_location: '',
      car_location: '',
      destination: '',
      miles1: '',
      time1: '',
      dish_tags: [],
      miles2: '',
      time2: '',
      price: 1.0
    }
  },
  mixins: [base],
  methods: {
    wisdom () {
      alert('可正常运输')
    },
    showcar_location () {
      this.car_location = '=>定位中...'
      setTimeout(() => {
        this.car_location = '仲恺农业工程学院'
      }, 1600)
    },
    show_miles1 () {
      this.miles1 = '=>计算中...'
      this.$axios.get('/api/v1/map', {
        params: {
          start: this.wh_location,
          end: this.destination
        }
      }).then(res => {
        console.log(res)
        if (res.status !== 200) {
          this.notifyWarn('请求错误')
          return
        }
        this.miles1 = res.data.data.distance
        this.time1 = res.data.data.time
      })
    },
    show_miles2 () {
      this.miles2 = '=>计算中...'
      this.$axios.get('/api/v1/map', {
        params: {
          start: this.car_location,
          end: this.destination
        }
      }).then(res => {
        console.log(res)
        if (res.status !== 200) {
          this.notifyWarn('请求错误')
          return
        }
        this.miles2 = res.data.data.distance
        this.time2 = res.data.data.time
      })
    },
    createDish () {
      if (this.checkStringNull(this.wh_location)) {
        this.notifyWarn('仓库地址不得为空')
        return
      }
      this.$axios.post('/api/v1/dish/', {
        name: this.dish_name,
        temperature: this.temperature,
        humidity: this.humidity,
        description: this.description,
        tags: this.dish_tags.join(','),
        expected_cooking_time: this.expected_cooking_time,
        price: this.price
      }).then(response => {
        if (response.data.ok === true) {
          this.$q.notify({
            color: 'green',
            textColor: 'white',
            icon: 'thumb_up',
            message: response.data.message,
            position: 'top-right',
            avatar: 'statics/huaji.png'
          })
          this.goPage('/dish-manage')
        } else {
          this.$q.notify({
            color: 'red',
            textColor: 'white',
            icon: 'thumb_up',
            message: response.data.message,
            position: 'top-right',
            avatar: 'statics/sad.png'
          })
        }
      }).catch(e => {
        this.$q.notify({
          color: 'red',
          textColor: 'white',
          icon: 'thumb_up',
          message: '未知错误',
          position: 'top-right',
          avatar: 'statics/sad.png'
        })
      })
    },
    selected (item) {
      console.log(item.label)
    },
    duplicate (label) {
      this.$q.notify(`"${label}" 重复标签`)
    },
    search (terms, done) {
      var searchParams = {}
      searchParams.search_field = terms
      this.$axios.get('/api/v1/dish/tag', {
        params: searchParams
      }).then(response => {
        let tags = response.data
        var results = tags.map(tag => {
          return {
            label: tag.name,
            icon: 'filter_vintage',
            value: tag.name
          }
        })
        // Call done and pass the result set
        done(results)
      })
    }
  }
}
</script>
