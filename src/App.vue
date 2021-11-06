<template>
    <div class="page">
        <MainPage></MainPage>
    </div>
</template>

<script>
import MainPage from './pages/MainPage.vue';

export default {
    name: 'App',
    components: {
        MainPage
    }
};
</script>

<style lang="scss">
body {
    font-family: 'Open Sans', sans-serif;
    font-size: 16px;
    font-weight: 400;
    min-width: 375px;
    line-height: 1.4;
    color: #202123;
    overflow-x: hidden;
    margin: 0;
    padding: 0;

    * {
        box-sizing: border-box;
        margin: 0;
        padding: 0;
    }
    background-color: #d7d7d7;
}

#app {
    // height: 100%;
    // background-color: #d7d7d7;
}

.page {
    max-width: 1140px;
    margin: 0px auto;
    padding: 50px 40px 0 25px;
}
</style>
