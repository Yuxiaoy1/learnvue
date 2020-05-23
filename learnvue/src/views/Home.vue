<template>
    <div class="home">
        <Header></Header>
        <h1>我是父组件的h1</h1>
    </div>
</template>

<script>
// @ is an alias to /src
import Header from "@/components/03/Header.vue";
export default {
    name: "Home",
    components: {
        Header
    }
};
</script>
<style scoped>
.home /deep/ h1 {
    color: green;
}
</style>
