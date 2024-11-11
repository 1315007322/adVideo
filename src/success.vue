<template>
  <div>
    <p v-if="!isSuccess">恭喜您获得现金奖励 <span style='font-size: 30px;font-weight:blod;color: red'>{{amount}}</span></p>

    <p v-else>提现金额为 <span style='font-size: 30px;font-weight: bold;color: red'>{{amount}}</span> 元，请留意钱包注意查收！</p>

    <img src='https://file-1330837989.cos.ap-guangzhou.myqcloud.com/amount.png' width='50'/>
  </div>
</template>
<script lang='ts'>
import { watch } from "vue";
export default {
  props: {
    amount: Number,
    isSuccess:Boolean,
  },

};
</script>
