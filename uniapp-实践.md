# 一 功能实现

## 1 点击跳转到底部

描述：要点击一个悬浮按钮跳转到页面底部

解决：

1. 调用pageScrollTo的api时设置scrollTop为一个很大的数字比如999999999

2. 创建一个querySelector取到元素，然后获取到它的位置，经过一番计算，调用pageScrollTo，把计算的合适的值传给scrollTop

3. 使用pageScrollTo接口中自带的selector属性，可以直接跳转到尾元素的位置。不过要用id：

   ```js
   uni.pageScrollTo({
     selector: '#bottom'
   });
   ```

   只需要在页面底部放一个指定id的元素就可以了，我觉得这是最好的方式

## 2 上拉加载

解决：

使用后端接口的分页功能，每次监听到下拉到底的生命周期时，就page+1来获取列表，然后使用数组的concat来拼接。

```js
getArticleList() {
  const that = this
  uni.request({
    url: 'http://api.pblog.anniesqq.com/mini/browse/articles',
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
```

然后判断page是否大于等于总页数了，如果不小于，那么就不再加载

由于加载过快，设置1秒的定时器来使加载过程更明显

## 3 markdown渲染

可以用npm引入marked模块把内容转换成html格式，然后用rich-text来显示。但是这样效果并不好，可能还需要自己来调整样式。因此还是选择使用uni-app的插件： MDParserHighlight。但是效果也并没有太好，但是有一些别的功能，比如自带图片预览、链接点击复制等，还是不错。

# 二 总结

uniapp基于vue，内置vuex，加上它跨端，使用起来非常方便。社区随时都有官方在回答问题，比较活跃。社区的插件也很多，uni-ui的插件也几乎涵盖了很多的应用场景。

组件中使用下来几个比较有感触的：

1. 好用的就uni-icons：里面包含的图标不算特别多，但是特别全。日常需要的图标里面都有，并且几乎每种都有线性和面性。可以调大小、颜色。唯一的缺点就是设置大小不能带rpx这样的单位。其他的都非常好。

2. uni-easyinput是一个基于uni-icons的可自定义输入框。但是使用下来并不好用。首先要给它设置自定义的样式不容易，而内置样式又无法满足。它可以添加左右图标和清除图标，但是如果添加了右图标就会覆盖掉清除图标。然后看了这个插件的源码，发现实现起来并不难，就是在input外面套一个view，然后这个view设置为垂直和水平都居中的flexbox，里面的内容就会在同一行。然后就可以在里面随意添加东西了。外层view设置圆角。然后聚焦边框高亮就通过class绑定值，监听内部input的focus和blur。这样子代码并不多，并且可以做出更加漂亮又灵活的输入框

   ```html
   <!-- 评论框 -->
   <view :class="{comment_input_outer: true,comment_input_focus: isCommentInputFocus}">
     <uni-icons type="chatbubble" size="20" color="#6699CC"></uni-icons>
     <input type="text" v-model="commentInput" @focus="isCommentInputFocus = true" @blur="isCommentInputFocus = false" @confirm="handleCommentSend"/>
     <uni-icons type="paperplane" size="20"  @click="handleCommentSend"></uni-icons>
   </view>
   ```

   

uni-app对scss的支持比较好，默认根目录就有一个样式变量的文件。不需要再引入别的插件，直接设置lang=“scss”就可以使用了，在样式方面可以直接嵌套选择器，可以使用全局的变量，像长度、宽度、透明度、字号、颜色等。这样可以更方便地做出统一的主题风格，非常人性化。