<template>
    <div>
        <h1>我是Header组件的h1</h1>
    </div>
</template>
<script>
export default {
    name: "Header",
    data() {
        return {};
    }
};
</script>
// 这里添加scoped属性，定义组件内部私有样式
<style scoped>
h1 {
    text-align: center;
    border: solid 2px cyan;
    margin: 0;
    padding: 2rem;
    background-color: #83e1ec;
}
</style>