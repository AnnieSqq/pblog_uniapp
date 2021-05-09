<template>
	<view>
		<view class="read_outer">
			<MDParserHighlight
				:resource="articleData.content"
			></MDParserHighlight>
		</view>
		<view id="bottom"></view>
		<!-- 悬浮按钮 -->
		<view class="fixed_btns">
			<view class="btn_up btn" @click="handleScrollUp"></view>
			<view class="btn_down btn" @click="handleScrollDown()"></view>
		</view>
		<!-- 评论展示板 -->
		<view class="comments_outer">
			<view
				class="comments_card"
				v-for="(item, index) in commentList"
				:key="index"
			>
				<view>{{ item.visitor_name }} {{ item.release_time }}</view>
				<text>{{ item.content }}</text>
			</view>
			<view class="comments_info">
				<uni-icons
					v-if="commentListInfo.page < commentListInfo.pages"
					type="spinner-cycle"
				></uni-icons>
				<text v-else>--到底了--</text>
			</view>
		</view>
		<!-- 固定互动栏 -->
		<view class="interact_fixed">
			<uni-icons
				type="hand-thumbsup"
				size="20"
				color="#A63E3E"
				@click="handleLikeClick"
			></uni-icons>
			<uni-icons
				type="star"
				size="20"
				color="#f0ad4e"
				@click="handleCollectClick"
			></uni-icons>
			<!-- 评论框 -->
			<view
				:class="{
					comment_input_outer: true,
					comment_input_focus: isCommentInputFocus
				}"
			>
				<uni-icons
					type="chatbubble"
					size="20"
					color="#6699CC"
				></uni-icons>
				<input
					type="text"
					v-model="commentInput"
					@focus="isCommentInputFocus = true"
					@blur="isCommentInputFocus = false"
					@confirm="handleCommentSend"
				/>
				<uni-icons
					type="paperplane"
					size="20"
					@click="handleCommentSend"
				></uni-icons>
			</view>
		</view>
		<view class="interact_fixed_holder"></view>
	</view>
</template>

<script>
// markdown渲染插件
import MDParserHighlight from '../../components/cmder-MDParserHighlight/index.vue'
export default {
	data() {
		return {
			// 文章数据
			articleData: {},
			// 评论列表
			commentList: [],
			// 评论列表分页信息
			commentListInfo: {
				page: 0,
				size: 1,
				total: 0,
				pages: 1
			},
			// 评论输入框
			commentInput: '',
			// 评论输入框是否聚焦
			isCommentInputFocus: false
		}
	},
	components: { MDParserHighlight },
	methods: {
		// 获取文章内容
		getArticleData() {
			let id = this.articleData.id
			const that = this
			uni.request({
				url: 'http://api.pblog.anniesqq.com/visitor/read/article/' + id,
				success(res) {
					uni.setNavigationBarTitle({
						title: res.data.data.title
					})
					that.articleData = res.data.data
				}
			})
		},
		// 获取评论列表
		getCommentList() {
			const that = this
			uni.request({
				url: 'http://api.pblog.anniesqq.com/visitor/read/comments',
				data: {
					page: that.commentListInfo.page + 1,
					article: that.articleData.id,
					action: 'comment'
				},
				success(res) {
					that.commentList = that.commentList.concat(
						res.data.data.comments
					)
					that.commentListInfo = {
						page: res.data.data.page,
						size: res.data.data.size,
						total: res.data.data.total,
						pages: res.data.data.pages
					}
				}
			})
		},
		// 回到顶部
		handleScrollUp() {
			uni.pageScrollTo({
				scrollTop: 0
			})
		},
		// 到底部
		handleScrollDown() {
			uni.pageScrollTo({
				selector: '#bottom'
			})
		}, // 点赞
		handleLikeClick() {
			uni.showToast({
				title: '点赞成功'
			})
		},
		// 收藏
		handleCollectClick() {
			uni.showToast({
				title: '收藏成功'
			})
		},
		// 评论
		handleCommentSend() {
			if(this.commentInput===''){
				return uni.showToast({
					title:'不能发空的评论哦'
				})
			}
			this.commentInput = ''
			uni.showToast({
				title: '评论成功'
			})
		}
	},
	onLoad(options) {
		this.articleData = { id: options.id }
		this.getArticleData()
		this.getCommentList()
	},
	onReachBottom() {
		if(this.commentListInfo.page<this.commentListInfo.pages){
			this.getCommentList()
		}
	}
}
</script>

<style lang="scss" scoped>
// 内容容器
.read_outer {
	padding: 20rpx;
}
// 悬浮按钮容器
.fixed_btns {
	position: fixed;
	bottom: 150rpx;
	right: 20rpx;
	.btn {
		width: 0;
		height: 0;
		border-left: 30rpx solid transparent;
		border-right: 30rpx solid transparent;
		opacity: 0.5;
	}
	.btn_up {
		border-bottom: 20px solid $uni-color-success;
		margin-bottom: 10rpx;
	}
	.btn_down {
		border-top: 20px solid $uni-color-success;
	}
}
// 评论展示容器
.comments_outer {
	padding: 20rpx;
	margin-bottom: 6rpx;
	> .comments_card {
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
	> .comments_info {
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
// 评论输入框

.comment_input_outer {
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
}
.comment_input_focus {
	background-color: #fff;
	box-shadow: 0 0 10rpx $uni-color-success;
}
</style>
