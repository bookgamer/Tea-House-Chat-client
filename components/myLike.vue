<template>
  <view class="container">
    <view class="form">
      <view class="form-item">
        <text class="label">文章标题</text>
        <input class="input" type="text" v-model="title" placeholder="请输入文章标题" />
      </view>
      <view class="form-item">
        <text class="label">文章介绍</text>
        <textarea class="textarea" v-model="intro" placeholder="请输入文章介绍" />
      </view>
      <view class="form-item">
        <text class="label">文章内容</text>
        <textarea class="textarea" v-model="content" placeholder="请输入文章内容" />
      </view>
      <button class="button" @click="submit">确定</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      title: "", // 文章标题
      intro: "", // 文章介绍
      content: "", // 文章内容
      isPublic: 0 // 0是 1否 公开
    };
  },
  methods: {
    // 提交表单
    submit() {
      // 检查是否有空值
      if (!this.title || !this.intro || !this.content) {
        uni.showToast({
          title: "请填写完整信息",
          icon: "none",
        });
        return;
      }
      // 判断用户是否登录
      wx.getStorage({
        key: 'userId',
        success: res => {
          // 显示弹窗
          wx.showModal({
            title: '确认权限',
            content: "发布内容是否公开",
            confirmText: '公开',
            cancelText: '不公开',
            success: (res) => {
              if (res.confirm) {
                //点击符合按钮
                uni.request({
                  url: "https://example.com/api/post", // 后端接口地址
                  method: "POST", // 请求方式
                  data: {
                    title: this.title, // 文章标题
                    intro: this.intro, // 文章介绍
                    content: this.content, // 文章内容
                    isPublic: this.isPublic, // 是否公开
                  },
                  success(res) {
                    // 请求成功
                    uni.showToast({
                      title: "发布成功",
                      icon: "success",
                    });
                    // 跳转到首页或其他页面
                    uni.navigateTo({
                      url: "/pages/index/index",
                    });
                  },
                  fail(err) {
                    // 请求失败
                    uni.showToast({
                      title: "发布",
                      icon: 'none',
                      duration: 2000
                    })
                  }
                })
              } else if (res.cancel) {
                this.isPublic = 1
                //点击不符合按钮
                uni.request({
                  url: "https://example.com/api/post", // 后端接口地址
                  method: "POST", // 请求方式
                  data: {
                    title: this.title, // 文章标题
                    intro: this.intro, // 文章介绍
                    content: this.content, // 文章内容
                    isPublic: this.isPublic, // 是否公开
                  },
                  success(res) {
                    // 请求成功
                    uni.showToast({
                      title: "发布成功",
                      icon: "success",
                    });
                    // 跳转到首页或其他页面
                    uni.navigateTo({
                      url: "/pages/index/index",
                    });
                  },
                  fail(err) {
                    // 请求失败
                    uni.showToast({
                      title: "发布",
                      icon: 'none',
                      duration: 2000
                    })
                  }
                })
              }
            }
          })
        },
        fail: err => {
          uni.showToast({
            title: '请登录',
            icon: 'none',
            duration: 2000
          })
        }
      })

    }
  }
}
</script>
<style></style>