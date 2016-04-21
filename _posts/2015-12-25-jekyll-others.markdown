---
layout: post
title:  "jekyll访问统计＋标签页＋评论＋搜索"
date:   2015-12-25 15:11:05 +0800
categories: blog
tags:   [jekyll]
---
### 访问统计

使用CNZZ。去他们网站注册个用户，然后按照向导得到一段javascript，放到文章模版页面的结尾即可。

### 标签页

做一个标签页用来按标签分组文章。主要是使用`site.tags`

{% highlight html %}
{% raw %}
---
layout: page
title: 标签
permalink: /tags/
---
<ul class="tags">
    {% for tag in site.tags %}
    <li>
        <a href="#{{ tag[0] }}">{{ tag[0] }}</a> <sup>{{ tag[1].size }}</sup>
    </li>
    {% endfor %}
</ul>

<ul class="listing">
    {% for tag in site.tags %}
    <li class="listing-seperator" id="{{ tag[0] }}">{{ tag[0] }}</li>
    {% for post in tag[1] %}
    <li class="listing-item">
        <time datetime="{{ post.date | date:"%Y-%m-%d" }}">{{ post.date | date:"%Y-%m-%d" }}</time>
        <a href="{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a>
    </li>
    {% endfor %}
{% endfor %}
</ul>
{% endraw %}
{% endhighlight %}

### 评论
一开始是很抗拒云评论服务(Disqus、多说等)的。想法是评论页做成静态的。

关于静态评论[这篇文章](http://www.hezmatt.org/~mpalmer/blog/2011/07/19/static-comments-in-jekyll.html){:target="_blank"}很好。而且github上有很多jekyll静态评论项目。

后来试用了一下多说。感觉速度还可以，而且不复杂。那么现在就用多说吧。

_题外话：关于多说一个蛋疼的地方,各处的说明都提到了要一个叫多说**shortname**的东西。可是在多说网站怎么都找不到配置的地方。后来搜索了才知道链接是[http://duoshuo.com/create-site/](http://duoshuo.com/create-site/){:target="_blank"}。 另外多说后台地址是：http://yourshortname.duoshuo.com/admin/_

按照向导把javascript，放到文章模版页面即可。然后调节一下css，按你喜欢的样式显示。

当然还是不很喜欢这个评论。以后再做个静态的评论吧。

参考：    
[http://blog.puhao.me/吐槽/使用多说/](http://blog.puhao.me/%E5%90%90%E6%A7%BD/%E4%BD%BF%E7%94%A8%E5%A4%9A%E8%AF%B4/){:target="_blank"}            
[http://liberize.me/tech/jekyll-use-duoshuo-comment-system.html](http://liberize.me/tech/jekyll-use-duoshuo-comment-system.html){:target="_blank"}

### 搜索

搜索的思路有2种：

1. 用Google或百度做站内搜索。 总觉得村花嫁东村秀才不成，也不愿屈从了村中恶霸。所以选择第二种。
2. 生成本博客信息的xml/json文件，然后通过ajax获取该文件，使用正则表达式搜索。默认RSS的feed.xml文件限制10篇文章，给别人订阅用很好，所以不动feed.xml，另外生成个[json文件](https://github.com/snowyxx/snowyxx.github.io/blob/master/search.json)。因为要在全部博客内容中搜索，加载慢，所以单独做了个[搜索页面](https://github.com/snowyxx/snowyxx.github.io/blob/master/search.html)。

