<template>
  <div >
    <NavBar />
    <nuxt />
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import NavBar from '~/components/NavBar.vue';
export default Vue.extend({
  components: {
    NavBar
  }
});
</script>
