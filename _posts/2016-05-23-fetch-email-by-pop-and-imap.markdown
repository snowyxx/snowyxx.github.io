---
layout: post
title:  "使用python下载邮件"
date:   2016-05-23 09:16:05 +0800
categories: blog
tags:   [python]
---

* 目录
{:toc}

### POP和IMAP

因为公司要换邮件服务商，所以要把所有的邮件备份到本地一份。Outlook的数据文件太大了，而且依赖于Outlook，很不方便。所以我要把每个原始邮件保存为一个文件。
方便以后使用任意方法解析，或者使用任意邮件客户端工具打开。

收取邮件邮2种方式，pop（[Post Office Protocol - RFC 1725](https://tools.ietf.org/html/rfc1725.html))或imap（[Internet Message Access Protocol - RFC 2060](https://tools.ietf.org/html/rfc2060.html)）。

python对应的库为[poplib](https://docs.python.org/2/library/poplib.html)和[imaplib](https://docs.python.org/2/library/imaplib.html)

### POP

```python
import poplib

email = 'you@yourdomain.com'
password = 'yourpassword'
pop3_server = 'pop.yourserver.net'

server = poplib.POP3(pop3_server)
server.set_debuglevel(1)
print server.getwelcome()

server.user(email)
server.pass_(password)

print 'Messages: %s. Size: %s' % server.stat()  # stat()返回邮件数量和占用空间:
# Request message list, result is in the form (response, ['mesg_num
# octets', ...], octets). If which is set, it is the message to list.
print server.list()

# to get newest mail
latest_index = len(server.list()[1])
resp, lines, octets = server.retr(latest_index)

msg_content = '\r\n'.join(lines)
print msg_content

with open('raw.txt','wb') as fh:
     fh.write(msg_content)
```

因为pop不能获取收件箱以外的文件夹，所以选择imaplib。

### IMAP

```python
# coding:utf-8
import os
import sys
import imaplib
import re
import imapUTF7

def imap4(host, mailid, passwd):
    server = imaplib.IMAP4(host, 143)
    server.login(mailid, passwd)
    # get all folders
    list_pattern = re.compile(
        r'\((?P<flags>.*?)\) "(?P<delimiter>.*)" (?P<name>.*)')
    stat, folders = server.list()
    flist = {}
    for folder in folders:
        flags, delimiter, folder_name = list_pattern.match(folder).groups()
        # or ['\Sent','\Trash','\Drafts','\Junk']
        if flags in ['\Trash', '\Drafts', '\Junk']:
            continue
        folder_name = folder_name.strip('"')
        folder_name_str = imapUTF7.decoder(folder_name)[0]
        print "[*] Handling folder: %s" % folder_name_str

        # mkdir for each folder
        if not os.path.exists(folder_name_str):
            os.mkdir(folder_name_str)
        else:
            if os.path.isfile(folder_name_str):
                print '[-] %s is a file. We will not download mails within this folder' % folder_name_str
                continue
            else:
                print '[-] %s has already existed' % folder_name_str

        # get raw mail
        server.select(folder_name, readonly=True)
        sata, data = server.search(None, "ALL")
        indexlist = data[0].split()
        indexsize = str(len(indexlist))
        for n, index in enumerate(indexlist):
            filename = folder_name_str+'/raw_'+str(index)+'.eml'
            if os.path.exists(filename):
                print "[-] File : %s was downloaed before, so speak it. \r" % filename,
                continue
            print '[...]   %s of %s\r' % (str(n),indexsize),
            try:
                stat, data = server.fetch(index, '(RFC822)')
            # except imaplib.IMAP4.abort: # imaplib.abort: command: STATUS => socket error: EOF
            #     print '[-] i got the imaplib.IMAP4.abort error. Try to download the mail again: ' + str(index)
            #     server.logout()
            #     server = imaplib.IMAP4(host, 143) 
            #     server.login(mailid, passwd)
            #     server.select(folder_name, readonly=True)
            #     stat, data = server.fetch(index, '(RFC822)')
            except:
                flist.get(folder_name_str,[]).append(index)
                print "[-] Failed to download: %s - %s" % (folder_name_str,str(index))
                server.logout()
                server = imaplib.IMAP4(host, 143) 
                server.login(mailid, passwd)
                server.select(folder_name, readonly=True)
                continue
            with open(filename, 'wb') as fh:
                fh.write(data[0][1])
        print 'Downloaded %s mails in %s folder.' % (indexsize,folder_name_str)
    print "[-] Failed downloading:"
    print flist

```

> 文件夹名称使用了UTF-7编码，直接使用了[imapUTF7](https://code.google.com/archive/p/slivlen-oss/source)这个模块的方法

> 一个奇怪的问题，只要下载到某个邮件到时候就出现`imaplib.abort: command: STATUS => socket error: EOF`，所以在except中重试下载。要重新连接到邮件服务器。

最后保存的eml可以直接使用outlook打开。或者以后自己写个解析方法，大致应该类似这样：

```python
def print_info(msg, indent=0):
    if indent == 0:
        for header in ['From', 'To', 'Subject', 'Date', 'Cc', 'Bcc']:
            value = msg.get(header, '')
            if value:
                if header == 'Subject':
                    value = decode_str(value)
                elif header == 'Date':
                    d = parsedate(value)
                    value = '%i-%i-%i %i:%i:%i' % (d[0],
                                                   d[1], d[2], d[3], d[4], d[5])
                else:  # mail id
                    mailist = value.split(r',')
                    finalValue = ''
                    for mail in mailist:
                        hdr, addr = parseaddr(mail)
                        name = decode_str(hdr)
                        value = '%s<%s>,' % (name, addr)
                        finalValue += value
                    value = finalValue[:-1]
            print('HEAD -- %s%s: %s' % ('  ' * indent, header, value))
    if(msg.is_multipart()):
        # get_payload()获取所有MIEMMultipart的子元素
        parts = msg.get_payload()
        for n, part in enumerate(parts):
            print '%spart %s' % ('  ' * indent, n)
            print '%s--------------------' % ('  ' * indent)
            print_info(part, indent + 1)
    else:  # not a MIMEMultipart
        content_type = msg.get_content_type()
        if content_type == 'text/plain' or content_type == 'text/html':
            content = msg.get_payload(decode=True)
            charset = guess_charset(msg)
            if charset:
                content = content.decode(charset)
            print '%sText: %s' % ('  ' * indent, '...')
            filename = 'content'+str(indent)+'.html'
            if os.path.exists(filename):
                filename = 'content'+str(indent)+'_1.html'
            with open(filename,'w') as fh:
                fh.write(content)
        else:  # 附件
            print '%sAttachment: %s' % ('  ' * indent, content_type)
            filename = msg.get_filename()
            if filename: 
                # Decode filename
                h = email.Header.Header(filename)
                dh = email.Header.decode_header(h)
                filename = dh[0][0]

                print '%sAttachment File name: %s' % ('  ' * indent, msg.get_filename())
            else:
                pos = content_type.find(r'/')
                filename = 'attachment.'+content_type[pos+1:].strip()
            # to save attachment
            content = msg.get_payload(decode=True)
            with open(filename, 'wb') as f:
                f.write(content)


def decode_str(s):
    value, charset = decode_header(s)[0]
    if charset:
        value = value.decode(charset)
    return value


def guess_charset(msg):
    charset = msg.get_charset()
    if charset is None:
        charset = msg.get_content_charset()
        #below is a way to get charset, but not simple as above function
        content_type=msg.get('Content-Type').lower()
        pos=content_type.find('charset=')
        if pos>=0:
          charset=content_type[pos+8:].strip()f
```

---

参考：                                   
<http://www.pythoner.com/414.html>      
[http://www.liaoxuefeng.com/wiki/](http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001386832745198026a685614e7462fb57dbf733cc9f3ad000)                 
<http://stackoverflow.com/questions/7575943/eof-error-in-imaplib>

