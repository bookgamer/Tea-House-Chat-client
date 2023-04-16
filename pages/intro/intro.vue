<template>
	<!-- 文章内容 -->
	<view
		class="article"
		:onLoad="handleLoad">
		<view
			v-if="!loading"
			class="background">
			<view class="container">
				<view class="title">{{ article.title }}</view>
				<view class="content">{{ article.content }}</view>
				<view class="actions">
					<view
						class="action"
						@click="like">
						<image
							:src="isLiked ? '../../icon/liked.png' : '../../icon/like.png'"
							:class="{ liked: isLiked }" />
						<!-- 点赞 -->
						<view class="like-count">{{ article.likes }}</view>
					</view>
					<button
						open-type="share"
						class="action">
						分享
					</button>
					<view
						class="action"
						@click="collect">
						<image
							:src="
								isCollected ? '../../icon/crollerd.png' : '../../icon/croller.png'
							"
							:class="{ collected: isCollected }" />
						<!-- 收藏 -->
					</view>
				</view>
			</view>
		</view>
		<!-- 加载中 -->
		<view
			v-else
			class="loading">
			<text>加载中...</text>
		</view>
		<!-- 评论 -->
		<view
			class="comments"
			:onLoad="handleLoad">
			<view class="container">
				<!-- 发表评论 -->
				<view class="comment-input">
					<input
						type="text"
						placeholder="写下你的评论..."
						v-model.trim="content"
						@confirm="submitComment" />
					<button @tap="submitComment">发送</button>
				</view>
				<!-- 评论列表 -->
				<view class="comment-list">
					<!-- 评论项 -->
					<view
						class="comment-item"
						v-for="(comment, index) in comments"
						:key="index">
						<!-- 评论者头像 -->
						<image
							class="avatar"
							:src="comment.avatarUrl"
							mode="aspectFill" />
						<!-- 评论内容 -->
						<view class="content">
							<view>
								<!-- 评论者昵称 -->
								<text class="nickname">{{ comment.nickName }}</text>
							</view>
							<!-- 评论正文 -->
							<text class="text">{{ comment.content }}</text>
							<!-- 评论图片 -->
							<view
								class="images"
								v-if="comment.images && comment.images.length > 0">
								<image
									class="image-item"
									v-for="(image, i) in comment.images"
									:key="i"
									:src="image"
									mode="aspectFill" />
							</view>
							<!-- 评论时间和操作 -->
							<view class="actions">
								<text class="time">{{ comment.time }}</text>
								<view class="buttons">
									<view
										class="button-item"
										@tap="toggleLike(comment)">
										<text
											class="iconfont"
											:class="{
												'icon-like': !comment.liked,
												'icon-liked': comment.liked
											}"></text>
										<text>{{ comment.likes }}</text>
									</view>
									<view
										class="button-item"
										@tap="reply(comment)">
										<text class="iconfont icon-comment"></text>
										<text>{{ comment.replies }}</text>
									</view>
								</view>
							</view>
						</view>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				loading: true, // 是否正在加载文章
				article: {}, // 文章内容
				comments: [], // 评论列表
				isLiked: false, // 是否已经点赞
				isCollected: false, // 是否已经收藏
				content: '', // 评论内容
				id: 0
			}
		},
		onLoad(options) {
			// 页面加载时向后端发送请求获取文章内容和评论列表
			console.log('开始请求页面加载所需要的数据')
			this.getArticle(options.id)
			this.getCommentList(options.id)
			this.getCollect(options.id)
			this.getLike()
			this.id = options.id
			console.log('请求页面所需要的数据加载完毕')
		},
		methods: {
			onInput(e) {
				this.textNum = e.detail.value.length
			},
			// 提交评论
			submitComment() {
				wx.getStorage({
					key: 'userId',
					success: res => {
						uni.request({
							url: `http://localhost:8081/api/Addcomments?userId=${res.data}`,
							method: 'POST',
							data: {
								articleId: this.id,
								content: this.content
							},
							success: res => {
								// 添加评论成功后，清空评论内容
								uni.showToast({
									title: '评论成功',
									icon: 'success',
									duration: 2000
								})
								this.content = ''
								this.getCommentList(this.id)
							},
							fail: err => {
								console.log('添加评论失败', err)
								uni.showToast({
									title: '评论失败，请稍后再试',
									icon: 'none',
									duration: 2000
								})
							}
						})
					},
					fail(err) {
						uni.showToast({
							title: '请登录',
							icon: 'none',
							duration: 2000
						})
					}
				})
			},
			getArticle(id) {
				// 发送请求获取文章内容
				uni.request({
					url: `http://localhost:8081/api/article?id=${id}`,
					success: res => {
						// 请求成功则将文章内容赋值给 article 变量，同时将 loading 变量置为 false
						this.article = res.data
						// console.log("后端响应的文章内容"+res.data)
						this.loading = false
					},
					fail: err => {
						console.error(err)
					}
				})
			},
			getCommentList(id) {
				// 发送请求获取评论列表
				wx.getStorage({
					key: 'userId',
					success: res1 => {
						uni.request({
							url: `http://localhost:8081/api/comments?id=${id}&userId=${res1.data}`,
							success: res => {
								res.data.forEach(item => {
									// 获取每个对象中的time字段
									var time1 = item.time
									// 计算时间差并格式化时间
									var date1 = new Date(time1.replace(/-/g, '/'))
									var date2 = new Date()
									var diff = date2.getTime() - date1.getTime()
									var seconds = Math.floor(diff / 1000)
									var minutes = Math.floor(seconds / 60)
									var hours = Math.floor(minutes / 60)
									var days = Math.floor(hours / 24)
									var result = ''
									if (days > 0) {
										result = days + '天前'
									} else if (hours > 0) {
										result = hours + '小时前'
									} else if (minutes > 0) {
										result = minutes + '分钟前'
									} else {
										result = seconds + '秒前'
									}
									// 修改每个对象中的time字段
									item.time = result
								})
								// 请求成功则将评论列表赋值给comments变量
								this.comments = res.data
								console.log(this.comments)
							},
							fail: error => {
								console.error(error)
							}
						})
					},
					fail: err => {
						uni.request({
							url: `http://localhost:8081/api/comments?id=${id}&userId=0`,
							success: res => {
								res.data.forEach(item => {
									// 获取每个对象中的time字段
									var time1 = item.time
									// 计算时间差并格式化时间
									var date1 = new Date(time1.replace(/-/g, '/'))
									var date2 = new Date()
									var diff = date2.getTime() - date1.getTime()
									var seconds = Math.floor(diff / 1000)
									var minutes = Math.floor(seconds / 60)
									var hours = Math.floor(minutes / 60)
									var days = Math.floor(hours / 24)
									var result = ''
									if (days > 0) {
										result = days + '天前'
									} else if (hours > 0) {
										result = hours + '小时前'
									} else if (minutes > 0) {
										result = minutes + '分钟前'
									} else {
										result = seconds + '秒前'
									}
									// 修改每个对象中的time字段
									item.time = result
								})
								// 请求成功则将评论列表赋值给comments变量
								this.comments = res.data
								console.log(this.comments)
							},
							fail: error => {
								console.error(error)
							}
						})
					}
				})
			},
			like() {
				wx.getStorage({
					key: 'userId',
					success: res => {
						uni.request({
							url: 'http://localhost:8081/api/like',
							method: 'POST',
							data: {
								articleId: this.id,
								userId: res.data
							},
							success: res1 => {
								if (this.isLiked) {
									this.isLiked = false
								} else {
									this.isLiked = true
								}
								this.getArticle(this.id)
							},
							fail: err => {
								console.log('点赞失败，错误信息：', err)
							}
						})
					},
					fail(err) {
						uni.showToast({
							title: '请登录',
							icon: 'none',
							duration: 2000
						})
					}
				})
			},
			getLike() {
				wx.getStorage({
					key: 'userId',
					success: res => {
						uni.request({
							url: 'http://localhost:8081/api/like',
							method: 'GET',
							data: {
								articleId: this.id,
								userId: res.data
							},
							success: res1 => {
								console.log('查询点赞，后端返回数据' + res1.data)
								this.isLiked = res1.data
							},
							fail: err => {
								console.log('查询点赞接口报错，错误信息：', err)
							}
						})
					},
					fail(err) {
						uni.showToast({
							title: '请登录',
							icon: 'none',
							duration: 2000
						})
					}
				})
			},
			share() {
				// 分享
				// 这里使用uni-app的分享功能，将文章内容进行分享
				let that = this
				return {
					title: that.article.title,
				 imageUrl: that.article.src,
					path: '/pages/article/index?id=' + that.id
				}
			},
			collect() {
				wx.getStorage({
					key: 'userId',
					success: res1 => {
						uni.request({
							url: 'http://localhost:8081/api/collect',
							method: 'POST',
							data: {
								articleId: this.id,
								userId: res1.data
							},
							success: res => {
								this.isCollected = res.data
								if(res.data===true){
									console.log("收藏成功！")
								}else{
									console.log("取消收藏成功！")
								}
								
							},
							fail: err => {
								console.log('收藏失败，错误信息：', err)
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
			},
			getCollect(id){
				wx.getStorage({
					key: 'userId',
					success: res1 => {
						uni.request({
							url: 'http://localhost:8081/api/getCollect',
							method: 'GET',
							data: {
								articleId: id,
								userId: res1.data
							},
							success: res => {
								console.log('查询收藏成功，响应结果：', res)
								this.isCollected = res.data
							},
							fail: err => {
								console.log('查询收藏失败，错误信息：', err)
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
			},
			toggleLike(comment) {
				wx.getStorage({
					key: 'userId',
					success: res => {
						uni.request({
							url: 'http://localhost:8081/api/CommentLikes',
							method: 'POST',
							data: {
								commentId: comment.id,
								userId: res.data
							},
							success: res1 => {
								this.getCommentList(this.id)
							},
							fail: err => {
								console.log('查询评论接口报错，错误信息：', err)
							}
						})
					},
					fail(err) {
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
	.like-count {
		font-size: 14px;
		color: #666;
		margin-left: 5px;
	}
	/* .container {
	padding: 10rpx;
} */
	.icon-like:before {
		content: '\2665'; /* 这是一个心形图标的 Unicode 码 */
		font-size: 20px; /* 调整图标大小 */
		color: #999;
	}

	.icon-liked:before {
		content: '\2665'; /* 这是一个心形图标的 Unicode 码 */
		font-size: 20px; /* 调整图标大小 */
		color: red; /* 点赞时填充为红色 */
	}
	.icon-comment:before {
		content: '\1F4AC'; /* 这是一个气泡图标的 Unicode 码 */
		font-size: 20px; /* 调整图标大小 */
		color: #999;
	}

	.icon-commented:before {
		content: '\1F4AC'; /* 这是一个气泡图标的 Unicode 码 */
		font-size: 20px; /* 调整图标大小 */
		color: red; /* 点击评论按钮时填充为红色 */
	}
	.background {
		/* background-image: url('https://tuapi.eees.cc/api.php?category=dongman&type=302&px=pc'); */
		background-size: cover;
		width: 100%;
		height: 100%;
		background-position: center;
		border: 1px solid gray;
		border-radius: 5px;
		box-shadow: 5px 5px 5px gray;
	}

	/* .container {
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	padding: 20px;
} */

	.loading {
		display: flex;
		justify-content: center;
		align-items: center;
		height: 100vh;
	}

	.article {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		min-height: 100px;

		/* margin-top: 20px; */
	}

	.title {
		font-family: Comic Sans MS;
		font-size: 24px;
		font-weight: bold;
		margin: 10px 0;
		color: #000000;
		text-align: center;
	}

	.actions {
		display: flex;
		flex-direction: row;
		justify-content: center;
		align-items: center;
		margin: 10px 0;
	}

	.action {
		margin-right: 10px;
	}

	.action image {
		width: 30px;
		height: 30px;
	}

	.comments {
		width: 100%;
		border: 1px solid gray;
		border-radius: 5px;
		box-shadow: 5px 5px 5px gray;
		margin: 20px 0;
		min-height: 100px;
	}

	.content {
		font-family: Comic Sans MS;
		font-size: 20px;
		line-height: 30px;
		color: #666;
	}

	.container {
		display: flex;
		flex-direction: column;
		height: 100%;
	}

	.comment-list {
		flex: 1;
		overflow-y: scroll;
	}

	.comment-item {
		display: flex;
		padding: 10px;
		border-bottom: 1px solid #eee;
	}

	.avatar {
		width: 30px;
		height: 30px;
		border-radius: 50%;
	}

	.content {
		flex: 1;
		margin-left: 10px;
		/* 设置一个固定的高度 */
		/* 设置溢出内容的显示方式 */
		overflow: hidden;
	}

	.nickname {
		font-size: 16px;
		font-weight: bold;
	}

	.text {
		margin-top: 5px;
		font-size: 20px;
	}

	.images {
		display: flex;
		flex-wrap: wrap;
		margin-top: 5px;
	}

	.image-item {
		width: calc(33.33% - 10px);
		height: calc(33.33% - 10px);
		margin-right: 5px;
		margin-bottom: 5px;
	}

	.actions {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-top: 5px;
	}

	.time {
		font-size: 15px;
		color: #999;
	}

	.buttons {
		display: flex;
	}

	.button-item {
		display: flex;
		align-items: center;
		margin-left: 10px;
	}

	.iconfont {
		font-size: 18px;
	}

	.icon-like {
		color: #999;
	}

	.icon-liked {
		color: #f00;
	}

	.comment-input {
		display: flex;
		align-items: center;
		padding: 10px;
		border-top: 1px solid #eee;
	}

	input {
		flex: 1;
		height: 40px;
		padding: 0 10px;
		border: 1px solid #ccc;
		border-radius: 20px;
	}

	button {
		height: 40px;
		margin-left: 10px;
		background-color: #0099ff;
		color: #fff;
		border-radius: 20px;
	}
</style>
