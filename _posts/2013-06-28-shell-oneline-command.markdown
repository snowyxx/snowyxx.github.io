---
layout: post
title:  "shell单行命令笔记"
date:   2013-06-28 09:15:41 +0800
categories: blog
tags:   [linux]
---
学习[Bash One-Liners Explained](http://www.catonmat.net/blog/bash-one-liners-explained-part-one/){:target="_blank"}的笔记。

## 文件相关

1. 清空一个文件

        $>file    

2. 通过重定向来添加内容

        $echo -n "test" >>|> file
         -n 参数表示不在最后添加空行
         
3. 把文件第一行赋值给一个变量

        $ read -r line < file
         read函数将把读入数据的首尾空白清除。要避免，先把IFS（ Internal Field Separator）设置为空。
        $ IFS= read -r line < file
        另外一种方式是：
        $ line=$(head -1 file)
         $(...)替换操作符用来返回...的执行结果。等同于\`...\`

4. 逐行读取文件

        $ while IFS= read -r line; do
            # do something with $line
        done < file

5. 从文件中随机读取一行并赋值给变量

        $ read -r random_line < <(sort -R file)
          <(...)是进程替换操作符。效果是把...的输出写入到一个以匿名通道命名的文件中。
          sort -R 随机排序
        $ random_line=$(sort -R file | head -1)

6. 读取文件的前3个字段并赋值给变量，其他字段丢弃

        $ while read -r field1 field2 field3 throwaway; do
         # do something with $field1, $field2, and $field3
        done < file
            例如$info="20 packets in 10 seconds"
                $read packets _ _ time _ <<< "$info"
                $echo $packets
                20
                $echo $time
                10
                    这个的<<<表示把字符定向到命令的标准输入中。
           

7. 从路径中提取文件名

        $ filename=${path##*/}
           例如：$path="/path/to/file.ext"
                 $filename=${path##*/}
                 $echo $filename
                 file.ext
            ${var##pattern} 用来返回var删除从其开头匹配pattern的内容
            其他方法：filename=$(basename $path)


8. 从路径中提取文件夹

        $ dirname=${path%/*}
            ${var%pattern} 用来返回var删除从其结尾匹配pattern的内容
        其他方法：dirname=$(dirname $path)


9. 快速复制文件

        $ cp /path/to/file{,_copy}
            /path/to/file{,_copy}等同于 /path/to/file /path/to/file_copy

## 字符串相关


10. 输出a到z

        $ echo {a..z}
        a b c d e f g h i j k l m n o p q r s t u v w x y z

11. 格式化输出

        $ printf "%c" {a..z} $'\n'
             $'\n'是在bash中输出换行的标准形式
             通过printf为变量赋值 $ printf -v alphabet "%c" {a..z}
        $ printf "d " {0..9}
        00 01 02 03 04 05 06 07 08 09
        $ echo {00..09}
        00 01 02 03 04 05 06 07 08 09


12. 生成30个英文单词

        $ echo {w,t,}h{e{n{,ce{,forth}},re{,in,fore,with{,al}}},ither,at}
        when whence whenceforth where wherein wherefore wherewith wherewithal whither what then thence thenceforth there therein therefore therewith therewithal thither that hen hence henceforth here herein herefore herewith herewithal hither hat
        这个就是{,,}作用
         $echo {a,b,c}{1,2,3}
         a1 a2 a3 b1 b2 b3 c1 c2 c3

         $  echo foo{,,,,,,,,,,}  
         foo foo foo foo foo foo foo foo foo foo foo
                foo和10个空字符组合


13. 合并字符

        var=$a$b

14. 分割字符

        $ str="foo-bar-bz"
        $ IFS=- read -r x y z <<< "$str"
        $ echo $
        $ echo $x
        foo
        $ echo $y
        bar
        $ echo $z
        bz
            读取到数组中
               $ IFS=- read -ra parts <<< "foo-bar-bz"
               $ echo ${parts[@]}
               foo bar bz
               $ echo ${parts[0]}
               foo

15. 逐个字符处理字符串

        $ while IFS= read -rn1 c; do
              # do something with $c
          done <<< "$str"
              n2就是一次读取2个字符

16. 字符串替换

        ${var/find/replace}
        替换全部  ${var//find/replace}

17. 检查是否匹配通配符

        $ if [[ $answer = [Yy]* ]]; then
          # do something
        fi
        通配符*匹配全部  ?匹配一个字符 [...]匹配其中的字符


18. 检查是否匹配正则表达式

        $ if [[ $str =~ [0-9]+\.[0-9]+ ]]; then
             # do something
        fi
              参考man 3 regex

19. 获取字符串长度

        $ echo ${#str}

20. 截取部分字符串

        ${var:offset:length}

21. 大小写转换

        $ declare -u var
        $ var="foo bar"
        $ echo $var
        FOO BAR
        $ str="zoo raw"
        $ echo ${str^}
        Zoo raw
        $ echo ${str^^}
        ZOO RAW

        $ declare -l var
        $ var="FOO BAR"
        $ echo $var
        foo bar
        $ str="FOO BAR"
        $ echo ${str,}
        fOO BAR
        $ echo ${str,,}
        foo bar

        echo 'UPPERCASESTRING' |tr '[:upper:]' '[:lower:]'

## 重定向

首先了解3个系统标准操作符：0表示标准输入stdin；1表示标准输出stdout；2表示标准错误stderr。可以创建自己的操作描述符，例如3，4，5

在终端中默认0，1，2指向当前终端

22. 重定向标准输出到文件

        $ command >file   等同于 command 1>file

23. 重定向标准错误到文件

        $ command 2> file

24. 重定向标准输出和标准错误到文件

        $ command &>file
        $ command >file 2>&1 
         常见写法。但是不要写成$ command 2>&1 >file。因为重定向是从左到右执行的，这个命令先把stderr附加到stdout中，一切默认显示在终端中，然后>file有把stdout重定向到file，而stderr还是显示在终端中。

25. 丢弃标准输出

        $ command > /dev/null

26. 把文件重定向到标准输入

        $ command

27. 重定向多行文本到标准输入中

        $ sed 's|http://||' <<EOL
        http://url1.com
        http://url2.com
        http://url3.com
        EOL
        结果：
        url1.com
        url2.com
        url3.com

28. 重定向单行文本到标准输入

        $ command <<< "foo bar baz"

29. 重定向所有命令标准错误到文件中，知道shell关闭

        $ exec 2>file
        $ command1
        $ command2
        $ ...

30. 创建读取文件描述符

        $ exec 3
        $ read -u 3 line
        $ grep "foo" <&3
        $ exec 3>&-  （关闭描述符3）

31. 创建写入文件描述符

        $ exec 4>file
        $ echo "foo" >&4
        $ exec 4>&-

32. 创建读写文件描述符

        $ exec 3<>file
   
33. 将多个命令输出重定向到文件

        $ (command1; command2) >file
           (commands)操作符在当前shell创建子shell，其中的命令在子shell中执行，然后将输出重定向到文件

34. 通过命名通道执行命令

        在第一个终端
           mkfifo fifo
           exec < fifo
        在第二个终端
           exec 3> fifo;
           echo 'echo test' >&3

35. 通过bash访问网站

        $ exec 3<>/dev/tcp/www.google.com/80
        $ echo -e "GET / HTTP/1.1\n\n" >&3
        $ cat <&3

36. 防止重定向到已有文件

        $ set -o noclobber 
        $ echo "ddd" > d.txt
        bash: d.txt: 无法覆盖已存在的文件
        $ echo "ddd" >| d.txt   强制重定向

37. 同时重定向到文件和stdout

        tee命令可以作这个
        $ cat d.txt|tee -a d.txt
        $ cat d.txt
        ddd
        ddd

38. 重定向一个命令的stdout到另一个命令的stdin

        就是通道   command1 | commmand2

39. 重定向一个命令的stdout和stderr到另一个命令的stdin

        $ command1 2>&1 | command2

40. 为文件描述符命名

        bash 4.1之后支持
        $ exec {filew}>output_file

41. 交换stdout和stderr

        $ command 3>&1 1>&2 2>&3

42. 查看通道中所有命令的执行状态

bash命令只返回最后一个命令的状态，如果通道中有多个命令，则把每个命令的状态保存在数组PIPESTATUS中。

        $ echo 'pants are cool' | grep 'moo' | sed 's/o/x/' | awk '{ print $1 }'
        $ echo ${PIPESTATUS[@]}
        0 1 0 0

## 历史

43. 清除历史

        $ history -c
        $ history -w

44. 停止当前会话的历史记录

        $ unset HISTFILE  或者 $ HISTFILE=/dev/null

45. 不记录当前命令

        在命令前添加一个空格（在.bashrc中配置）

46. 在历史记录中添加上时间戳

        $ HISTTIMEFORMAT="%Y-%m-%d %H:%M:%S"

47. 显示最后几个命令

        $ history 50

48. 显示使用最多的10个命令

        $ history |
        sed 's/^ \+//;s/  / /' |
        cut -d' ' -f2- |
        awk '{ count[$0]++ } END { for (i in count) print count[i], i }' |
        sort -rn |
        head -10

        perl:history | perl -lne 's/\d+//;s/^\s+//;s/\s\s/ /; $count{$_}++; END { print "$count{$_} $_" for (sort { $count{$a} <= $count{$b} } keys %count )[0..10] }'

49. 快速执行上一个命令

        $ !!
          这个忘记sudo的时候非常有用。。。。sudo !!

50. 打开编辑器来编辑上一条命令

        $fc

## 导航

首先bash支持emacs、vi和自定义形式的操作。使用set -o emacs和set -o vi命令更改。在~/.inputrc文件中自定义。也可以使用bind命令，如bind '"\C-f": "ls\n"'绑定CTRL+f去执行ls。
以下是一些emacs形式的操作（**有些在GUI的终端中不起作用）。

51. 移动到命令的开头、结尾

        CTRL+a   CTRL+e

52. 向后和向前移动一个单词

        ESC+b/ALT+b   ESC+f/ALT+f

53. 删除/恢复光标前的一个单词

        CTRL+w   CTRL+y

54.  向后和向前移动一个字符

        CTRL+b    CTRL+f

55. 删除光标所有内容

        CTRL+u

56. 搜索

        CTRL+r（向后）     CTRL+s（向前。在我的终端中不行）

57. 快速交换两个临近的字符

        CTRL+t

58.  快速交换两个临近的单词

        Esc+t 或 Alt+t

59. 把单词改成大写/小写

        Esc+u或Alt+u            Esc+l or Alt+l

60. 单词首字母大写

        Esc+c或Alt+c

61. 输入元字符

        Ctrl+v  如输入Windows换行符，先 Ctrl+v，然后输入回车或 Ctrl+m

62. 注释当前命令 

        Esc+#或Alt+# 
        和删除命令不执行不同的是注释一个命令会记录在history中。

63. 在编辑器中打开当前命令

        TRL+x然后CTRL+e

64. 向左或向右删除一个字符

        Ctrl+h    Ctrl+d

65. 取消之前操作

        Ctrl+x 然后 Ctrl+u

66. 插入上一个命令的最后的参数

        Esc+.或Alt+.

67. 取消所有更改

        Esc+r或Alt+r 

68. 清空屏幕

        Ctrl+l
