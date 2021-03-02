<template>
  <q-page style="margin-top:10px;" class="row justify-center">
    <div style="width: 400px; max-width: 90vw; " >
      <h4>新建生鲜农产品</h4>
      <q-field icon="short_text">
        <q-input v-model="dish_name" placeholder="产品名" @blur="nameBlur" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="temperature" placeholder="系统推荐温度阈值" />
      </q-field>
      <q-field icon="short_text">
        <q-input v-model="humidity" placeholder="系统推荐湿度阈值" />
      </q-field>
      <q-field icon="description">
        <q-input v-model="description" placeholder="运送方式(常温运送 or 冷藏运送)" />
      </q-field>
      <q-field icon="tag_faces">
         <q-chips-input  v-model="dish_tags" placeholder="添加产品标签"  @duplicate="duplicate">
           <q-autocomplete :min-characters="1" @search="search" @selected="selected" />
         </q-chips-input>
      </q-field>
      <q-field icon="timer" label="送达时间设置(小时)" :label-width="5">
         <q-input type="number"  max-length="16" v-model="expected_cooking_time" />
      </q-field>
      <q-field icon="attach money" label="价格(元)" :label-width="5">
         <q-input type="number"  max-length="16" v-model="price" />
      </q-field>
      <q-field>
        <q-btn  @click="goPage('/dish-manage')" icon="keyboard return" color="purple-7" label="返回生鲜农产品列表" />
        <q-btn  @click="createDish" icon="add" color="blue" style="margin-left: 10px;" label="创建生鲜农产品" />
      </q-field>
    </div>
  </q-page>
</template>

<style>
</style>

<script>
import base from '../mixins/base'
export default {
  name: 'Newdish',
  data () {
    return {
      expected_cooking_time: 20,
      dish_name: '',
      temperature: '',
      humidity: '',
      dish_tags: [],
      description: '',
      price: 1.0
    }
  },
  mixins: [base],
  methods: {
    // 产品名移除
    nameBlur () {
      console.log(111)
      this.$axios.get('/api/v1/th', {
        params: {
          productName: this.dish_name
        }
      }).then(res => {
        console.log(res)
        if (res.status !== 200) {
          this.notifyWarn('请求错误')
          return
        }
        console.log('res.data.temperature=', res.data.temperature)
        this.temperature = res.data.data.temperature
        this.humidity = res.data.data.humidity
      })
    },
    createDish () {
      if (this.checkStringNull(this.dish_name)) {
        this.notifyWarn('产品名不得为空')
        return
      }
      this.$axios.post('/api/v1/dish/', {
        name: this.dish_name,
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
