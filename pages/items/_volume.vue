<template>
  <section class="main-content section">
    <div class="container">
      <nav class="breadcrumb" aria-label="breadcrumbs">
        <ul>
          <li>
            <a href="/">Home</a>
          </li>
          <li>
            <a href="/items/">items</a>
          </li>
          <li class="is-active">
            <a :href="`/items/${$route.params.volume}/`">
              {{
              $route.params.volume
              }}
            </a>
          </li>
        </ul>
      </nav>
      <h1 class="title is-1">{{ $route.params.class }}</h1>
      <div>
        <div v-for="item in items" :key="item.id" class="card">
          <div class="card-content">
            <b-switch :value="itemHaveInfo[item.id]" @input="val => onInput(item.id, val)" >
              {{ `${item.id} : ${item.label}` }}
            </b-switch>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script lang="ts">
import Vue from "vue";
import axios from "axios";
import { firestore, auth, isAuth } from "~/plugins/firebase"
export default Vue.extend({
  data() {
    return {
      items: null,
      itemHaveInfo: {}
    };
  },
  async mounted() {
    const isAuthRet = await isAuth();
    if(!isAuthRet){
      this.$router.push('/')
    }
    const querySnapshot =  await firestore.collection("users").doc(auth.currentUser.uid).get()
    if(!querySnapshot.exists){
      await firestore.collection("users").doc(auth.currentUser.uid).set({})
    }
    firestore.collection("users").doc(auth.currentUser.uid).onSnapshot(this.onSnapshot)
    const volume = this.$route.params.volume;
    const typePredUri = `http://www.w3.org/1999/02/22-rdf-syntax-ns#type`;
    const query = `
    PREFIX prism: <https://prismdb.takanakahiko.me/prism-schema.ttl#>
    SELECT ?URI ?LABEL
    WHERE {
      ?URI prism:volume	"${volume}";
           rdfs:label ?LABEL
    }`;
    try {
      console.log(volume);
      const response = await axios.get(
        "https://prismdb.takanakahiko.me/sparql",
        {
          params: { query },
          headers: { "Content-Type": "application/sparql-query+json" }
        }
      );
      this.itemHaveInfo = querySnapshot.data()
      const items = response.data.results.bindings.map(b => {
        const id = b["URI"].value.replace("https://prismdb.takanakahiko.me/rdfs/item/", "")
        return {
          id: id,
          label: b["LABEL"].value
        }
      });
      this.items = items;
    } catch (e) {
      console.log(e);
    }
  },
  methods: {
    onSnapshot(doc) {
      this.itemHaveInfo = doc.data()
    },
    onInput(id:string, val:boolean) {
      const value = {}
      value[id] = val
      firestore.collection("users").doc(auth.currentUser.uid).update(value)
    }
  }
});
</script>