---
layout: post
title:  "perl单行命令笔记"
date:   2013-06-26 15:33:43 +0800
categories: blog
tags:   [linux]
---
学习[Famous Perl One-Liners Explained](http://www.catonmat.net/blog/perl-one-liners-explained-part-one/){:target="_blank"}的笔记

在每行后插入空行

    perl -pe '$\="\n"'
      p 表示打开循环，格式为：            
                 while (<>) {
                        # your program goes here
                } continue {
                    print or die "-p failed: $!\n";
                }
       e 表示在命令行中执行
       $\ 输出分隔符
       $/ 输入分隔符
    perl -pe 'BEGIN { $\="\n" }'
    perl -pe '$_ .= "\n"'
    perl -pe 's/$/\n/'
    不在空行后插入空行
    perl -pe '$_ .= "\n" unless /^$/'
    perl -pe '$_ .= "\n" if /\S/'

在每行前输入空行

    perl -pe 's//\n/'
        空的正则表达式表示$_的开头

移除所有空行

     perl -ne 'print unless /^$/'
     perl -lne 'print if length'
     perl -ne 'print if /\S/'
           n 表示打开循环，格式为：
                  while (<>) {
                           # your program goes here
                  }
           l 表示“-l[octal]         enable line ending processing, specifies line terminator”

移除连续空行

      perl -00 -pe ''
      perl -00pe0
           0 表示记录分隔符，00表示以段落分（两个空行）。（参考perldoc perlrun)

显示行号

      perl -pe '$_ = "$. $_"'
          $. 记录号
      非空行
      perl -pe '$_ = ++$a." $_" if /./'
      perl -ne 'print $_ = ++$a." $_" if /./'

格式化输出行号（cat -n）

      perl -ne 'printf "%-5d %s", $., $_'

输出行数(wc -l)

      perl -lne 'END { print $. }'
      perl -le 'print $n=()=<>'
      perl -ne '}{print $.'
         这个有意思，等同于：
         while (<>) {
         }{                    # eskimo operator here
                print $.;
         }

打印非空行数

      perl -le 'print scalar(grep{/./}<>)' 
      perl -le 'print ~~grep{/./}<>'
      perl -le 'print~~grep/./,<>'

打印匹配表达式的行数

       perl -lne '$a++ if /regex/; END {print $a+0}'

计算一行中所有字段和

       perl -MList::Util=sum -alne 'print sum @F'

计算所有字段之和

       perl -MList::Util=sum -alne 'push @S,@F; END { print sum @S }'
       perl -MList::Util=sum -alne '$s += sum @F; END { print $s }'
          第二个较好。。。不生成第一个中的那个大数列@S

计算每行的字段(word)数

       perl -alne 'print scalar @F'

打印匹配表达式的数量

       perl -alne '$t += grep /regex/, @F; END { print $t }'

打印匹配表达式的行数

      perl -lne '/regex/ && $t++; END { print $t }'

打印Pi

      perl -Mbignum=bpi -le 'print bpi(21)'
      perl -Mbignum=PI -le 'print PI'

打印时间

      perl -le 'print time'
      perl -le 'print scalar gmtime'
      perl -le 'print scalar localtime'
      perl -le 'print join ":", (localtime)[2,1,0]'
          gmtime和localtime返回：
                    ($second,             [0]
                     $minute,              [1]
                     $hour,                [2]
                     $month_day,           [3]
                     $month,               [4]
                     $year,                [5]
                     $week_day,            [6]
                     $year_day,            [7]
                     $is_daylight_saving   [8]
                    )

生成10个5到15的随机数

       perl -le '$n=10; $min=5; $max=15; $, = " "; print map { int(rand($max-$min))+$min } 1..$n'

打印a到zz

       perl -le ''$, = ","; print ("a".."zz")'
               $, 表示print分隔符

生成8位随机密码

       perl -le 'print map { ("a".."z", 0..9)[rand 36] } 1..8'

生成1到100的奇/偶数的数列

      perl -le '@odd = grep {$_ % 2 == 1} 1..100; print "@odd"'
      perl -le '@even = grep {$_ % 2 == 0} 1..100; print "@even"'

ROT13

    perl -lpe 'y/A-Za-z/N-ZA-Mn-za-m/' file

Base64加密解密

      perl -MMIME::Base64 -0777 -ne 'print encode_base64($_)' file
                  使用-0777不存在的分隔符和-n一起把文件内容放到$_中
      perl -MMIME::Base64 -ne 'print decode_base64($_)' file

URL编码(escape)和反编码(uuuuunnnnnescape)

      perl -MURI::Escape -le 'print uri_escape($string)'
      perl -MURI::Escape -le 'print uri_uuuuunnnnnescape($string)'   # j8 sina blog!!!!
             Linux中perl模块安装方法：sudo perl -MCPAN -e shell
                                      install URI::Escape

HTML编码(encode)和反编码(decode)

      perl -MHTML::Entities -le 'print encode_entities($string)'
      perl -MHTML::Entities -le 'print decode_entities($string)'
              对中文也编码了。。

大小写转换

      perl -nle 'print uc'
      perl -nle 'print lc'
      perl -ple 'y/A-Za-z/a-zA-Z/'

删除行首空白

      perl -ple 's/^[ \t]+//'
      perl -ple 's/^\s+//'

删除行首行尾空白

      perl -ple 's/^[ \t]+|[ \t]+$//g' 

系统间新行转换

      dos-->unix  perl -pe 's|\r\n|\n|'
      unix-->dos  perl -pe 's|\n|\r\n|'
      unix-->mac  perl -pe 's|\n|\r|'

匹配替换

      perl -pe '/baz/ && s/foo/bar/'

打印首行（head -1）

      perl -ne 'print; exit'

打印前10行(head -10)

      perl -ne 'print if $. <= 10'
      perl -ne '$. <= 10 && print'

打印最后一行（tail -1）

      perl -ne '$last = $_; END { print $last }'
      perl -ne 'print if eof'

打印最后10行 （tail -10）

      perl -ne 'push @a, $_; @a = @a[@a-10..$#a]; END { print @a }'
          这个就是首先把每一行都添加到@a中，然后只保留@a的最后10个元素

打印匹配和不匹配的行

      perl -ne '/regex/ && print'
      perl -ne '!/regex/ && print'

打印匹配的前一行

      perl -ne '/regex/ && $last && print $last; $last = $_'  
      
打印匹配的后一行

      perl -ne 'if ($p) { print; $p = 0 } $p++ if /regex/'

打印长度超过80的行

      perl -ne 'print if length >= 80'

打印第13行

      perl -ne '$. == 13 && print && exit'

打印非27行

     perl -ne '$. != 27 && print'

打印选择的3行

     perl -ne 'print if $. == 13 || $. == 19 || $. == 67'
     perl 5.10 perl -ne 'print if int($.) ~~ (13, 19, 67)'

打印两个匹配之间的行

     perl -ne 'print if /regex1/../regex2/'

打印17到30行

    perl -ne 'print if $. >= 17 && $. <= 30'
    perl 5.10 perl -ne 'print if int($.) ~~ (17..30)'
    perl -ne 'print if grep { $_ == $. } 17..30'

打印最长的行

     perl -ne '$l = $_ if length($_) > length($l); END { print $l }'

打印最短行

     perl -ne '$s = $_ if $. == 1; $s = $_ if length($_) < length($s); END { print $s }'

打印奇数行

     perl -ne 'print if $. % 2'

打印偶数行

     perl -ne 'print if $. % 2 == 0'

打印重复的行

     perl -ne 'print if ++$a{$_} == 2'

不打印重复的行

     perl -ne 'print unless $a{$_}++'
 
一些正则表达式

- 类型IP地址 `/^\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}$/`
           `/^(\d{1,3}\.){3}\d{1,3}$/`

- 0到255之间的数   `/^([0-9]|[0-9][0-9]|1[0-9][0-9]|2[0-4][0-9]|25[0-5])$/`

 - 匹配IP地址  `my $ip_part = qr|([0-9]|[0-9][0-9]|1[0-9][0-9]|2[0-4][0-9]|25[0-5])|; if ($ip =~ /^($ip_part\.){3}$ip_part$/) { say "valid ip"; }`

- 邮件地址 /.+@.+\..+/ 或者使用Email::Valid模块：
        
        use Email::Valid; 
        print (Email::Valid->address('john@example.com') ? 'valid email' : 'invalid email');

- 匹配十进制数 `/^[+-]?\d+\.?\d*$/`
- 匹配十六进制数  `/^0x[0-9a-f]+$/i`
- 匹配八进制数   `/^0[0-7]+$/`
- 匹配儿进制数  `/^[01]+$/`
以上匹配最好使用Regexp::Common模块$RE{num}{int}，$RE{num}{hex}，$RE{num}{oct}和$RE{num}{bin}

- 匹配一行出现2次的词  `/(word).*\1/`

- 把一行中所有数字加1  `$str =~ s/(\d+)/$1+1/ge`

- 匹配所有ASCII字符（参考man ascii）  `/[ -~]/`

- 替换\<b\>为\<strong\>   `$html =~ s|<(/)?b>|<$1strong>|g`

- 获得所有匹配内容  `my @matches = $text =~ /regex/g;`