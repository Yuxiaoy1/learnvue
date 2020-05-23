<template>
    <pre>
        <!-- class中指定代码语言 -->
        <code class="language-javascript">
            const vm = new Vue({
                el: '#app',
                data: {},
                methods: {}
            })
        </code>
    </pre>
</template>

<script>
import Prism from "prismjs";
export default {
    name: "CodeHighLight",
    mounted() {
        // Prismjs内置的代码高亮方法
        Prism.highlightAll();
    }
};
</script>

<style>
</style>