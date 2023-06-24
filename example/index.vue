<template>
    <taro-qr-code-vue :value="state.v" ref="qrEle"></taro-qr-code-vue>
    <input v-model="state.v" />
    <button @click="refsh">刷新</button>
</template>
<script setup>
import { reactive, ref } from 'vue'
import TaroQrCodeVue from '../../views/taroqrcodevue/index';

const state = reactive({
    v: 'ffff'
});
const qrEle = ref(null);

function refsh() {
    console.log(state.v);
    qrEle.value.renderCanvas();
}
</script>