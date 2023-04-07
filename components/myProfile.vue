<template>
  <view class="container">
    <view class="avatar-container">
      <image class="avatar" :src="avatarUrl" mode="aspectFill"></image>
      <view class="avatar-edit" @click="chooseImage">
        <i class="iconfont icon-edit"></i>
      </view>
    </view>
    <view class="form-field">
      <view class="label">昵称</view>
      <input class="input" type="text" v-model="nickname"></input>
    </view>
    <view class="form-field">
      <view class="label">性别</view>
      <radio-group @change="onGenderChange" :value="gender">
        <radio class="radio" value="male">男</radio>
        <radio class="radio" value="female">女</radio>
      </radio-group>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      avatarUrl: '',     // 头像地址
      nickname: '',      // 昵称
      gender: 'male',    // 性别
    }
  },

  methods: {
    // 点击修改头像
    chooseImage() {
      uni.chooseImage({
        count: 1,         // 只允许选择一张图片
        success: (res) => {
          const tempFilePaths = res.tempFilePaths
          this.uploadImage(tempFilePaths[0])
        }
      })
    },

    // 上传图片
    uploadImage(filePath) {
      uni.uploadFile({
        url: 'https://example.com/upload',   // 上传接口地址
        filePath: filePath,
        name: 'file',
        success: (res) => {
          const data = JSON.parse(res.data)
          if (data.code === 1) {
            this.avatarUrl = data.url
          } else {
            uni.showToast({
              icon: 'none',
              title: '上传失败，请重试！'
            })
          }
        },
        fail: (res) => {
          uni.showToast({
            icon: 'none',
            title: '上传失败，请重试！'
          })
        }
      })
    },

    // 切换性别
    onGenderChange(event) {
      this.gender = event.currentTarget.dataset.name
    },
  },

  mounted() {
    // 页面加载时获取用户信息
    this.avatarUrl = 'https://example.com/avatar.jpg'   // TODO: 获取当前用户头像地址
    this.nickname = '张三'   // TODO: 获取当前用户昵称
    this.gender = 'male'      // TODO: 获取当前用户性别
  }
}
</script>

<style lang="scss">
.container {
  padding: 20rpx;
}

.avatar-container {
  position: relative;
  width: 100%;
  height: 160rpx;
  margin-bottom: 40rpx;
}

.avatar {
  width: 100%;
  height: 100%;
  border-radius: 50%;
}

.avatar-edit {
  position: absolute;
  bottom: 0;
  right: 0;
  width: 80rpx;
  height: 80rpx;
  border-radius: 50%;
  background-color: #fff;
  display: flex;
  justify-content: center;
  align-items: center;
}

.iconfont {
  font-size: 30rpx;
}

.form-field {
  display: flex;
  flex-direction: row;
  align-items: center;
  height: 80rpx;
}

.label {
  width: 200rpx;
  font-size: 30rpx;
}

.input {
  flex-grow: 1;
  font-size: 30rpx;
  background-color: #f8f8f8;
  border: none;
  border-radius: 8rpx;
  padding: 0 20rpx;
}

.radio-group {
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
  margin-top: 20rpx;
}

.radio {
  margin-right: 100rpx;
}
</style>