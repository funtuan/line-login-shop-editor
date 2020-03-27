<template>
  <div id="app" v-loading.fullscreen.lock="fullscreenLoading">
    <div id="nav" v-if="logged">
      <router-link to="/">店家編輯</router-link> |
      <router-link to="/tags">標籤編輯</router-link>
    </div>
    <router-view v-if="logged"/>
    <div v-if="!logged" class="not-logged">
      請點擊 <span class="LINE" @click="login()">LINE Login</span> 登入
    </div>
  </div>
</template>


<script>
import axios from 'axios';
axios.defaults.withCredentials = true;
export default {
  data: () => {
    return {
      logged: true,

      fullscreenLoading: false,
    };
  },
  created: function() {
    this.checkLogin();
  },
  methods: {
    checkLogin() {
      this.fullscreenLoading = true;
      axios.get(`${this.APIHOST}/api/checkLogin`)
        .then((response) => {
          this.logged = response.data.success;
          console.log(this.logged);
          this.fullscreenLoading = false;
        });
    },

    login() {
      window.location = `${this.APIHOST}/login`;
    },
  }
};
</script>

<style>
@import url(https://fonts.googleapis.com/earlyaccess/notosanstc.css);

#app {
  font-family: ‘Noto Sans TC’, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}

.LINE {
  background: #34b813;
  padding: 10px;
  color: #fff;
  border-radius: 8px;
  cursor: pointer;
}

.not-logged {
  margin-top: 23%;
}
</style>
