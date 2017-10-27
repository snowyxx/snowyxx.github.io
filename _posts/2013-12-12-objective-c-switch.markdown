---
layout: post
title:  "objective-c string使用switch的一种方法"
date:   2013-12-12 16:30:24 +0800
categories: blog
tags:   [mac]
---
{% highlight objc %}
NSString * statusString     = @"someString"
        NSArray *status=@[@"critical",@"warning",@"clear"];
        int item=[status indexOfObject:statusString];
        switch (item) {
            case 0:
                balabala;
                break;
            case 1:
                balabala;
                break;
            case 2:
                balabala;
                break;
            default:
                break;
        }
{% endhighlight %}