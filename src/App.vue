<template>
  <div id="app">
    <router-view/>
    <template v-if="tabbarVisible">
      <van-tabbar v-model="active">
        <van-tabbar-item v-for="item in tabbar" :key="item.icon"
         :icon="item.icon" :url="item.url">{{item.name}}</van-tabbar-item>
      </van-tabbar>
    </template>
  </div>
</template>
<script>

import { mapGetters } from 'vuex';

export default {
  data() {
    return {
      active: 0,
      userType: '',
      homeIcon: {
        normal: require('@/assets/tabbar/home.png'),
        active: require('@/assets/tabbar/homea.png'),
      },
      taskIcon: {
        normal: require('./assets/tabbar/carry.png'),
        active: require('./assets/tabbar/carrya.png'),
      },
      applyIcon: {
        normal: require('./assets/tabbar/app.png'),
        active: require('./assets/tabbar/appa.png'),
      },
      dataIcon: {
        normal: require('./assets/tabbar/dashboard.png'),
        active: require('./assets/tabbar/dashboarda.png'),
      },
      mineIcon: {
        normal: require('./assets/tabbar/user.png'),
        active: require('./assets/tabbar/usera.png'),
      },
      stuTtabbar: [{
        icon: 'home',
        url: '#/home',
        name: '首页',
      },
      {
        icon: 'completed',
        url: '#/task',
        name: '任务',
      },
      {
        icon: 'shop',
        url: '#/application',
        name: '应用',
      },
      {
        icon: 'contact',
        url: '#/me',
        name: '我的',
      }],
      teaTtabbar: [{
        icon: 'home',
        url: '#/home',
        name: '首页',
      },
      {
        icon: 'completed',
        url: '#/task',
        name: '任务',
      },
      {
        icon: 'shop',
        url: '#/application',
        name: '应用',
      },
      {
        icon: 'contact',
        url: '#/me',
        name: '我的',
      }],
      tabbar: [],
    };
  },
  computed: {
    ...mapGetters('system', ['tabbarVisible']),
  },
  // watch: {
  //   $route(route) {
  //     if (route.path !== '/') {
  //       this.active = 0;
  //     }
  //   },
  // },
  updated() {
    this.userType = localStorage.getItem('userType');
    if (this.userType === '1' || this.userType === '6') {
      this.tabbar = this.stuTtabbar;
    } else {
      this.tabbar = this.teaTtabbar;
    }
  },
};
</script>
<style lang="scss">
</style>
