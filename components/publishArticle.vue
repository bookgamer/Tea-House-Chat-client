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
				<textarea class="textarea" v-model="content" placeholder="请输入文章内容"  maxlength="1020" />
			</view>
			<button class="button" @click="submit">
				确定
			</button>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			title: '', // 文章标题
			intro: '', // 文章介绍
			content: '', // 文章内容
			isPublic: 0 // 0是 1否 公开
		}
	},
	methods: {
		// 提交表单
		submit() {
			// 检查是否有空值
			if (!this.title || !this.intro || !this.content) {
				uni.showToast({
					title: '请填写完整信息',
					icon: 'none'
				})
				return
			}
			// 判断用户是否登录
			wx.getStorage({
				key: 'userId',
				success: res1 => {
					// 显示弹窗
					wx.showModal({
						title: '确认权限',
						content: '发布内容是否公开',
						confirmText: '公开',
						cancelText: '不公开',
						success: res => {
							if (res.confirm) {
								//点击符合按钮
								uni.request({
									url: 'http://localhost:8081/api/addArticle', // 后端接口地址
									method: 'POST', // 请求方式
									data: {
										userId: res1.data,
										title: this.title, // 文章标题
										intro: this.intro, // 文章介绍
										content: this.content, // 文章内容
										publics: this.isPublic // 是否公开
									},
									success(res) {
										wx.switchTab({
										  url: '/pages/index/index',
										  success: function() {
										    wx.showToast({
										      title: '发布成功',
										      icon: 'success',
										      duration: 1500
										    })
										  }
										})
									},
									fail(err) {
										// 请求失败
										uni.showToast({
											title: '发布',
											icon: 'none',
											duration: 2000
										})
									}
								})
							} else if (res.cancel) {
								this.isPublic = 1
								//点击不符合按钮
								uni.request({
									url: 'http://localhost:8081/api/addArticle', // 后端接口地址
									method: 'POST', // 请求方式
									data: {
										userId: res1.data,
										title: this.title, // 文章标题
										intro: this.intro, // 文章介绍
										content: this.content, // 文章内容
										publics: this.isPublic // 是否公开
									},
									success(res) {
										if (res.data.code === 1) {
											wx.switchTab({
											  url: '/pages/index/index',
											  success: function() {
											    wx.showToast({
											      title: '发布成功',
											      icon: 'success',
											      duration: 1500
											    })
											  }
											})
										} else {
											uni.showToast({
												title: '系统未知错误，请稍后重试',
												icon: 'none',
												duration: 2000
											})
										}
									},
									fail(err) {
										// 请求失败
										uni.showToast({
											title: '发布失败',
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
<style>
	text{
		font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
		font-size: 16px;
		color: #333;
	}
	.form-item {
	    margin-bottom: 10px;
		overflow: hidden;
	  }
	  .label {
	    display: block;
	    font-size: 14px;
	    color: #333;
	    margin-bottom: 5px;
	  }
	  .textarea {
	    display: block;
	    width: 100%;
	    padding: 10px;
	    box-sizing: border-box;
	    border: 1px solid #ccc;
	    border-radius: 3px;
	    font-size: 14px;
	    color: #333;
	  }
	  .button {
	    display: block;
	    width: 100%;
	    padding: 10px;
	    box-sizing: border-box;
	    border-radius: 3px;
	    background-color: #409eff;
	    color: #fff;
	    font-size: 16px;
	    text-align: center;
	  }
</style>
