<template>
	<view>
		<!-- 固定搜索栏 -->
		<view class="fixed_header">
			<view
				:class="{ input_outer: true, input_outer_focus: isSearchFocus }"
			>
				<!-- 输入框 -->
				<input
					type="text"
					v-model="searchInput"
					:focus="isSearchFocus"
					:placeholder="isSearchFocus ? '' : '搜索文章标题或内容'"
					@focus="isSearchFocus = true"
					@blur="isSearchFocus = false"
					@confirm="hancleSearchClick"
				/>
				<!-- 清空按钮 -->
				<uni-icons
					v-if="searchInput !== ''"
					type="clear"
					color="#777"
					@click="handleClearSearch"
				></uni-icons>
				<!-- 清空按钮占位 -->
				<uni-icons v-else type="clear" color="#f8f8f8"></uni-icons>
				<!-- 搜索按钮 -->
				<uni-icons
					type="search"
					color="#777"
					@click="hancleSearchClick"
				></uni-icons>
			</view>
		</view>
		<!-- 搜索栏占位 -->
		<view class="fixed_header_holder"></view>
		<!-- 文章卡片 -->
		<article-card
			v-for="(item, index) in articleList"
			:key="index"
			:article-data="item"
			@click="handleArticleClick(item.id)"
		></article-card>
		<!-- 底部填充 -->
		<view class="bottom_tip">
			<uni-icons
				v-if="articleListInfo.page < articleListInfo.pages"
				type="spinner-cycle"
			></uni-icons>
			<text v-else>--到底了--</text>
		</view>
	</view>
</template>

<script>
import articleCard from '../../components/article-card/article-card.vue'
export default {
	data() {
		return {
			// 搜索内容
			searchInput: '',
			// 搜索框是否聚焦
			isSearchFocus: false,
			// 文章列表
			articleList: [],
			// 文章列表信息
			articleListInfo: {
				page: 0,
				size: 6,
				total: 0,
				pages: 1
			}
		}
	},
	components: {
		articleCard
	},
	onLoad() {
		this.getArticleList()
	},
	methods: {
		// 点击搜索图标按钮
		hancleSearchClick() {
			this.searchArticles()
		},
		// 清除搜索框
		handleClearSearch() {
			this.searchInput = ''
			this.searchArticles()
		},
		// 获取文章列表。一次8个
		getArticleList() {
			const that = this
			uni.request({
				url: 'http://api.pblog.anniesqq.com/mini/browse/articles',
				method: 'GET',
				data: {
					page: that.articleListInfo.page + 1,
					size: this.articleListInfo.size,
					content: this.searchInput
				},
				success(res) {
					// 设置文章列表
					that.articleList = that.articleList.concat(res.data.data)
					// 设置列表信息
					that.articleListInfo = {
						page: res.data.page,
						size: res.data.size,
						total: res.data.total,
						pages: res.data.pages
					}
				}
			})
		},
		// 搜索文章时，加载内容归零
		searchArticles() {
			this.articleListInfo = {
				page: 0,
				size: 6,
				total: 0,
				pages: 1
			}
			this.articleList = []
			this.getArticleList()
		},
		// 文章点击
		handleArticleClick(id) {
			uni.navigateTo({
				url: '/pages/read/read?id='+id
			})
		}
	},
	onReachBottom() {
		// 如果页数小于总页数就继续加载
		if (this.articleListInfo.page < this.articleListInfo.pages) {
			this.getArticleList()
		}
	}
}
</script>

<style lang="scss" scoped>
// 固定搜索栏
.fixed_header {
	position: fixed;
	height: 100rpx;
	width: 100%;
	display: flex;
	align-items: center;
	justify-content: center;
	background-color: $uni-bg-color-grey;
	box-shadow: 0 10rpx 20rpx $uni-bg-color-mask;
}
.fixed_header_holder {
	height: 100rpx;
	width: 100%;
}
// 输入框外框
.input_outer {
	border: $uni-border-color 1rpx solid;
	border-radius: 60rpx;
	padding-left: 20rpx;
	width: 650rpx;
	display: flex;
	input {
		width: 85%;
	}
	// 小图标按钮
	uni-icons {
		margin: 0 15rpx;
		justify-self: right;
	}
}
.input_outer_focus {
	// border: $uni-color-primary 1rpx solid;
	border: none;
	box-shadow: 0rpx 0rpx 10rpx $uni-color-success;
}
// 底部提示
.bottom_tip {
	height: 60rpx;
	display: flex;
	justify-content: center;
	align-items: center;
	margin: 25rpx 0;
}
</style>
