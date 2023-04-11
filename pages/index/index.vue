<template>
	<view class="THC">
		<view class="search-box">
		  <input class="search-input" type="text" v-model="inputValue" placeholder="请输入搜索关键字"/>
		  <button @click="onSearchClick">搜索</button>
		</view>
		<view class="img">
			<swiper indicator-dots autoplay>
			<swiper-item>
			  <image src="http://imgapi.xl0408.top/index.php" style="height: 500rpx;width: 100%; background-color: bisque;"/>
			</swiper-item>
			<swiper-item>
			  <image src="https://imgapi.xl0408.top/index.php" style="height: 500rpx;width: 100%; background-color: bisque;"/>
			</swiper-item>
			</swiper>
		</view>
		<view class="Text">
			<font>热点资讯</font>
		</view>
		<view class="index" :onLoad="handleLoad">
			 <view class="container-wrapper">
			    <view v-for="(item, index) in list" :key="index" class="container">
			      <image :src="item.src" class="picture"/>
			      <view class="content">
			       <navigator class="title" :url="'/pages/intro/intro?id=' + item.ID" open-type="navigate">{{ item.Title }}</navigator>
			        <view class="introduction">{{ item.Intro }}</view>
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
				inputValue:"",
				list:[]
			}
		},
		onLoad(options) {
		  wx.request({
			  url: 'http://localhost:8081/index/getData',
			  success: (res) => {
				this.list = res.data
			}
		})
		},
		methods: {
			  handleLoad() {
			    let container = this.$refs.container;
			    let contentHeight = container.clientHeight;
			    container.style.height = contentHeight + 'px';
			  },
			// 处理搜索查询操作
			onSearchClick() {
				wx.request({
				  url: '',
				  method: 'GET',
				  data: {
					keyword: this.inputValue // 将输入的搜索关键字作为请求参数发送给后端
				  },
				  success: function(res) {
					console.log(res.data)
				  },
				  fail: function(err) {
					console.error(err)
				  }
				})
			}
		}
	}
</script>

<style>
		.container-wrapper {
		  display: flex;
		  flex-direction: column;
		  justify-content: center;
		  align-items: center;
		}
		
		.container {
		  display: flex;
		  width: 80%;
		  margin-bottom: 20px;
		  border: 1px solid gray;
		  border-radius: 5px;
		  box-shadow: 5px 5px 5px gray;
		  padding: 10px;
		}
		.picture {
		  width: 100px;
		  height: 100px;
		  border-radius: 50%;
		}
		
		.content {
		  display: flex;
		  flex-direction: column;
		  margin-left: 10px;
		  flex: 1;
		  /* 设置一个固定的高度 */
		  /* 设置溢出内容的显示方式 */
		  overflow: hidden;
		}
		
		.title {
		  font-size: 16px;
		  font-weight: bold;
		  color: black;
		  margin-bottom: 5px;
		}
		
		.introduction {
		  font-size: 14px;
		  color: gray;
		}
		
		.container-wrapper .container .item {
		  width: 100%; /* 将每个循环项的宽度设置为 100% */
		}
	.index{
		margin-top: 50rpx;
		display: flex; /* 将容器变为 flex 布局 */
		justify-content: center; /* 水平方向上内容居中对齐 */
		align-items: center; /* 竖直方向上内容居中对齐 */
		min-height:100px; /* 设置容器高度 */
	}
	button {
	  background-color: #007bff;
	  color: #fff;
	  border-color: #007bff;
	  border-radius: 5px;
	  font-size: 16px;
	  font-weight: bold;
	  padding: 10px 20px;
	}
	.search-box {
	  display: flex;
	  flex-direction: row;
	  align-items: center;
	  justify-content: center;
	  background-color: #fff;
	  padding: 5px;
	  border-radius: 20px;
	}
	
	.search-icon {
	  width: 20px;
	  height: 20px;
	  margin-right: 5px;
	}
	
	.search-input {
	  flex: 1;
	  font-size: 14px;
	  border: none;
	  outline: none;
	}
	.Text{
		margin-top: 10rpx;
	}
	font{
		font-style: italic;
		font-weight:bold;
	}
</style>
