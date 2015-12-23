---
layout: post
title:  "jekyll分页+jekyll中变量"
date:   2015-12-23 14:49:05 +0800
categories: blog
tags:   [jekyll]
---
按照[说明](http://jekyllrb.com/docs/pagination/){:target="_blank"}在_config.yml添加：

    paginate: 20
    paginate_path: "/blog/page:num/"
    
但是在`jekyll serve`的时候出现警告：

>Deprecation: You appear to have pagination turned on, but you haven't included the \`jekyll-paginate\` gem. Ensure you have \`gems: [jekyll-paginate]\` in your configuration file.

原因[这里说明](http://jekyllrb.com/docs/pagination/){:target="_blank"}：           

>For Jekyll 3, include the jekyll-paginate plugin in your Gemfile and in your _config.yml under gems. For Jekyll 2, this is standard.

解决方法：

+ sudo gem install jekyll-paginate
+ 在_config.yml中添加`gems: [jekyll-paginate]`

**最后**      
设置好分页之后，在index.html中按分页列出博客；添加底部页面导航。（[source code](https://github.com/snowyxx/snowyxx.github.io/blob/master/index.html)）

---

**补充**jekyll中变量

## Jekyll变量

### 头部定义

主要用于指定模板(layout)和定义一些变量，比如：标题(title)、描述(description)、标签(tags)、分类(category/categories)、是否发布(published)，以及其他自定义的变量。

{% highlight yaml %}
layout:     post   # 指定使用的模板文件，“_layout” 目录下的模板文件名决定变量名
title:      title  # 文章的标题
date:       date   # 覆盖文章名中的日期
category:   blog   # 文章的类别
description: description
published:  true   # default true 设置 “false” 后，文章不会显示
permalink:  /:categories/:year/:month/:day/:title.html  # 覆盖全局变量设定的文章发布格式
{% endhighlight %}

### 使用变量

关于 Jekyll 的变量，可以参考[官方说明](http://jekyllrb.com/docs/variables/)  
上面文章页面中定义的头部变量，需要使用下面的语法获得：

```
page.title
```

所有的变量都是一个树节点，比如：page 就是当前页面的根节点。

其中全局根结点有：

- **site**： `_config.yml` 中配置的信息
- **page**： 页面的配置信息，包括 YAML 中定义的变量
- **content**： 用在模板文件中，该变量包含页面的子视图，用于引入子节点的内容；不能在 post 和 page 文件中使用
- **paginator**： 分页信息，需要事先设定 site 中的 `paginate` 值，参考 [Pagination](http://jekyllrb.com/docs/pagination/)

注意：`post` 变量仅作用于 `for` 循环内部，如 `{ % for post in site.posts % }` 。假如需要访问当前正在渲染的文章页面的变量，就要用 `page` 变量代替 post 对象。比如，post 的 title 变量，要通过 `page.title` 来访问。


#### site 下的变量

| 变量                | 描述 |
| ----              | ---- |
|site.time          |当前的时间(运行 Jekyll 时的时间)|
|site.pages         |所有页面列表|
|site.posts         |按时间逆序排列的所有文章列表|
|site.related_posts |如果当前被处理的页面是一个 post 文件，那这个变量是一个包含了最多10篇相关的文章列表|
|site.static\_files  |所有静态文件的列表(如：没有被 Jekyll 处理的文件)，每个文件有3个属性：`path`、`modified_time` 和 `extname`|
|site.html_pages    |所有 HTML 页面列表|
|site.collections   |自定义的对象集合列表，参考 [Collections](http://jekyllrb.com/docs/collections/)|
|site.data          |_data 目录下 YAML 文件的数据列表|
|site.documents     |所有 Collections 里面的文档列表|
|site.categories.CATEGORY|所有在 CATEGORY 类别下的 post 列表|
|site.tags.TAG      |所有在 TAG 标签下的 post 列表|
|site.[CONFIGURATION_DATA]|其他自定义的变量|


#### page 下的变量 

| 变量            | 描述 |
| ----          | ---- |
|page.content   |页面的内容|
|page.title     |页面的标题|
|page.excerpt   |未渲染的摘要|
|page.url       |不带域名的页面链接，如：`/2008/12/14/my-post.html`|
|page.date      |指定每一篇 post 的时间，可在 post 的 front matter 里覆盖这个值，格式是：`date: YYYY-MM-DD HH:MM:SS`|
|page.id        |每一篇 post 的唯一标示符(在RSS中非常有用)，如：`/2008/12/14/my-post`|
|page.categories|post 隶属的一个分类列表，可在 YAML 头部指定|
|page.tags      |post 隶属的一个标签列表，可在 YAML 头部指定|
|page.path      |页面的源码地址|
|page.next      |按时间顺序排列的下一篇文章|
|page.previous  |按时间顺序排列的上一篇文章|


#### paginator 下的变量 

分页只在 index 页面中有效，index 页面可以在子目录里，比如：主页在 `/blog/index.html`，那么通过配置 `paginate_path: "blog/page:num/"`，主页里面放不下的其他内容就可以设定在第 2 页 `blog/page2/index.html` 以及后面的页面中。

| 变量                | 描述 |
| ----              | ---- |
|paginator.per\_page        |每一页的 post 数量|
|paginator.posts        |当前页面上可用的 post 列表|
|paginator.total\_posts |所有 post 的数量|
|paginator.total_pages  |分页总数|
|paginator.page         |当前页的页码，或者 `nil`|
|paginator.previous\_page|上一页的页码，或者 `nil`|
|paginator.previous\_page\_path|上一页的路径，或者 `nil`|
|paginator.next\_page   |下一页的页码，或者 `nil`|
|paginator.next\_page\_path|下一页的路径，或者 `nil`|




参考:     
[http://alfred-sun.github.io/blog/2015/01/10/jekyll-liquid-syntax-documentation/](http://alfred-sun.github.io/blog/2015/01/10/jekyll-liquid-syntax-documentation/){:target="_blank"}