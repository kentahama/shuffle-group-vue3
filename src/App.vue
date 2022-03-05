<script setup lang="ts">
import { ref } from "vue";

type Groups<T> = T[][];

/**
 * Returns randomly n-grouped array.
 * @param a An array
 * @param n Number of groups
 */
function randomGroup<T>(a: T[], n: number): Groups<T> {
  const _a = [...a];
  _a.sort(() => Math.random() - 0.5);
  return Array.from(Array(n).keys(), (i) => _a.filter((_, j) => j % n === i));
}

/**
 * Non-symmetric set similarity; #A∩B/#B
 * @param a An objective array
 * @param b A base array
 */
function similarity<T>(a: T[], b: T[]): number {
  const intersection = a.filter((v) => b.includes(v));
  return intersection.length / b.length;
}

const members = ref("alice\nbob\ncharlie\n");
const numOfGroups = ref(2);
const res = ref<Groups<string>>([]);
const history: Groups<string>[] = [];

function shuffle() {
  const names = members.value.split("\n").filter(Boolean);
  const n = numOfGroups.value;
  let random = randomGroup(names, n);
  if (history.length > 0) {
    const last = history[history.length - 1];
    const threashold = 0.7;
    let retry = 100;
    while (
      random.some((a) =>
        last.some((b) => {
          similarity(a, b) > threashold;
        })
      )
    ) {
      random = randomGroup(names, n);
      retry--;
      if (retry <= 0) {
        console.warn("retry limit exceeded");
        break;
      }
    }
  }
  history.push(random);
  res.value = random;
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
