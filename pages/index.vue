<template>
  <section class="section">
    <card
      title="これはなに?"
      icon="heart"
    >
      アイテムを持っているか確認できるやつです
    </card>
  </section>
</template>

<script lang="ts">
import Vue from "vue";
import Card from '~/components/Card.vue';
export default Vue.extend({
  name: 'HomePage',
  components: {
    Card
  }
});
</script>
