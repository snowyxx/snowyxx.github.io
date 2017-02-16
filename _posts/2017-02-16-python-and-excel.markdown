---
layout: post
title:  "结合使用Python和Excel来分析日志的部分数据"
date:   2017-02-16 16:20:05 +0800
categories: blog
tags:   [python]
---

* 目录
{:toc}

我从一个应用程序的日志中提出每天的某个数据。然后通过Execl对数据进行处理，得到数据分布和趋势图表。下面记录使用Python从日志中挑出数据、使用Python把数据存储为Excle文件、使用Excle处理数据生成图表的过程。


### 使用Python从日志中挑选自己要的数据

使用正则表达式从日志中提出有日期和数据关键字的行。然后按天求和。

```python
import os
import re

files = []
sizes = {} #{'dddd-dd-dd':size,}

pickFileSizeLinesPattern = r'(?i)(\d\d\d\d-\d\d-\d\d)\s*02:.*?Downloading.*?Bytes:\s*(\d*)\s'
for d, ds, fs in os.walk('.'):
    logfiles = [x for x in fs if x.startswith('output.log')]
    files += logfiles

for f in files:
    with open(f, 'r') as fhander:
        lines = fhander.readlines()
        for l in lines:
            result = re.search(pickFileSizeLinesPattern, l)
            if result:
                date = result.group(1)
                size = result.group(2)
                sizes[date] = sizes.get(date, 0) + int(size)/1024/1024
```

这段代码把日志中满足条件的行选出来，例如：

```
2017-01-11 02:07:15,977 INFO Downloading: xxxxx.exe Bytes: 65658928
2017-01-11 02:07:16,961 INFO Downloading: xxxxx_64.exe Bytes: 68804008

```

然后求和，把每天的值放到dict中。如：`{'2017-01-11':8888}`


### 使用Python把数据写入到Excel文件中

选择使用`xlwt`。安装：

`pip install xlwt`

使用方法：

 ```python
import xlwt

excel = xlwt.Workbook()
dsheet = excel.add_sheet('daily')

dsheet.write(0,0,'Date')
dsheet.write(0,1,'Size')

for index, val in enumerate(sorted(sizes)):
    date = val
    size = sizes[date]
    dsheet.write(index+1,0,date)
    dsheet.write(index+1,1,size)
    
excel.save('downloaded.xls')

 ```

最终生成的xls有的sheet，而且有2列的数据。如下图：

![excle data](/images/excle_1.png)

### Excel中数据处理

#### 计算每天的累加值

公式： `SUM(B$2:B2)` 

> 把公式所在单元格向下拉，生成逐天累加结果。B$2表示每次都从B2开始。例如拉到第三个单元格，公式变成 `SUM(B$2:B4)`。

效果如下图：

![excle data sum](/images/excle_2.png)


#### 计算每月的累加值

公式： `SUMPRODUCT((YEAR(A2:A2000)=D2)*(MONTH(A2:A2000)=E2)*B2:B2000)` 

> - SUMPRODUCT函数是处理多个数组，先求数组间的乘积，然后求和。例如`SUMPRODUCT({1,0,1},{2,2,2})`，先算乘积，得到`SUMPRODUCT({2,0,2})`，最终结果是`4`。
> - YEAR和MONTH函数获取日期格式数据的年份和月份。`YEAR(A2:A2000)=D2`判断A2中的年份是否和D2相等。得到一个从2到2000的数组`{TRUE,TRUE,FALSE,...}`
> - SUMPRODUCT能把`{TRUE,TRUE,FALSE,...}`自动转成`{1,1,0,...}`。
> - TIPS: 强制把TRUE,FALSE转成1,0的方法：`--(YEAR(A2:A2000)=D2)`

效果如下图：

![excle data sumproduct](/images/excel_3.png)


#### 插入带趋势线的图表

选择月数据插入折线图表。在生成的图表上右键，选择**添加趋势线**。在趋势线上右键，选择**设置趋势线格式**。在**趋势预测**那里可输入向前或向后多少周期。

效果如下图：

![excle chart](/images/excel_4.png)


---

参考：                                   

- [Excel 中的公式概述](https://support.office.com/zh-cn/article/Excel-%E4%B8%AD%E7%9A%84%E5%85%AC%E5%BC%8F%E6%A6%82%E8%BF%B0-ecfdc708-9162-49e8-b993-c311f47ca173)                   
- [Excel 函数（按类别列出）](https://support.office.com/zh-cn/article/Excel-%E5%87%BD%E6%95%B0%EF%BC%88%E6%8C%89%E7%B1%BB%E5%88%AB%E5%88%97%E5%87%BA%EF%BC%89-5f91f4e9-7b42-46d2-9bd1-63f26a86c0eb?ui=zh-CN&rs=zh-CN&ad=CN)                   
- [Excel SUMPRODUCT Function）](https://exceljet.net/excel-functions/excel-sumproduct-function)                   

