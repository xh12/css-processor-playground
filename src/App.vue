<script setup>
import Code from './components/code.vue'
import { ref } from 'vue'
import snippet from './snippet.mjs'

let type = 'variable'
const language = ref('scss')
const from = ref(snippet[type][language.value])
const to = ref('');


renderToCss(from.value)

function ontypeselect(e) {
  type = e.target.value
  from.value = snippet[type][language.value]
}

function onlangselect(e) {
  language.value = e.target.value
  from.value = snippet[type][language.value]
}

function onchange(value) {
  renderToCss(value)
}
function renderToCss(value) {
  const lang = language.value;
  if (lang === 'scss') {
    // console.log(sass);
    // const res = sass.compileString(value)
    // console.log(res);
    fetch('/api/compile', {
      method: 'POST',
      mode: 'cors',
      headers: {
        'Content-Type': 'application/json'
        // 'Content-Type': 'application/x-www-form-urlencoded',
      },
      body: JSON.stringify({
        compiler: "dart-sass/1.32.12",
        input: value,
        outputStyle: 'expanded',
        syntax: 'SCSS'
      })
    }).then(async (v) => {
      const res = await v.json()
      to.value = res.css
      // emit('change', res.css)
    })
  } else {
    console.log('less change')
    window.less.render(value)
      .then(v => {
        to.value = v.css
        // emit('change', v.css)
      })
  }
}
</script>

<template>
  <div class="container">
    <header>
      <select value="variable" @change="ontypeselect">
        <option v-for="key in Object.keys(snippet)" :value="key">{{key}}</option>
        <!-- <option value="variable">variable</option>
        <option value="hahaha">haahah</option> -->
      </select>
      <select value="scss" @change="onlangselect">
        <option value="scss">scss</option>
        <option value="less">less</option>
      </select>
    </header>
    <div class="code-container"><Code :value="from" :lang="language" @change="onchange" /></div>
    <div class="code-container"><Code :value="to" /></div>
  </div>
</template>

<style scoped>
.container:after {
  content: "";
  display: block;
  clear: both;
}

.code-container {
  width: 50%;
  float: left;
}

header {
  height: 5vh;
}
</style>
