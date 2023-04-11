<template>
	<view>
		<view class="top-wrapper">
			<view
				v-if="userInfo"
				class="avatar-wrapper"
				:style="{ backgroundImage: 'url(' + avatarUrl + ')' }">
				<!-- 如果有用户信息，则显示用户头像 -->
			</view>
			<view v-else>
				<!-- 如果没有用户信息，则显示默认头像 -->
				<image
					src="../../icon/white.png"
					class="default-avatar" />
			</view>
			<view
				class="nickname"
				v-if="wxuserInfo">
				{{ wxuserInfo.nickName }}
			</view>
			<view
				class="gender"
				v-if="userInfo">
				{{ genderText===0?'男':'女' }}
			</view>
			<button
				v-else
				class="login-btn"
				open-type="getUserInfo"
				@click="login">
				登录
			</button>
		</view>
		<view class="link-wrapper">
			<navigator
				:url="'../../components/myProfile?avatarUrl='+avatarUrl"
				class="link">
				我的资料
			</navigator>
			<navigator
				url="../../components/myCollection"
				class="link">
				我的收藏
			</navigator>
			<navigator
				url="../../components/publishArticle"
				class="link">
				发布文章
			</navigator>
			<navigator
				url="../../components/contactDev"
				class="link">
				联系开发人员
			</navigator>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				wxuserInfo:{},
				userInfo: false, // 用户信息
				hasUserInfo: false,
				canIUseGetUserProfile: false,
				genderText: 0 // 用户性别文本
			}
		},
		computed: {
			// 头像地址
			avatarUrl() {
				return this.userInfo ? this.wxuserInfo.avatarUrl : ''
			}
		},
		onLoad() {
			if (wx.getUserProfile) {
				this.canIUseGetUserProfile = true
			}
		},
		methods: {
			// 点击登陆按钮，进行微信授权登陆
			login() {
			// 推荐使用wx.getUserProfile获取用户信息，开发者每次通过该接口获取用户个人信息均需用户确认
			// 开发者妥善保管用户快速填写的头像昵称，避免重复弹窗
				wx.getUserProfile({
					desc: '登录本软件使用', // 声明获取用户个人信息后的用途，后续会展示在弹窗中，请谨慎填写
					success: res => {
						uni.request({
							url:'http://localhost:8081/login',
							method:'POST',
							data:{
								avatarUrl:res.userInfo.avatarUrl,
								nickName:res.userInfo.nickName,
								gender:res.userInfo.gender
							},
							success: suc => {
								if(suc.data.code===1){
									this.wxuserInfo = res.userInfo
									this.genderText = res.userInfo.gender
									this.userInfo = true
									wx.setStorage({
										key:"userId",
										data: suc.data.data
									})
								}else{
									uni.showToast({
										title: suc.data.msg,
										icon: 'none',
										duration: 2000
									})
								}
							},
							fail:err => {
								console.log("登录失败，接口报错："+err)
								uni.showToast({
									title: '登录失败，请稍后再试',
									icon: 'none',
									duration: 2000
								})
							}
						})
						console.log(this.wxuserInfo)
					}
				})
			}
		}
	}
</script>

<style scoped>
	.top-wrapper {
		display: flex;
		flex-flow: column nowrap;
		justify-content: center;
		align-items: center;
		margin-top: 40px;
		position: relative;
	}
	.avatar-wrapper {
		width: 100px;
		height: 100px;
		border-radius: 50%;
		background-size: cover;
		margin-bottom: 10px;
		animation: pulse 2s ease-in-out infinite;
	}
	.default-avatar {
		width: 100px;
		height: 100px;
		border-radius: 50%;
		background-color: #fff;
	}
	.nickname {
		font-family: 'Comic Sans MS';
		font-size: 20px;
		color: #444;
		margin-bottom: 10px;
	}
	.gender {
		font-family: 'Comic Sans MS';
		font-size: 16px;
		color: #666;
	}
	.login-btn {
		font-family: 'Comic Sans MS';
		font-size: 18px;
		color: #fff;
		background-color: #fbc02d;
		border: none;
		border-radius: 20px;
		padding: 10px 20px;
		margin-top: 40px;
	}
	.link-wrapper {
		display: flex;
		flex-flow: column nowrap;
		justify-content: center;
		align-items: center;
		margin-top: 60px;
	}
	.link {
		font-family: 'Comic Sans MS';
		font-size: 18px;
		color: #444;
		margin: 10px 0;
		text-decoration: none;
	}

	@keyframes pulse {
		0% {
			transform: scale(1);
		}
		50% {
			transform: scale(1.1);
		}
		100% {
			transform: scale(1);
		}
	}
</style>
