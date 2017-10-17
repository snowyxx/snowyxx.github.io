---
layout: post
title:  "awk单行命令笔记"
date:   2013-05-28 11:11:55 +0800
categories: 笔记
tags:   [linux]
---
学习[Peteris Krumins][1]的文章[《Famous Awk One-Liners Explained》][2]的笔记：             

每行后插入空行

    awk '1; { print "" }'
    awk '1; { print "\n" }'

在每行后输出2空行

    awk 'BEGIN { ORS="\n\n" }; 1'
        #ORS是记录分隔符
    awk 'NF { print $0 "\n" }'
        #NF是字段数，如果是0，则空行，不处理

在每行中添加上行号

    awk '{ print FNR "\t" $0 }'
    awk '{ print NR "\t" $0 }'
        #FNR是每个文件的记录数，NR是所有文件中的记录数
    awk '{ printf("%5d : %s\n", NR, $0) }'
    awk 'NF { $0=++a " :" $0 }; { print }'
        #只标识非空的行

显示行数（wc -l)

    awk 'END { print NR }'

将每行中的字段汇总

    awk '{ s = 0; for (i = 1; i <= NF; i++) s = s+$i; print s }'

将所有行中的字段汇总

    awk '{ for (i = 1; i <= NF; i++) s = s+$i }; END { print s+0 }'
        #s+0是为处理当s为空的时候,显示结果为0，相当于undef+0=0

取字段的绝对值

    awk '{ for (i = 1; i <= NF; i++) if ($i < 0) $i = -$i; print }'

计算字段总数

    awk '{ total = total + NF }; END { print total+0 }'

计算特定字段的总数

    awk '/Beth/ { n++ }; END { print n+0 }'

显示某列中最大值

    awk '$1 > max { max=$1; maxline=$0 }; END { print max, maxline }'
        #如所有的值都为负数，应该使用：awk 'NR == 1 { max = $1; maxline = $0; next; } $1 > max { max=$1; maxline=$0 }; END { print max, maxline }'

打印最后一列

    awk '{ print $NF }'

打印最后一行的最后一列

    awk '{ field = $NF }; END { print field }'
    awk 'END { print $NF }'
        #这个更好

打印除了最后一列的其他列

    awk 'NF--'
        #但是：awk '--NF'是不行的

打印字段多于4的记录

    awk 'NF > 4'

打印字段值大于4个记录

    awk '$NF > 4'

把dos的新行标记（CRLF）转换成Unix新行标记（LF）

    awk '{ sub(/\r$/,""); print }'
        #在vi中输入ctrl+v然后回车，就是^M

删除行头和行尾的空格

    awk '{ gsub(/^[ \t]+|[ \t]+$/, ""); print }'

字段居中定长

    awk '{ l=length(); s=int((79-l)/2); printf "%"(s+l)"s\n", $0 }

替换指定位置的字段

    gawk '{ $0 = gensub(/foo/,"bar",4); print }'

显示不匹配的正则表达式

    awk '!/baz/ { gsub(/foo/, "bar") }; { print }'

反序显示（tac）

    awk '{ a[i++] = $0 } END { for (j=i-1; j>=0;) print a[j--] }'

合并以反斜杠（\）结尾行

    awk '/\\$/ { sub(/\\$/,""); getline t; print $0 t; next }; 1'
        #getline t表示获取下一行并保存到变量t中

替换字段位置

    awk '{ print $2, $1 }' file
    awk '{ temp = $1; $1 = $2; $2 = temp; print }'

反序打印每行的字段

    awk '{ for (i=NF; i>0; i--) printf("%s ", $i); printf ("\n") }'

删除连续重复的行

    awk 'a != $0; { a = $0 }'

删除不连续重复的行

    awk '!a[$0]++'
    awk '!($0 in a) { a[$0]; print }'

每5行连接到一起

    awk 'ORS=NR%5?",":"\n"'
    
打印前10行（head -10）

    awk 'NR < 11'
    awk '1; NR == 10 { exit }'
        #更好，因为处理到第10行以后的操作都没用了。

打印最后2行(tail -2)

    awk '{ y=x "\n" $0; x=$0 }; END { print y }'

打印最后一行

    awk 'END { print }'

打印匹配的前一行

    awk '/regex/ { print x }; { x=$0 }

如果第一行匹配表达式用下面的：

    awk '/regex/ { print (x=="" ? "match on line 1" : x) }; { x=$0 }'

打印匹配的下一行

    awk '/regex/ { getline; print }'

打印从匹配出到结尾

    awk '/regex/,0'
        #0表示false，就是一直都找不到结尾

打印从第几行到第几行

    awk 'NR==8,NR==12'

打印某一行

    awk 'NR==52 { print; exit }'
    


[1]:http://www.catonmat.net/about/
[2]:http://www.catonmat.net/blog/awk-one-liners-explained-part-one/