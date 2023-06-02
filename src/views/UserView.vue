<template>
  <h1>我是用户</h1>
  <h2>aaaaaaaaaaa</h2>
  <h3>nnnnnnnnn</h3>
  <img :src="qrCodeImg">
</template>

<script>
import { getQrCode, getQrCodeKey, checkQrCode } from "@/util/server";
import { getTimeStamp } from "@/util/formatDate";
export default {
  name: "UserView",
  data() {
    return {
      key: "",
      qrCodeImg: "",
      timer: null,
    };
  },
  created(){
    this.init();
  },
  methods: {
    async init() {
      await this.getQrCodeKey()
      await this.getQrCode();
      await this.checkQrCode();
    },
    async getQrCodeKey() {
      const res = await getQrCodeKey({ tiemstap: getTimeStamp() });
      console.log(res);
      this.key = res.data.unikey;
    },
    async getQrCode() {
      const res = await getQrCode({
        key: this.key,
        qrimg: true,
        timestamp: getTimeStamp(),
      });
      console.log(res);
      this.qrCodeImg = res.data.data.qrimg;
      console.log(this.qrCodeImg);
    },

    async checkQrCode() {
      this.timer = setInterval(async () => {
        // 定时器已被清空或已处于登录状态 则不向下执行
        if (!this.timer || this.$store.state.isLogin) return;

        const res = await checkQrCode({
          key: this.key,
          timestamp: getTimeStamp(),
        });
        console.log(res);
        // if (res.data.code === 800) {
        //   // 二维码过期
        //   this.clearChecker();
        //   this.init();
        // } else if (res.data.code === 803) {
        //   // 授权成功
        //   this.clearChecker();
        //   this.getAccountInfo();
        // }
      }, 2000);
    },
  },
};
</script>

<style>
img {
    height: 120px;
  }

</style>
