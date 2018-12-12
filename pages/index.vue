<template>
  <section class="container">
    <div>
      <app-logo/>
      <h1 class="title">nuxt_firebase_sample</h1>
      <h2 class="subtitle">Nuxt.js x Firebase sample application</h2>
      <div class="links">
        <Home v-if="!isLogin"></Home>
        <Mypage v-if="isLogin" :user="userData"></Mypage>
      </div>
    </div>
  </section>
</template>

<script>
import AppLogo from "~/components/AppLogo.vue";
import Home from "~/components/Home.vue";
import Mypage from "~/components/Mypage.vue";

import firebase from "@/plugins/firebase";

export default {
  components: {
    AppLogo,
    Home,
    Mypage
  },
  asyncData(context) {
    return { name: "Hello, World！！", isLogin: false, userData: null };
  },
  fetch() {},
  mounted: function() {
    firebase.auth().onAuthStateChanged(user => {
      console.log(user);
      if (user) {
        this.isLogin = true;
        this.userData = user;
      } else {
        this.isLogin = false;
        this.userData = null;
      }
    });
  }
};
</script>

<style>
.container {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: "Quicksand", "Source Sans Pro", -apple-system, BlinkMacSystemFont,
    "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif; /* 1 */
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>

