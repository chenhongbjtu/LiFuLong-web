/* eslint-disable */
<template>
  <q-layout view="lHh Lpr lFf">
    <q-layout-header>
      <q-toolbar
        color="primary"
        :glossy="$q.theme === 'mat'"
        :inverted="$q.theme === 'ios'"
      >
        <q-btn v-if="loggedIn"
          flat
          dense
          round
          @click="leftDrawerOpen = !leftDrawerOpen"
          aria-label="Menu"
        >
          <q-icon name="menu" />
        </q-btn>

        <q-toolbar-title>
          <a @click="goPage('/')" style="cursor:pointer;">生鲜农产品自营配送监测预警系统</a>
          <div slot="subtitle">你好，李富龙！</div>
        </q-toolbar-title>
        <q-btn-dropdown flat :label="user">
        <q-list link>
          <q-item v-if="loggedIn" @click.native="goPage('/change-pass')">
            <!-- <q-item-side icon="assignment" inverted color="primary" /> -->
            <q-item-main>
              <q-item-tile  label>修改密码</q-item-tile>
              <!-- <q-item-tile sublabel>February 22, 2016</q-item-tile> -->
            </q-item-main>
            <q-item-side right icon="info" color="amber" />
          </q-item>

          <q-item v-if="loggedIn" @click.native="logout">
            <!-- <q-item-side icon="assignment" inverted color="primary" /> -->
            <q-item-main>
              <q-item-tile  label>登出</q-item-tile>
              <!-- <q-item-tile sublabel>February 22, 2016</q-item-tile> -->
            </q-item-main>
            <q-item-side right icon="info" color="amber" />
          </q-item>
          <q-item v-if="!loggedIn" @click.native="goPage('/login')">
            <!-- <q-item-side icon="assignment" inverted color="primary" /> -->
            <q-item-main>
              <q-item-tile label>登录</q-item-tile>
              <!-- <q-item-tile sublabel>February 22, 2016</q-item-tile> -->
            </q-item-main>
            <q-item-side right icon="info" color="amber" />
          </q-item>

        </q-list>
      </q-btn-dropdown>
      </q-toolbar>
    </q-layout-header>

    <q-layout-drawer
      v-model="leftDrawerOpen"
      :content-class="$q.theme === 'mat' ? 'bg-grey-2' : null"
    >
      <q-list no-border link inset-delimiter>
        <q-list-header>功能菜单</q-list-header>
        <q-item v-if="my_role==='超级管理员'" @click.native="goPage('/dish-manage')">
          <q-item-side icon="fastfood" />
          <q-item-main
            label="生鲜农产品管理"
          />
        </q-item>
        <q-item v-if="my_role==='超级管理员'" @click.native="goPage('/table-manage')">
          <q-item-side icon="table_chart" />
          <q-item-main
            label="配送地址管理"
          />
        </q-item>
        <q-item v-if="my_role==='超级管理员'" @click.native="goPage('/kitchen-manage')">
          <q-item-side icon="smoking_rooms" />
          <q-item-main label="运输车管理" />
        </q-item>

        <q-item v-if="my_role==='超级管理员'" @click.native="goPage('/time-manage')">
          <q-item-side icon="smoking_rooms" />
          <q-item-main label="配送时间预警" />
        </q-item>

        <q-item v-if="my_role==='超级管理员'" @click.native="goPage('/th-manage')">
          <q-item-side icon="smoking_rooms" />
          <q-item-main label="温湿度监测" />
        </q-item>

        <q-item v-if="my_role==='超级管理员'" @click.native="goPage('/delivery-man')">
          <q-item-side icon="smoking_rooms" />
          <q-item-main label="配送员信息" />
        </q-item>

        <q-collapsible v-if="my_role==='订单管理员' || my_role==='超级管理员'" indent icon="recent_actors" label="配送管理" opened>
          <q-item class="main-item" @click.native="goPage('/order-manage')">
            <q-item-side icon="menu" />
            <q-item-main label="配送中订单" />
          </q-item>
          <q-item class="main-item" @click.native="goPage('/order-by-table')">
            <q-item-side icon="crop_din" />
            <q-item-main label="地址视图" />
          </q-item>
        </q-collapsible>
        <q-collapsible v-if="my_role==='仓库管理员' || my_role==='超级管理员'" indent icon="assignment_ind" label="运输车视图" opened>
          <q-item class="main-item" @click.native="goPage('/chef-assignment-board')">
            <q-item-side icon="assignment" />
            <q-item-main label="订单分配视图" />
          </q-item>
        </q-collapsible>
        <q-collapsible v-if="my_role==='超级管理员'" indent icon="recent_actors" label="报表管理" opened>
          <q-item class="main-item" @click.native="goPage('/report-manage')">
            <q-item-side icon="now wallpaper" />
            <q-item-main label="全部历史订单" />
          </q-item>
          <q-item class="main-item" @click.native="goPage('/report-by-table')">
            <q-item-side icon="tablet" />
            <q-item-main label="历史订单(地址)" />
          </q-item>
        </q-collapsible>

        <q-item v-if="my_role==='超级管理员'" class="main-item" @click.native="goPage('/user-manage')">
          <q-item-side icon="people" />
          <q-item-main label="用户管理" />
        </q-item>
      </q-list>
    </q-layout-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { openURL } from 'quasar'

export default {
  name: 'MyLayout',
  data () {
    return {
      user: '用户',
      my_role: '',
      loggedIn: false,
      leftDrawerOpen: false
    }
  },
  methods: {
    openURL,
    goPage (val) {
      this.$router.push(val)
    },
    logout () {
      this.$axios.post('/logout', {}, { headers: { 'Content-Type': 'application/x-www-form-urlencoded' } })
        .then(response => {
          location.reload()
        })
        .catch(e => {
          location.reload()
        })
    },
    whoami () {
      var storeThis = this
      this.$axios.get('/api/v1/user/info').then(function (response) {
        storeThis.user = response.data.username
        storeThis.loggedIn = true
        storeThis.my_role = response.data.role_name
      }).catch(function (error) {
        console.log(error)
        storeThis.leftDrawerOpen = false
        storeThis.loggedIn = false
        storeThis.$router.push('/login')
      })
    },
    regularCheckLogin () {
      var storeThis = this
      var count = 0
      setInterval(function () {
        count = count + 1
        storeThis.whoami()
      }, 15000)
    }
  },
  mounted () {
    this.leftDrawerOpen = (this.loggedIn && this.$q.platform.is.desktop)
    this.whoami()
    this.regularCheckLogin()
  }
}
</script>

<style>
.main-item{
  cursor:pointer;
}
</style>
