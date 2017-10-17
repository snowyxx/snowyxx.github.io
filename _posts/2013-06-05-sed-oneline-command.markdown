---
layout: post
title:  "sed单行命令笔记"
date:   2013-06-05 15:38:22 +0800
categories: 笔记
tags:   [linux]
---
看博客[Famous Sed One-Liners Explained](http://www.catonmat.net/blog/sed-one-liners-explained-part-one/){:target="_blank"}记的笔记。比较awk来说这些sed命令非常难理解，所以一些命令只是记录，原文有详细的解释。

首先要理解sed的几个概念：      
**input stream**、**outputstream**、**pattern space**和**hold buffer**。                        

sed操作input stream并生成output stream。input stream中的每行放入到pattern space中，并在其中被修改。hold buffer用做临时存储。

在每行后输出空行

        sed G
               # G： Copy/append hold space to pattern space. 就是在pattern space加上新的一行来自holdbuffer的内容。
                在该命令中hold buffer是空的，所以只是在添加了一个空行。
                能够修改hold buffer的命令有 h、H和x

在每5行后输出空行

        sed 'n;n;n;n;G;'

在非空行后输出空行

        sed '/^$/d;G'
                d：     Delete pattern space. Start next cycle.删除当前的pattern space。

删除每行后获的空格

        sed 'n;d'
                n：     Read the next line of input into the pattern space. n用来打印当前pattern space（如果没有使用-n参数），并且清除当前pattern space，从input stream中读入下一行到pattern space中。这个命令假设所有的偶数行都是空行。

在需要行的上面添加空行

        sed '/regex/{x;p;x}'
                x：   Exchange the contents of the hold and pattern spaces.把hold和pattern space中的内容交换。
                {}：    是命令分组

在需要行的上下都插入空行

        sed '/regex/{x;p;x;G;}'

为每行添加行号

        sed = filename | sed 'N;s/\n/\t/'
                =： Print the current line number. 用来打印当前行号。=是把行号直接输出到output scream中的，所使用通道来进行进一步处理。
                N：append the next line of input into the pattern space. 在当前pattern space中附加上新行，新行的内容是input scream中的下一行。和n不同的是不会清除当前pattern space。n的效果是“\n下一行”；N的效果是“当前行\n下一行”。
                s///：就是著名的替换命令了。

为每行添加行号，右对齐

        sed = filename | sed 'N; s/^/这里是5个空格/; s/ *\(.\{6,\}\)\n/\1这里是2个空格/'
                例如echo "echo "-----110@aaaaaaaaaaaaaa" |sed 's/-*\(.\{6,\}\)@/\1/''的结果是“---110aaaaaaaaaaaaaa”。其中\1匹配的内容是@之前的任意6个字符。

为非空行编号

        sed '/./=' filename | sed '/./N; s/\n/ /'

显示总行数

        sed -n '$='

-n, --quiet, --silent： suppress automatic printing of pattern space.不自动打印pattern space。

                当使用了-n参数的时候，必须直接修改output scream才能打印出内容。可以修改output stream的命令有： '=', 'a', 'c', 'i', 'I', 'p', 'P', 'r'和'w'。

把dos的新行标记（CRLF）转换成Unix新行标记（LF）

        sed 's/.$//'
        sed 's/\x0D$//'
        sed 's/^M$//'
            # ^M输入方法：ctrl+v然后ctrl+m


删除行首和行尾的空白

        sed 's/^[ \t]*//;s/[ \t]*$//'

定宽右对齐

        sed -e :a -e 's/^.\{1,78\}$/ &/;ta'
        -e: add the script to the commands to be executed. 如果sed命令中有多个条件，可以用-e来分别处理。和';'的格式一样，但是可读性更好。
         : label Label for b and t commands.冒号用来设置标签。b和t命令使用标签。
                &：表示正则表达式匹配的内容。
                t label： If a s/// has done a successful substitution since the last input line was read and since the last t or T command, then branch to label; if label is omitted, branch to end of script. 如果s///匹配成功，跳转到标签的位置。如果没有指定标签则调整到脚本结尾。
                 T label： If no s/// has done a successful substitution since the last input line was read and since the last t or T command, then branch to label; if label is omitted, branch to end of script. This is a GNU extension. 和t相反。
                这个命令实现了循环：检查一行文字是否小于79个字符，否则就在该行前添加个空格；然后再检查直到达到定宽（79）

定宽居中显示

        sed -e :a -e 's/^.\{1,77\}$/ & /;ta'
        sed -e :a -e 's/^.\{1,77\}$/ &/;ta' -e 's/\( *\)\1/\1/'

查找替换

        sed 's/foo/bar/'
                替换一次
        sed 's/foo/bar/4'
                替换第4个
        sed 's/foo/bar/g'
                替换全部
        sed '/baz/s/foo/bar/g'
                条件替换
        sed '/baz/!s/foo/bar/g'
                非条件替换
        sed 's/scarlet/red/g;s/ruby/red/g;s/puce/red/g'
                替换多个

倒叙显示(tac)

        sed '1!G;h;$!d'
            h H： Copy/append pattern space to hold space.分别用来复制或附加pattern space到hold space中。            
            1和$表示位置，第一行和结尾。
        sed -n '1!G;h;$p'

倒叙显示一行(rev)

        sed '/\n/!G;s/\(.\)\(.*\n\)/&\2\1/;//D;s/.//'

合并相邻行

        sed '$!N;s/\n/ /'

合并以\结尾的行

        sed -e :a -e '/\\$/N; s/\\\n//; ta'
 如果一行以=号开头，把这行和上一行合并到一起
        sed -e :a -e '$!N;s/\n=/ /;ta' -e 'P;D'
                 P Print up to the first embedded newline of the current pattern space.
                 D Delete up to the first embedded newline in the pattern space.         
                    Start next cycle, but skip reading from the input if there is                   
                    still data in the pattern space.

数字分组

        sed -e :a -e 's/\(.*[0-9]\)\([0-9]\{3\}\)/\1,\2/;ta'
        $ echo "12345 1234 123" | sed 's/\B[0-9]\{3\}\>/,&/g'
                12,345 1,234 123
                \B 匹配非单词边界
                \> 匹配单词结尾

打印前10行(head -10)

        sed 10q
                q 就是quit，退出并打印pattern space

打印前第一行(head -1)

        sed q

打印最后10行（tail -10)

        sed -e :a -e '$q;N;11,$D;ba'

打印最后2行

        sed '$!N;$!D'

打印最后1行

        sed '$!d'
        sed -n '$p'

打印配置表达式的行

        sed -n '/regexp/p'
        sed '/regexp/!d'

打印不匹配表达式的行（grep -v)

        sed -n '/regexp/!p'
        sed '/regexp/d'

打印匹配表达式行的前一行

        sed -n '/regexp/{g;1!p;};h'

打印匹配表达式行的后一行

        sed -n '/regexp/{n;p;}'

打印匹配表达式前后行（grep -A1 -B1）

        sed -n -e '/regexp/{=;x;1!p;g;$!N;p;D;}' -e h

打印匹配表达式的段落

        sed -e '/./{H;$!d;}' -e 'x;/AAA/!d;'

只打印超过65个字符的行

        sed -n '/^.\{65\}/p'

只打印小于65个字符的行

        sed -n '/^.\{65\}/!p'

打印从表达式位置到文件结尾

        sed -n '/regexp/,$p'

打印8到12行

        sed -n '8,12p' 或者 sed '8,12!d'

打印两个表达式直接的行

        sed -n '/Iowa/,/Montana/p'

删除连续重复的行

        sed '$!N; /^\(.*\)\n\1$/!P; D'

删除不连续重复的行

        sed -n 'G; s/\n/&&/; /^\([ -~]*\n\).*\n\1/d; s/\n//; h; P'

删除不连续重复的行

        sed '$!N; s/^\(.*\)\n\1$/\1/; t; D'

删除前10行

        sed '1,10d'

删除最后一行

        sed '$d'

删除最后10行

        sed -e :a -e '$d;N;2,10ba' -e 'P;D'
        sed -n -e :a -e '1,10!{P;N;D;};N;ba'

清除html标签

        sed -e :a -e 's/<[^>]*>//g;/