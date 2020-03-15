<template>
  <b-navbar class="header has-shadow is-primary">
    <template slot="brand">
      <b-navbar-item tag="router-link" :to="{ path: '/' }">
        <img
          src="~assets/buefy.png"
          alt="Lightweight UI components for Vue.js based on Bulma"
        />
      </b-navbar-item>
    </template>
    <template slot="start">
      <b-navbar-item href="/">Home</b-navbar-item>
    </template>

    <template slot="end">
      <b-navbar-item v-if="!this.isLogin" tag="div">
        <div class="buttons">
          <a class="button is-light" @click="login" >Log in</a>
        </div>
      </b-navbar-item>
      <b-navbar-item v-else tag="div">
        <div class="buttons">
          <a class="button is-light" @click="logout" >Log Out</a>
        </div>
      </b-navbar-item>
    </template>
  </b-navbar>
</template>

<script lang="ts">
import Vue from "vue";
import firebase from "firebase"
import { auth } from "~/plugins/firebase";

export default Vue.extend({
  data() {
    return {
      isLogin: false
    }
  },
  async mounted() {
    await auth.onAuthStateChanged((user) => this.isLogin = user ? true : false)
  },
  methods: {
    async login() {
      await auth.signOut()
      const provider = new firebase.auth.GoogleAuthProvider()
      auth.signInWithRedirect(provider)
        .then(user => this.$router.push('/'))
        .catch(e => alert(e.message))
    },
    async logout() {
      await auth.signOut()
      this.$router.push('/')
    }
  }
});
</script>
