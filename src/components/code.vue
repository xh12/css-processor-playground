<script setup>
import * as monaco from 'monaco-editor';
import editorWorker from 'monaco-editor/esm/vs/editor/editor.worker?worker'
import jsonWorker from 'monaco-editor/esm/vs/language/json/json.worker?worker'
import cssWorker from 'monaco-editor/esm/vs/language/css/css.worker?worker'
import htmlWorker from 'monaco-editor/esm/vs/language/html/html.worker?worker'
import tsWorker from 'monaco-editor/esm/vs/language/typescript/ts.worker?worker'
import { getCurrentInstance, onMounted, watch, watchEffect } from 'vue'
self.MonacoEnvironment = {
    getWorker(_, label) {
        if (label === 'json') {
            return new jsonWorker()
        }
        if (label === 'css' || label === 'scss' || label === 'less') {
            return new cssWorker()
        }
        if (label === 'html' || label === 'handlebars' || label === 'razor') {
            return new htmlWorker()
        }
        if (label === 'typescript' || label === 'javascript') {
            return new tsWorker()
        }
        return new editorWorker()
    }
}
const props = defineProps({
    value: String,
    lang: String,
})
const emit = defineEmits(['change']);


onMounted(() => {
    const instance = getCurrentInstance()
    const { root } = instance.refs
    const editor = monaco.editor.create(root, {
        value: props.value,
        language: props.lang || 'css',
        fontSize: 15
    })
    watch(() => props.value || '', (value) => {
    console.log(value, '');
        editor.setValue(value)
    })
    watch(() => props.lang || 'css', (value) => {
        const model = monaco.editor.createModel(props.value, value)
        editor.setModel(model)
    })
    editor.onDidChangeModelContent(() => {
        // renderToCss(editor.getValue())
        emit('change', editor.getValue())
    })
})

</script>
<style scoped>
.code-root {
    height: 95vh;
}
</style>
<template>
    <div class="code-root" ref="root"></div>
</template>