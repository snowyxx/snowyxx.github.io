---
layout: page
title: 关于
permalink: /about/
---

只是我的博客。

{% if site.duoshuo %}
<h3>最新评论</h3>
<!-- 多说最新评论 start -->
<div class="ds-recent-comments" data-num-items="5" data-show-avatars="1" data-show-time="1" data-show-title="1" data-show-admin="1" data-excerpt-length="70"></div>
<!-- 多说最新评论 end -->
<!-- 多说公共JS代码 start (一个网页只需插入一次) -->
<script type="text/javascript">
var duoshuoQuery = {short_name:"{{site.duoshuo}}"};
    (function() {
        var ds = document.createElement('script');
        ds.type = 'text/javascript';ds.async = true;
        ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
        ds.charset = 'UTF-8';
        (document.getElementsByTagName('head')[0] 
         || document.getElementsByTagName('body')[0]).appendChild(ds);
    })();
</script>
<!-- 多说公共JS代码 end -->
<!-- 多说热评文章 start -->
<h3>热评文章</h3>
<div class="ds-top-threads" data-range="monthly" data-num-items="5"></div>
<!-- 多说热评文章 end -->
<!-- 多说公共JS代码 start (一个网页只需插入一次) -->
<script type="text/javascript">
var duoshuoQuery = {short_name:"{{site.duoshuo}}"};
    (function() {
        var ds = document.createElement('script');
        ds.type = 'text/javascript';ds.async = true;
        ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
        ds.charset = 'UTF-8';
        (document.getElementsByTagName('head')[0] 
         || document.getElementsByTagName('body')[0]).appendChild(ds);
    })();
</script>
<!-- 多说公共JS代码 end -->         

{% endif %} 
<br><br>
Powered by:
{% include icon-github.html username="jekyll" %} /
[jekyll](https://github.com/jekyll/jekyll)

<p>{{ site.description }}</p>
