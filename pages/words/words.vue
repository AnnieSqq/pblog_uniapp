<template>
	<view>
		<!-- 留言展示板 -->
		<view class="words_outer">
			<view
				class="words_card"
				v-for="(item, index) in wordList"
				:key="index"
			>
				<view>{{ item.visitor_name }} {{ item.release_time }}</view>
				<text>{{ item.content }}</text>
			</view>
			<view class="words_info">
				<uni-icons
					v-if="wordListInfo.page < wordListInfo.pages"
					type="spinner-cycle"
				></uni-icons>
				<text v-else>--到底了--</text>
			</view>
		</view>
		<!-- 固定留言栏 -->
		<view class="interact_fixed">
			<!-- 留言框 -->
			<view
				:class="{
					word_input_outer: true,
					word_input_focus: iswordInputFocus
				}"
			>
				<uni-icons
					type="chatbubble"
					size="20"
					color="#6699CC"
				></uni-icons>
				<input
					type="text"
					v-model="wordInput"
					@focus="iswordInputFocus = true"
					@blur="iswordInputFocus = false"
					@confirm="handlewordSend"
				/>
				<uni-icons
					type="paperplane"
					size="20"
					@click="handlewordSend"
				></uni-icons>
			</view>
		</view>
		<view class="interact_fixed_holder"></view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			// 留言输入
			wordInput: '',
			// 输入框是否聚焦
			iswordInputFocus: false,
			wordList: [],
			wordListInfo: {
				page: 0,
				pages: 1
			}
		}
	},
	methods: {
		// 发送留言
		handlewordSend() {
			if (this.wordInput === '') {
				return uni.showToast({
					title: '不能发空的留言哦'
				})
			}
			this.wordInput = ''
			uni.showToast({
				title: '留言成功'
			})
		},
		// 获取留言列表
		getwordList() {
			const that = this
			uni.request({
				url: 'http://api.pblog.anniesqq.com/visitor/read/comments',
				data: {
					page: that.wordListInfo.page + 1,
					action: 'leaveword'
				},
				success(res) {
					that.wordList = that.wordList.concat(res.data.data.comments)
					that.wordListInfo = {
						page: res.data.data.page,
						size: res.data.data.size,
						total: res.data.data.total,
						pages: res.data.data.pages
					}
				}
			})
		}
	},
	onLoad() {
		this.getwordList()
	},
	onReachBottom() {
		if (this.wordListInfo.page < this.wordListInfo.pages) {
			// 由于加载过快，设置定时器来使加载过程更明显
			setTimeout(() => {
				this.getwordList()
			}, 1000)
		}
	}
}
</script>

<style lang="scss" scoped>
// 留言展示容器
.words_outer {
	padding: 20rpx;
	margin-bottom: 6rpx;
	> .words_card {
		background-color: $uni-bg-color-grey;
		padding: 20rpx;
		> view {
			font-size: 24rpx;
			color: $uni-color-success;
		}
		> text {
			font-size: 32rpx;
		}
	}
	> .words_info {
		text-align: center;
		padding-top: 40rpx;
	}
}
// 固定互动栏
.interact_fixed {
	// 位置大小
	position: fixed;
	bottom: 0;
	height: 120rpx;
	width: 100%;
	// 外观样式
	background-color: #fff;
	box-shadow: 0 -2rpx 20rpx $uni-text-color-grey;
	// 内容居中
	display: flex;
	justify-content: center;
	align-items: center;
	> uni-icons {
		margin-right: 30rpx;
	}
}
// 固定互动栏占位
.interact_fixed_holder {
	height: 120rpx;
	width: 100%;
	margin: 20rpx 0;
}
// 留言输入框
.word_input_outer {
	display: flex;
	justify-content: center;
	align-items: center;
	// border: 1rpx solid $uni-text-color-grey;
	border-radius: 40rpx;
	background-color: $uni-bg-color-grey;
	box-shadow: 0 0 5rpx $uni-text-color-grey;
	> uni-icons {
		margin: 0 10rpx;
	}
	> input {
		width: 550rpx;
	}
}
.word_input_focus {
	background-color: #fff;
	box-shadow: 0 0 10rpx $uni-color-success;
}
</style>
