---
layout: post
title:  "perl笔记1 - 使用printf格式化输出"
date:   2011-02-12 15:09:06 +0800
categories: 笔记
tags:   [perl]
---
纯笔记，内容来自《Learning Perl 5th Edition》

- `printf "Hello, %s; your password expires in %d days!\n", $user, $days_to_die;`

- To print a number in what’s generally a good way, use **%g**, which automatically chooses
floating-point, integer, or even exponential notation as needed:    
`printf "%g %g %g\n", 5/2, 51/17, 51 ** 17; # 2.5 3 1.0683e+29`

>“General” numeric conversion. Or maybe a “Good conversion for this number” or “Guess what I want the
output to look like.”

- The **%d** format means a decimal integer, truncated as needed:   
`printf "in %d days!\n", 17.85;# in 17 days!`

>Note that this is truncated, not rounded.
In Perl, printf is most often used for columnar data, since most formats accept a field
width. If the data won’t fit, the field will generally be expanded as needed:
printf "m\n", 42; # output like \`\`\`\`42 (the ` symbol stands for a space)
printf "-\n", 2e3 + 1.95; # 2001
>>There’s also %x for hexadecimal and %o for octal if you need those. But we really say “decimal” here as a
memory aid: %d for decimal integer.

- The **%s** conversion means a string, so it effectively interpolates the given value as a string,
but with a given field width:
<pre><code>printf "s\n", "wilma";# looks like `````wilma 
</code></pre>          
A negative field width is left-justified (in any of these conversions):             
<pre><code>printf "%-15s\n", "flintstone"; # looks like flintstone`````</code></pre>  

 - The **%f** conversion (floating-point) rounds off its output as needed, and even lets you
request a certain number of digits after the decimal point:
<pre><code>    printf "f\n", 6 * 7 + 2/3;
    printf ".3f\n", 6 * 7 + 2/3;
    printf ".0f\n", 6 * 7 + 2/3;
    # looks like ```42.666667
    # looks like ``````42.667
    # looks like ``````````43
</code></pre>  
- To print a real percent sign, use **%%**, which is special in that it uses no element from the
list:
`printf "Monthly interest rate: %.2f%%\n",5.25/12; # the value looks like "0.44%"`

- printf使用数组：       
<pre><code>my @items = qw( wilma dino pebbles );              
my $format = "The items are:\n" . ("s\n" x @items);              
## print "the format is >>$format<<\n"; # for debugging                
printf $format, @items;
</code></pre>
