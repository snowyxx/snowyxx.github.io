---
layout: post
title:  "sharepoint designer(FrontPage)中的正则表达式使用"
date:   2012-11-02 12:20:19 +0800
categories: 笔记
tags:   [other]
---
sharepoint designer中的正则表达式和其他的正则表达不同，开始的时候很折磨人              
例如向后引用的分组使用的是大括号{}，而不是括号()。         
还有其他的不同，如使用#@等符号，:b表示空格，:d表示数等等。。。。  
折磨我的那个替换：                          
![sharepointRegex.png](/images/sharepointRegex.png)
    

    <table class=FTR_TABLE.*\n(^.*$\n)@</table>
    这个是一个处理多行的表达式，使用了\n和@

[微软的详细说明地址](https://support.office.com/en-US/article/Find-and-replace-text-by-using-regular-expressions-Advanced-eeaa03b0-e9f3-4921-b1e8-85b0ad1c427f){:target="blank"}
<table>
<tbody>
<tr>
<th style="background:rgb(204,204,204);">
<b>EXPRESSION</b></th>
<th style="background:rgb(204,204,204);">
<b>SYNTAX</b></th>
<th style="background:rgb(204,204,204);">
<b>DESCRIPTION</b></th>
</tr>
<tr>
<td>
Any character</td>
<td>
.</td>
<td>
<p>
Acts as a wild card to match any single printing or non-printing
character with the exception of the newline
(<i>\n</i>)character.</p>
For example, the regular expression&nbsp;<wbr><code>c.t</code>&nbsp;<wbr>matches
the strings cat, c t, cot, but not cost. In this example, the
period (.) is a wild card for a single character. It appears
between the letters 'c' and 't', so any single character between
the characters 'c' and 't' will match the expression — even if it
is a space.<br></td>
</tr>
<tr>
<td>
Maximal — zero or more</td>
<td>
*</td>
<td>
<p>
Matches zero or more occurrences of a character that precede the
expression, matching as many characters as possible.</p>
The regular expression&nbsp;<wbr><code>.*</code>&nbsp;<wbr>matches
zero or more occurrences of one character.<br>
For example, the regular expression&nbsp;<wbr><code>b.*k</code>&nbsp;<wbr>matches
book, back, black, blank, and buck. In this example, we combine the
period (.) with the asterisk (*) to make one syntax. The period (.)
appears immediately before the asterisk (*) expression. The
asterisk (*) matches zero or more occurrences of any character
between 'b and 'k'. The period (.) acts as a wild card for the
characters between 'b' and 'k'. In this example, it means that any
character between 'b' and 'k' can be repeated.<br></td>
</tr>
<tr>
<td>
Maximal — one or more</td>
<td>
+</td>
<td>
<p>
Matches one or more occurrences of a character that precede the
expression, matching as many characters as possible.</p>
The regular expression&nbsp;<wbr><code>.+&nbsp;<wbr></code>matches
one or more occurrence of one character.<br>
For example, the regular expression&nbsp;<wbr><code>bo+.</code>&nbsp;<wbr>matches
bob, book, and boot. In this example, we combine the period (.)
with the plus sign (+) to make one syntax. The period (.) appears
immediately after the plus sign (+) expression. The plus sign (+)
matches one or more occurrences of the letter 'o'. The period (.)
acts as a wild card for the last character of each word, which, in
this example are 'b', 'k', and 't'.<br></td>
</tr>
<tr>
<td>
Minimal — zero or more</td>
<td>
@</td>
<td>
<p>
Matches zero or more occurrences of a character that precede the
expression, matching as few characters as possible.</p>
The regular expression&nbsp;<wbr><code>.@&nbsp;<wbr></code>means
match zero or more occurrences of one character.<br>
For example, the regular expression&nbsp;<wbr><code>a.@x</code>&nbsp;<wbr>matches
'abx' within 'abxbxb' and 'acx' within 'acxcxc'. In this example,
we combine the period (.) with the at sign (@) to make one syntax.
The period (.) appears immediately before the at sign (@)
expression. The at sign (@) matches zero or more occurrences of any
character between 'a and 'x'. In this example, the period (.) acts
as a wild card for the characters 'b' and 'c' between the
characters 'a' and 'x'.&nbsp;<wbr><br></td>
</tr>
<tr>
<td>
Minimal — one or more</td>
<td>
# </td>
<td>
<p>
Matches one or more occurrences of a character that precede the
expression, matching as few characters as possible.</p>
For example, the regular expression&nbsp;<wbr><code>si.#er</code>&nbsp;<wbr>matches
'sicker' or 'silkier'. In this example, we combine the period (.)
with the sharp sign (#) to make one syntax. The period (.) appears
immediately before the sharp sign (#) expression. The sharp sign
(#) matches one or more occurrences of any character between 'si'
and 'er'. The period (.) acts as a wild card for the characters 'c'
and 'k' in the word sicker, and 'l', 'k', and 'i' in the word
silkier.&nbsp;<wbr><br></td>
</tr>
<tr>
<td>
Set of characters</td>
<td>
[ ]</td>
<td>
Matches any one of the characters within the brackets ([ ]). You
can specify ranges of characters by using a hyphen (-), as
in&nbsp;<wbr><code>[a-z]</code>.<br>

<p>
Examples:</p>
<ul type="disc">
<li>
The regular expression&nbsp;<wbr><code>c[aou]t&nbsp;<wbr></code>matches
cat, cot, and cut, but not cet or cit.</li>
<li>
The regular expression [0-9] means match any digit.</li>
<li>
You can specify multiple ranges of letters as well. The regular
expression&nbsp;<wbr><code>[A-Za-z]&nbsp;<wbr></code>means
match all upper and lower case letters.</li>
</ul>
</td>
</tr>
<tr>
<td>
Beginning of line</td>
<td>
^</td>
<td>
<p>
Anchors the match to the beginning of a line.</p>
For example, the regular expression&nbsp;<wbr><code>^When
in</code>&nbsp;<wbr>matches the any string that begins with
"When in" and that also appears at the beginning of a line, such as
"When in the course of human events" or "When in town, call me".
Whereas, this regular expression does not match "What and when in
the course of human events" if it appears at the beginning of a
line.<br></td>
</tr>
<tr>
<td>
End of line</td>
<td>
$</td>
<td>
<p>
Anchors the match to the end of a line.</p>
For example, the regular expression&nbsp;<wbr><code>professional$</code>&nbsp;<wbr>matches
the end of the string "He is a professional" but not the string
"They are a group of professionals".&nbsp;<wbr><br></td>
</tr>
<tr>
<td>
Beginning of file</td>
<td>
^^</td>
<td>
<p>
Anchors the match to the beginning of a file. Works only when
searching for text in source code or in text files.</p>
For example, to match the first HTML tag at the beginning of a
file, use the following regular
expression:&nbsp;<wbr><code>^^</code><br>
</td>
</tr>
<tr>
<td>
End of file</td>
<td>
$$</td>
<td>
<p>
Anchors the match to the end of a file. Works only when searching
for text in source code or in text files.</p>
For example, to match the last HTML tag at the end of a file (with
no spaces following the tag), use the following regular
expression:&nbsp;<wbr>$$&nbsp;<wbr><br></td>
</tr>
<tr>
<td>
Or</td>
<td>
|</td>
<td>
<p>
Indicates a choice between two items, thereby matching the
expression before or after the OR symbol (|).</p>
For example, the regular expression<code>(him|her)&nbsp;<wbr></code>matches
the following occurrences:<br>
<ul type="disc">
<li>
"it belongs to him"</li>
<li>
"it belongs to her"</li>
</ul>
<p>
but it does not match the line "it belongs to them."</p>
</td>
</tr>
<tr>
<td>
Escape special character</td>
<td>
\</td>
<td>
<p>
Matches the character following the back slash ( \ ). This allows
you to find characters that are used in the regular expression
syntax, such as a left curly brace ({) or a caret (^) or some other
special character.</p>
For example, you can use&nbsp;<wbr><code>\$</code>&nbsp;<wbr>to
match the dollar sign character ($) rather than implementing the
regular expression to 'anchor to end of a line'. Similarly, you can
use the expression&nbsp;<wbr><code>\.</code>&nbsp;<wbr>to
match the period (.) character rather than match any single
character, which is what the period (.) regular expression
does.<br></td>
</tr>
<tr>
<td>
Tagged expression</td>
<td>
{}</td>
<td>
<p>
Tags the text matched by the enclosed expression. You can match
another occurrence of the tagged text in a Find expression or
insert the tagged text in a Replace expression using \<i>N</i>.</p>
For example, suppose you are looking to find two duplicate,
consecutive words. To search, use the following
expression:&nbsp;<wbr><code>{.#}
\1</code><br>
<p>
With the assumption that the consecutive words are separated by a
single space, you'll want to add a space between the right curly
brace (}) and the back slash ( \ ).</p>
In this example, we combine the sharp sign (#) and the period (.)
with the curly braces ({}) to make one syntax. In this
expression,&nbsp;<wbr><code>.#</code>&nbsp;<wbr>represents
any consecutive characters. Since this portion of the expression is
surrounded by curly braces ({}), the consecutive characters will be
tagged and can be referred to as \1. This expression will find any
consecutive characters followed by a space, followed by those exact
same consecutive characters.&nbsp;<wbr><br></td>
</tr>
<tr>
<td>
<i>N</i>th tagged expression</td>
<td>
\<i>N</i></td>
<td>
<p>
In a Find expression, \<i>N</i>&nbsp;<wbr>matches the text
matched by the&nbsp;<wbr><i>N</i>th tagged expression,
where&nbsp;<wbr><i>N</i>&nbsp;<wbr>is a number from 1
to 9.</p>
<p>
In a Replace expression, \<i>N</i>&nbsp;<wbr>inserts the
text matched by the&nbsp;<wbr><i>N</i>th tagged expression
where&nbsp;<wbr><i>N</i>&nbsp;<wbr>is a number from 1
to 9. \0 inserts the text matched by the entire Find
expression.</p>
For example, suppose you want to find two duplicate, consecutive
words and replace them with a single word. To search, use the
following expression:&nbsp;<wbr><code>{.#}
\l</code><br>
<p>
With the assumption that the consecutive words are separated by a
single space, you'll want to add a space between the right curly
brace (}) and the back slash ( \ ). In this example, we combined
the sharp sign (#) and the period (.) with the curly braces ({}) to
make one syntax.</p>
To replace, use the following
expression:&nbsp;<wbr><code>\l</code><br>

<p>
\1 represents what was found in the first pair of curly braces in
the find string. By using \1 in the replace action, you essentially
replace the duplicate, consecutive words with a single copy of the
word.</p>
</td>
</tr>
<tr>
<td>
Group expression</td>
<td>
( )</td>
<td>
<p>
Marks the beginning and end of a subexpression.</p>
A subexpression is a regular expression that you enclose in
parenthesis ( ), such as the expression that
follows:&nbsp;<wbr><code>(ha)+</code>&nbsp;<wbr>In
this example, we combine the plus sign (+) with the parenthesis ( )
group expression to make one syntax. The subexpression is (ha)
because it is encapsulated within the parenthesis ( ). When you add
the plus sign (+), the expression enables you to find repeating
pairs of letters. The plus sign (+) represents one or more
occurrences of 'ha'.&nbsp;<wbr><br>
<p>
This expression matches the following occurrences 'haha' and
'hahaha'.</p>
</td>
</tr>
<tr>
<td>
Prevent match</td>
<td>
~<i>x</i></td>
<td>
<p>
Prevents a match
when&nbsp;<wbr><i>x</i>&nbsp;<wbr>appears at this
point in the expression.</p>
For example, the regular expression&nbsp;<wbr><code>real~(ity)</code>&nbsp;<wbr>matches
the "real" in "realty" and "really", but prevents the match to
"real" in "reality".&nbsp;<wbr><br></td>
</tr>
<tr>
<td>
Line break</td>
<td>
\n</td>
<td>
<p>
Matches a new line in Code view, or a<br>
in Design view.</p>
<p>
The syntax (\n), is a shorthand approach to enable you to match all
line breaks.</p>
</td>
</tr>
<tr>
<td>
Tab</td>
<td>
\t</td>
<td>
<p>
Matches a single tab character.</p>
<p>
For example, if you want to find all single tabbed characters at
the beginning of a line, the regular expression would look like the
following:</p>
<code>^\t+</code><br>

<p>
In this example, we combine the caret (^) and the plus sign (+)
with the tab (\t) to make one syntax. The caret (^) that precedes
the single tab character expression, anchors the match to all
tabbed characters at the beginning of the line. The plus sign (+)
represents the matching of one or more tab characters.</p>
</td>
</tr>
<tr>
<td>
Any one character not in the set</td>
<td>
[^]</td>
<td>
<p>
Matches any character that is not in the set of characters that
follows the caret (^).</p>
For example, to match any character except those in the range, use
the caret (^) as the first character after the opening bracket. The
expression&nbsp;<wbr><code>[^269A-Z]</code>&nbsp;<wbr>will
match any characters except 2, 6, 9, and any upper case
alphabetical characters.<br></td>
</tr>
<tr>
<td>
Repeat expression</td>
<td>
^<i>n</i></td>
<td>
<p>
Matches&nbsp;<wbr><i>n</i>&nbsp;<wbr>occurrences of
the expression that precedes the caret (^).</p>
For example,
with&nbsp;<wbr><i>n</i>&nbsp;<wbr>equaling 4, the
expression&nbsp;<wbr><code>[0-9]^4</code>&nbsp;<wbr>matches
any 4-digit sequence. In this example, we combine the set of
characters ([ ]) syntax with the repeat (^<i>n</i>) syntax to
demonstrate a more realistic use of regular expressions.<br></td>
</tr>
<tr>
<td>
Alphanumeric character</td>
<td>
:a</td>
<td>
Matches the expression&nbsp;<wbr><code>[a-zA-Z0-9]</code>.&nbsp;<wbr><br>

You can use the following expression:&nbsp;<wbr><code>[a-zA-Z0-9]</code>&nbsp;<wbr>to
match one occurrence of a letter (upper case or lower case) or
number. Also known as alphanumeric occurrences. You can use the
shorthand expression&nbsp;<wbr><code>:a</code>&nbsp;<wbr>for
all instances of&nbsp;<wbr><code>[a-zA-Z0-9]</code>.<br>
</td>
</tr>
<tr>
<td>
White space</td>
<td>
:b</td>
<td>
<p>
Matches any white spaces in code or text.</p>
For example, to match a single white space character at the
beginning of a line, use the following regular
expression:<code>^:b</code><br>
</td>
</tr>
<tr>
<td>
Alphabetic character</td>
<td>
:c</td>
<td>
Matches the expression&nbsp;<wbr><code>[a-zA-Z]</code>When
you use this expression, it enables you to match all upper or lower
case letters.&nbsp;<wbr><br>
You can use the shorthand expression&nbsp;<wbr><code>:c</code>&nbsp;<wbr>for
all instances of&nbsp;<wbr><code>[a-zA-Z]</code>.<br>
</td>
</tr>
<tr>
<td>
Decimal digit</td>
<td>
:d</td>
<td>
Matches the expression<code>[0-9]</code>This
expression enables you to match any digit.&nbsp;<wbr><br>
For example, suppose you want to find a social security number in a
text file. The format for U.S. social security numbers is
999-99-9999.&nbsp;<wbr><code>:d^3-:d^2-:d^4</code>&nbsp;<wbr>or,
by using [0-9], the same resulting expression:<code>[0-9]^3-[0-9]^2-[0-9]^4]</code><br>

You can use the shorthand expression&nbsp;<wbr><code>:d</code>&nbsp;<wbr>for
all instances of&nbsp;<wbr><code>[0-9]</code>.<br>
</td>
</tr>
<tr>
<td>
Hexadecimal digit</td>
<td>
:h</td>
<td>
Matches the expression&nbsp;<wbr><code>[0-9a-fA-F]+</code><br>

<p>
Use a this expression when you want to match a hexadecimal
combination of any upper or lower case letters between 'A' and 'F',
and any numbers.</p>
<p>
For example, suppose the pages in your Web site have multiple
different background colors and you want to change the color of
those pages to black, such as 000000. However, you do not know what
the hexadecimal numbers are for the existing colors. Use the
following regular expression to find all existing hexadecimal
numbers:</p>
<code>\#:h</code><br>

<p>
You could use [0-9a-fA-F] to search, but in this example we combine
the back slash (\) and the sharp sign (#) with the hexadecimal
digit (:h) syntax. \# matches a non-expression sharp sign (#) and
:h matches any sequence of hexadecimal characters.</p>
To replace the existing hexadecimal numbers, type the hexadecimal
number of the background color that you
want:&nbsp;<wbr><code>000000</code><br>
</td>
</tr>
<tr>
<td>
Identifier</td>
<td>
:i</td>
<td>
Matches the expression&nbsp;<wbr><code>[a-zA-Z_$][a-zA-Z0-9_$]*</code><br>

When working with code, if you want to match all program
identifiers, you can use the shorthand
expression&nbsp;<wbr><code>:i&nbsp;<wbr></code>to
replace having to type the lengthy expression above.<br></td>
</tr>
<tr>
<td>
Rational number</td>
<td>
:n</td>
<td>
Matches the expression&nbsp;<wbr><code>([0-9]+\.[0-9]*)|([0-9]*\.[0-9]+)|([0-9]+)</code><br>

If you want to match all whole numbers that contain a decimal
point, you can use the shorthand
expression&nbsp;<wbr><code>:n</code>&nbsp;<wbr>to
replace having to type the lengthy expression above.<br></td>
</tr>
<tr>
<td>
Quoted string</td>
<td>
:q</td>
<td>
Matches the expression&nbsp;<wbr><code>("[~"]*")|('[~']*')</code><br>

If you want to match all quotes surrounded by quotation marks, you
can use the shorthand expression&nbsp;<wbr><code>:q</code>&nbsp;<wbr>to
replace having to type the lengthy expression above.<br></td>
</tr>
<tr>
<td>
Alphabetic string</td>
<td>
:w</td>
<td>
Matches the expression&nbsp;<wbr><code>[a-zA-Z]+</code><br>

<p>
This syntax is a shorthand approach to enable you to match one or
more alphabetical characters, either lower case or upper case.</p>
</td>
</tr>
<tr>
<td>
Decimal integer</td>
<td>
:z</td>
<td>
Matches the expression&nbsp;<wbr><code>[0-9]+</code>&nbsp;<wbr><br>

<p>
This syntax is a shorthand approach to enable you match any number
from zero or more.</p>
</td>
</tr>
</tbody>
</table>