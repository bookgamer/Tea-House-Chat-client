<template>
  <view>
    <image :src="qrcodeUrl" mode="widthFix" />
  </view>
</template>

<script>
export default {
  data() {
    return {
      qrcodeUrl: "" // 二维码图片地址
    };
  },
  created() {
    // 获取二维码图片地址
    // ...
  }
};
</script>

<style scoped>
</style>