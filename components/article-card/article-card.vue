<template>
	<view class="card_outer" @click="handleArticleClick">
		<view class="aticle_title">{{ articleData.title }}</view>
		<view class="article_info">
			<text>{{ articleData.reader_num }}</text>
			<uni-icons type="eye" size="24rpx"></uni-icons>
			<text>{{ articleData.like_num }}</text>
			<uni-icons type="hand-thumbsup" size="24rpx"></uni-icons>
			<text>{{ articleData.collect_num }}</text>
			<uni-icons type="star" size="24rpx"></uni-icons>
			<text>{{ articleData.comment_num }}</text>
			<uni-icons type="chatbubble" size="24rpx"></uni-icons>
		</view>
		<view class="article_info">{{ articleData.release_time }}</view>
	</view>
</template>

<script>
export default {
	name: 'article-card',
	data() {
		return {}
	},
	props: ['articleData'],
	created() {
		// console.log(this.articleData)
	},
	methods:{
		handleArticleClick(){
			this.$emit('click')
		}
	}
}
</script>

<style lang="scss" scoped>
// 文章容器
.card_outer {
	box-shadow: 0 10rpx 20rpx $uni-border-color;
	width: 650rpx;
	height: 130rpx;
	margin: 25rpx auto 0 auto;
	padding: 10rpx 25rpx;
}
// 文章标题
.aticle_title {
	font-weight: bold;
	font-size: 32rpx;
	overflow: hidden;
	text-overflow:ellipsis;
	white-space: nowrap;
}
// 文章信息
.article_info {
	font-size: 24rpx;
	margin-top: 10rpx;
	uni-icons{
		margin-right: 10rpx;
	}
}
</style>
