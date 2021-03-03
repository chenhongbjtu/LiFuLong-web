<template>
  <q-page style="margin-top:10px;" class="row justify-center">
    <div style="width: 400px; max-width: 90vw; " >
      <h4>预警系统</h4>
      <q-field icon="short_text">
        <q-input v-model="wh_location" placeholder="仓库地址" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="car_location" placeholder="点击定位车辆当前位置" @click="showcar_location" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="destination" placeholder="配送目的地" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="miles" placeholder="仓库与目的地之间的距离" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="time1" placeholder="仓库到目的地的时间预计" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="time2" placeholder="当前车辆位置到目的地的时间预计" />
      </q-field>
      <q-field>
        <q-btn @click="goPage('/dish-manage')" icon="description" color="blue-7" label="> 智慧预警查看 <" />
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
      options: [
        'Google', 'Facebook', 'Twitter', 'Apple', 'Oracle'
      ],
      wh_location: '',
      car_location: '',
      destination: '',
      miles: '',
      time1: '',
      dish_tags: [],
      time2: '',
      price: 1.0
    }
  },
  mixins: [base],
  methods: {
    showcar_location () {
      this.car_location = '=>定位中...'
      setTimeout(() => {
        this.car_location = '仲恺农业工程学院'
      }, 1600)
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
