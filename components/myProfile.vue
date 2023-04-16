<template>
	<view class="container">
		<form class="form-box">
			<view class="avatar-box">
				<image
					:src="avatar"
					class="avatar-img" />
				<view
					class="upload-avatar"
					@click="uploadAvatar">
					上传头像
				</view>
			</view>

			<view class="input-box">
				<input
					type="text"
					v-model="nickname"
					placeholder="请输入昵称" />
			</view>

			<view class="input-box">
				<input
					type="text"
					v-model="gender"
					placeholder="请输入性别" />
			</view>

			<view class="btn-box">
				<button
					class="confirm-btn"
					@click="confirm">
					确定
				</button>
			</view>
		</form>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				nickname: '', // 昵称
				gender: '', // 性别
				avatar: '' // 头像
			}
		},
		onLoad(options){
			this.avatar = options.avatarUrl
			this.nickname = options.nickname
			this.gender = options.gender
		},
		methods: {
			// 上传头像
			uploadAvatar() {
				uni.chooseImage({
					count: 1,
					sizeType: ['compressed'],
					sourceType: ['album', 'camera'],
					success: res => {
						this.avatar = res.tempFilePaths[0]
					}
				})
			},
			// 点击确定按钮
			confirm() {
				console.log('昵称：', this.nickname)
				console.log('性别：', this.gender)
				console.log('头像：', this.avatar)
				// TODO: 提交表单，发送请求
			}
		}
	}
</script>

<style scoped>
	.container {
		margin-top: 60px;
		display: flex;
		justify-content: center;
		align-items: center;
		height: 100%;
		background-color: #eee;
	}

	.form-box {
		width: 80vw;
		max-width: 400px;
		height: auto;
		border: 2px solid #ddd;
		border-radius: 10px;
		background-color: #fff;
		padding: 20px;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}

	.avatar-box {
		margin-left: 25px;
		padding-bottom: 10px;
		position: relative;
		width: 120px;
		height: 120px;
	}

	.avatar-img {
		width: 100%;
		height: 100%;
		border-radius: 50%;
	}

	.upload-avatar {
		position: absolute;
		bottom: -10px;
		left: 0;
		width: 100%;
		height: 30px;
		line-height: 30px;
		text-align: center;
		background-color: #333;
		color: #fff;
		border-radius: 0 0 10px 10px;
	}

	.input-box {
		width: 100%;
		margin: 10px 0;
		box-sizing: border-box;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	input {
		width: 100%;
		height: 30px;
		border: 1px solid #ddd;
		border-radius: 5px;
		text-align: center;
		font-size: 16px;
	}

	.btn-box {
		width: 100%;
		display: flex;
		justify-content: center;
	}

	button {
		width: 50%;
		height: 40px;
		border: none;
		border-radius: 5px;
		font-size: 18px;
		color: #fff;
		background-color: #333;
		cursor: pointer;
	}

	.confirm-btn:hover {
		background-color: #444;
	}
</style>
