<template>
	<view class="container">
		<!-- 评论列表 -->
		<view class="comment-list">
			<!-- 评论项 -->
			<view class="comment-item" v-for="(comment, index) in comments" :key="index">
				<!-- 评论者头像 -->
				<image class="avatar" :src="comment.avatar" mode="aspectFill"></image>
				<!-- 评论内容 -->
				<view class="content">
					<!-- 评论者昵称 -->
					<text class="nickname">{{ comment.nickname }}</text>
					<!-- 评论正文 -->
					<text class="text">{{ comment.text }}</text>
					<!-- 评论图片 -->
					<view class="images" v-if="comment.images && comment.images.length > 0">
						<image class="image-item" v-for="(image, i) in comment.images" :key="i" :src="image"
							mode="aspectFill"></image>
					</view>
					<!-- 评论时间和操作 -->
					<view class="actions">
						<text class="time">{{ comment.time }}</text>
						<view class="buttons">
							<view class="button-item" @tap="toggleLike(comment)">
								<text class="iconfont"
									:class="{ 'icon-like': !comment.liked, 'icon-liked': comment.liked }"></text>
								<text>{{ comment.likes }}</text>
							</view>
							<view class="button-item" @tap="reply(comment)">
								<text class="iconfont icon-comment"></text>
								<text>{{ comment.replies }}</text>
							</view>
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 发表评论 -->
		<view class="comment-input">
			<input type="text" placeholder="写下你的评论..." v-model.trim="inputText" @confirm="submitComment" />
			<button @tap="submitComment">发送</button>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			// 评论数据
			comments: [
				{
					id: 1,
					avatar: "https://thirdwx.qlogo.cn/mmopen/vi_32/emhMvargkfuur8sxGGNzxpPWV7iavV6IYMGVWkMjmHTR9iaVypUsnF6F6uG0o7wqF0kObibNFxfEhc1cuRfAK3vJw/132",
					nickname: "张三",
					text: "这篇文章写得很好，赞一个！",
					images: [],
					time: "1小时前",
					likes: 12,
					replies: 3,
					liked: false,
				},
				{
					id: 2,
					avatar: "https://thirdwx.qlogo.cn/mmopen/vi_32/emhMvargkfuur8sxGGNzxpPWV7iavV6IYMGVWkMjmHTR9iaVypUsnF6F6uG0o7wqF0kObibNFxfEhc1cuRfAK3vJw/132",
					nickname: "李四",
					text: "我觉得作者有点片面，没有考虑到其他方面的因素。spring boot是spring全家桶中最常用的一种，也企业开发和java编程中，必不可缺的一种技术框架",
					images: ["https://example.com/image1.jpg", "https://example.com/image2.jpg"],
					time: "2小时前",
					likes: 5,
					replies: 6,
					liked: true,
				},
				{
					id: 3,
					avatar: "https://thirdwx.qlogo.cn/mmopen/vi_32/emhMvargkfuur8sxGGNzxpPWV7iavV6IYMGVWkMjmHTR9iaVypUsnF6F6uG0o7wqF0kObibNFxfEhc1cuRfAK3vJw/132",
					nickname: "王五",
					text: "有没有人知道这个问题的答案？求解答！",
					images: [],
					time: "3小时前",
					likes: 0,
					replies: 0,
					liked: false,
				},
			],
			// 输入框内容
			inputText: "",
		};
	},
	methods: {
		// 切换点赞状态
		toggleLike(comment) {
			comment.liked = !comment.liked;
			if (comment.liked) {
				comment.likes++;
			} else {
				comment.likes--;
			}
		},
		// 回复评论
		reply(comment) {
			this.inputText = "@" + comment.nickname + " ";
		},
		// 提交评论
		submitComment() {
			if (this.inputText) {
				// 模拟生成一个新的评论对象
				let newComment = {
					id: this.comments.length + 1,
					avatar: "https://thirdwx.qlogo.cn/mmopen/vi_32/emhMvargkfuur8sxGGNzxpPWV7iavV6IYMGVWkMjmHTR9iaVypUsnF6F6uG0o7wqF0kObibNFxfEhc1cuRfAK3vJw/132", // 模拟自己的头像
					nickname: "赵六", // 模拟自己的昵称
					text: this.inputText,
					images: [],
					time: "刚刚",
					likes: 0,
					replies: 0,
					liked: false,
				};
				// 将新的评论对象添加到评论列表中
				this.comments.unshift(newComment);
				// 清空输入框内容
				this.inputText = "";
			} else {
				uni.showToast({
					title: "请输入评论内容",
					icon: "none",
				});
			}
		},
	},
};
</script>
		
<style>
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
	width: 50px;
	height: 50px;
	border-radius: 50%;
}

.content {
	flex: 1;
	margin-left: 10px;
}

.nickname {
	font-size: 16px;
	font-weight: bold;
}

.text {
	margin-top: 5px;
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
	width: 60px;
	height: 40px;
	margin-left: 10px;
	background-color: #0099ff;
	color: #fff;
	border-radius: 20px;
}
</style>
