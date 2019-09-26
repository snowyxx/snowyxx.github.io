---
layout: post
title:  "使用Python difflib对比html文件源码差异"
date:   2019-09-26 15:50:05 +0800
categories: blog
tags:   [python]
---

有2个html文件，想比较二者的差异。

下面使用python difflib的例子和说明：

```python
import difflib
def myHtmlDiff():
    result=''
    html1 = ''
    html2 = ''
    with open('1.html', 'r') as fr:
        html1 = fr.read()
    with open('2.html', 'r') as fr:
        html2 = fr.read()
    diffRatio = difflib.SequenceMatcher(None, html1, html2).real_quick_ratio()
    if diffRatio == 1.0:
        continue
    d = difflib.HtmlDiff(wrapcolumn=80)
    result = d.make_file(html1.splitlines(), html2.splitlines(), fromdesc='1.html', todesc='2.html', context=True, numlines=2)
    return result
```

- `.real_quick_ratio()` 是获取差异的比率，值为从1到0，1是完全相同。最快方式。
- `difflib.HtmlDiff()` 是以html格式输出差异。
- `html1.splitlines()` 如果不用splitlines()分行，会出现逐字，而不是逐行，对比html源文件的情况。
- `wrapcolumn=80` 可选。设置后表示在对比结果中的换行长度。
- `context=True, numlines=2` 2个参数配合使用。context默认为False，表示输出对比文件所有行。当设置为True后表示输出差异前后numlines值的行数，默认值是5。

---

参考：                                   

- <https://docs.python.org/2/library/difflib.html>