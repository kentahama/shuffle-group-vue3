<script setup lang="ts">
import { ref } from "vue";

const members = ref("alice\nbob\ncharlie\n");
const numOfGroups = ref(2);

const res = ref<string[][]>([]);
const history: string[] | null = null;

function shuffle() {
  const mems = members.value.split("\n").filter(Boolean);
  const ng = numOfGroups.value;
  mems.sort(() => Math.random() - 0.5);
  res.value = Array.from(Array(ng).keys(), (i) =>
    mems.filter((_, j) => j % ng === i)
  );
}
</script>

<template>
  <main>
    <h3>グループ分け分けするやつ</h3>
    <textarea v-model="members"></textarea><br />
    <label>グループ数：</label>
    <input type="number" v-model="numOfGroups" />
    <button @click="shuffle()">shuffle</button>
    <div class="left fixedwidth">
      <div v-for="(r, i) in res" :key="i">
        グループ{{ (i + 10).toString(36).toUpperCase() }}:
        {{ r.map((s) => s + "さん").join("、") }}
      </div>
    </div>
  </main>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
#app textarea {
  height: 15rem;
  width: 450px;
}
.left {
  text-align: left;
}
.fixedwidth {
  width: 450px;
  margin: 0 auto;
}
</style>
