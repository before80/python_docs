+++
title = "`email`: 示例"
date = 2024-11-15T12:09:25+08:00
weight = 70
type = "docs"
description = ""
isCJKLanguage = true
draft = false

+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/email.examples.html](https://docs.python.org/zh-cn/3.13/library/email.examples.html)
>
> 收录该文档的时间：`2024-11-15T12:09:25+08:00`

# `email`: 示例

​	以下是一些如何使用 [`email`]({{< ref "/library/netdata/email#module-email" >}}) 包来读取、写入和发送简单电子邮件以及更复杂的MIME邮件的示例。

​	首先，让我们看看如何创建和发送简单的文本消息（文本内容和地址都可能包含unicode字符）：

```
# 导入 smtplib 以使用实际的发送函数
import smtplib

# 导入我们需要的 email 模块
from email.message import EmailMessage

# 打开 textfile 中相应名称的纯文本文件供读取。
with open(textfile) as fp:
    # 创建纯文本消息
    msg = EmailMessage()
    msg.set_content(fp.read())

# me == 发送方 email 地址
# you == 接收方 email 地址
msg['Subject'] = f'The contents of {textfile}'
msg['From'] = me
msg['To'] = you

# 通过我们使用的 SMTP 服务器发送消息。
s = smtplib.SMTP('localhost')
s.send_message(msg)
s.quit()
```

​	解析 [**RFC 822**](https://datatracker.ietf.org/doc/html/rfc822.html) 标题可以通过使用 [`parser`]({{< ref "/library/netdata/email/email_parser#module-email.parser" >}}) 模块中的类来轻松完成：

```
# 导入我们需要的 email 模块
#from email.parser import BytesParser
from email.parser import Parser
from email.policy import default

# 如果 email 标头保存在文件中，则取消注释这两行：
# with open(messagefile, 'rb') as fp:
#     headers = BytesParser(policy=default).parse(fp)

#  或者如果要从字符串中解析标头（这是不太常见的操作），使用：
headers = Parser(policy=default).parsestr(
        'From: Foo Bar <user@example.com>\n'
        'To: <someone_else@example.com>\n'
        'Subject: Test message\n'
        '\n'
        'Body would go here\n')

#  现在标头条目将可作为字典访问：
print('To: {}'.format(headers['to']))
print('From: {}'.format(headers['from']))
print('Subject: {}'.format(headers['subject']))

# 你也可以访问地址的各个部分：
print('Recipient username: {}'.format(headers['to'].addresses[0].username))
print('Sender name: {}'.format(headers['from'].addresses[0].display_name))
```

​	以下是如何发送包含可能在目录中的一系列家庭照片的MIME消息示例：

```
# 导入 smtplib 以使用实际的发送函数。
import smtplib

# 以下是我们要用到的 email 包模块。
from email.message import EmailMessage

# 创建容器 email 消息。
msg = EmailMessage()
msg['Subject'] = 'Our family reunion'
# me == 发送方 email 地址
# family = 所有接收方的 email 地址列表
msg['From'] = me
msg['To'] = ', '.join(family)
msg.preamble = 'You will not see this in a MIME-aware mail reader.\n'

# 以二进制模式打开文件。 你也可以忽略子类型
# 如果你想要 MIMEImage 自动猜测的话。
for file in pngfiles:
    with open(file, 'rb') as fp:
        img_data = fp.read()
    msg.add_attachment(img_data, maintype='image',
                                 subtype='png')

# 通过我们自己的 SMTP 服务器发送 email。
with smtplib.SMTP('localhost') as s:
    s.send_message(msg)
```

​	以下是如何将目录的全部内容作为电子邮件消息发送的示例： [[1\]]({{< ref "/library/netdata/email/email_examples#id3" >}})

``` sh
#!/usr/bin/env python3

"""将目录的内容作为 MIME 消息来发送。"""

import os
import smtplib
# 用于根据文件扩展名来猜测 MIME 类型
import mimetypes

from argparse import ArgumentParser

from email.message import EmailMessage
from email.policy import SMTP


def main():
    parser = ArgumentParser(description="""\
Send the contents of a directory as a MIME message.
Unless the -o option is given, the email is sent by forwarding to your local
SMTP server, which then does the normal delivery process.  Your local machine
must be running an SMTP server.
""")
    parser.add_argument('-d', '--directory',
                        help="""Mail the contents of the specified directory,
                        otherwise use the current directory.  Only the regular
                        files in the directory are sent, and we don't recurse to
                        subdirectories.""")
    parser.add_argument('-o', '--output',
                        metavar='FILE',
                        help="""Print the composed message to FILE instead of
                        sending the message to the SMTP server.""")
    parser.add_argument('-s', '--sender', required=True,
                        help='The value of the From: header (required)')
    parser.add_argument('-r', '--recipient', required=True,
                        action='append', metavar='RECIPIENT',
                        default=[], dest='recipients',
                        help='A To: header value (at least one required)')
    args = parser.parse_args()
    directory = args.directory
    if not directory:
        directory = '.'
    # 创建消息
    msg = EmailMessage()
    msg['Subject'] = f'Contents of directory {os.path.abspath(directory)}'
    msg['To'] = ', '.join(args.recipients)
    msg['From'] = args.sender
    msg.preamble = 'You will not see this in a MIME-aware mail reader.\n'

    for filename in os.listdir(directory):
        path = os.path.join(directory, filename)
        if not os.path.isfile(path):
            continue
        # 根据文件扩展名来猜测内容类型。
        # 编码格式将被忽略，不过我们应当检查某些简单事务
        # 例如是否为 gzip 或压缩文件。
        ctype, encoding = mimetypes.guess_file_type(path)
        if ctype is None or encoding is not None:
            # 不可以猜测，或者文件已被编码（压缩），
            # 因此我们使用基本的比特位数据类型。
            ctype = 'application/octet-stream'
        maintype, subtype = ctype.split('/', 1)
        with open(path, 'rb') as fp:
            msg.add_attachment(fp.read(),
                               maintype=maintype,
                               subtype=subtype,
                               filename=filename)
    # 现在执行消息发送或存储
    if args.output:
        with open(args.output, 'wb') as fp:
            fp.write(msg.as_bytes(policy=SMTP))
    else:
        with smtplib.SMTP('localhost') as s:
            s.send_message(msg)


if __name__ == '__main__':
    main()
```

​	以下是如何将上述MIME消息解压缩到文件目录中的示例：

``` sh
#!/usr/bin/env python3

"""将 MIME 消息解包到一个文件目录中。"""

import os
import email
import mimetypes

from email.policy import default

from argparse import ArgumentParser


def main():
    parser = ArgumentParser(description="""\
Unpack a MIME message into a directory of files.
""")
    parser.add_argument('-d', '--directory', required=True,
                        help="""Unpack the MIME message into the named
                        directory, which will be created if it doesn't already
                        exist.""")
    parser.add_argument('msgfile')
    args = parser.parse_args()

    with open(args.msgfile, 'rb') as fp:
        msg = email.message_from_binary_file(fp, policy=default)

    try:
        os.mkdir(args.directory)
    except FileExistsError:
        pass

    counter = 1
    for part in msg.walk():
        # multipart/* 只是一些容器
        if part.get_content_maintype() == 'multipart':
            continue
        # 应用程序真的应该对所给文件名做无害化处理
        # 以保证 email 消息不能被用来覆盖重要的文件
        filename = part.get_filename()
        if not filename:
            ext = mimetypes.guess_extension(part.get_content_type())
            if not ext:
                # Use a generic bag-of-bits extension
                ext = '.bin'
            filename = f'part-{counter:03d}{ext}'
        counter += 1
        with open(os.path.join(args.directory, filename), 'wb') as fp:
            fp.write(part.get_payload(decode=True))


if __name__ == '__main__':
    main()
```

​	以下是如何使用备用纯文本版本创建 HTML 消息的示例。 为了让事情变得更有趣，我们在 html 部分中包含了一个相关的图像，我们保存了一份我们要发送的内容到硬盘中，然后发送它。

``` sh
#!/usr/bin/env python3

import smtplib

from email.message import EmailMessage
from email.headerregistry import Address
from email.utils import make_msgid

# 创建基本的文本消息。
msg = EmailMessage()
msg['Subject'] = "Pourquoi pas des asperges pour ce midi ?"
msg['From'] = Address("Pepé Le Pew", "pepe", "example.com")
msg['To'] = (Address("Penelope Pussycat", "penelope", "example.com"),
             Address("Fabrette Pussycat", "fabrette", "example.com"))
msg.set_content("""\
Salut!

Cette recette [1] sera sûrement un très bon repas.

[1] http://www.yummly.com/recipe/Roasted-Asparagus-Epicurious-203718

--Pepé
""")

# 增加 html 版本。 这会将消息转换为一个 multipart/alternative 容器，
# 原始文本消息作为第一部分而新的 html 消息作为第二部分。
asparagus_cid = make_msgid()
msg.add_alternative("""\
<html>
  <head></head>
  <body>
    <p>Salut!</p>
    <p>Cette
        <a href="http://www.yummly.com/recipe/Roasted-Asparagus-Epicurious-203718">
            recette
        </a> sera sûrement un très bon repas.
    </p>
    <img src="cid:{asparagus_cid}" />
  </body>
</html>
""".format(asparagus_cid=asparagus_cid[1:-1]), subtype='html')
# 请注意我们需要将 <> 从 msgid 中去掉以便在 html 中使用。

# 现在添加相关图像到 html 部分中。
with open("roasted-asparagus.jpg", 'rb') as img:
    msg.get_payload()[1].add_related(img.read(), 'image', 'jpeg',
                                     cid=asparagus_cid)

# 创建我们将要发送的内容的本地副本。
with open('outgoing.msg', 'wb') as f:
    f.write(bytes(msg))

# 通过本地 SMTP 服务器发送消息。
with smtplib.SMTP('localhost') as s:
    s.send_message(msg)
```

​	如果我们发送最后一个示例中的消息，这是我们可以处理它的一种方法：

```
import os
import sys
import tempfile
import mimetypes
import webbrowser

# 导入我们需要的 email 模块
from email import policy
from email.parser import BytesParser


def magic_html_parser(html_text, partfiles):
    """返回链接到 partfiles 的经安全性处理的 html。

    重写 href="cid:...." 属性以指向 partfiles 中的文件名。
    虽然并非琐碎，这应可使用 html.parser 来实现。
    """
    raise NotImplementedError("Add the magic needed")


# 在真正的程序中你将从参数中获得文件名。
with open('outgoing.msg', 'rb') as fp:
    msg = BytesParser(policy=policy.default).parse(fp)

# 现在可通过字典形式访问标头条目，并且任何非 ASCII 内容
# 都将被转换为 unicode：
print('To:', msg['to'])
print('From:', msg['from'])
print('Subject:', msg['subject'])

# 如果我们想要打印消息内容的预览，可以提取
# 未经格式化的载荷并打印其中前三行。 当然，
# 如果消息没有纯文本部分则打印 html 的前三行
# 可能是无用的，但这只是个概念性的示例。
simplest = msg.get_body(preferencelist=('plain', 'html'))
print()
print(''.join(simplest.get_content().splitlines(keepends=True)[:3]))

ans = input("View full message?")
if ans.lower()[0] == 'n':
    sys.exit()

# 我们可以提取最丰富的替代项用于显示：
richest = msg.get_body()
partfiles = {}
if richest['content-type'].maintype == 'text':
    if richest['content-type'].subtype == 'plain':
        for line in richest.get_content().splitlines():
            print(line)
        sys.exit()
    elif richest['content-type'].subtype == 'html':
        body = richest
    else:
        print("Don't know how to display {}".format(richest.get_content_type()))
        sys.exit()
elif richest['content-type'].content_type == 'multipart/related':
    body = richest.get_body(preferencelist=('html'))
    for part in richest.iter_attachments():
        fn = part.get_filename()
        if fn:
            extension = os.path.splitext(part.get_filename())[1]
        else:
            extension = mimetypes.guess_extension(part.get_content_type())
        with tempfile.NamedTemporaryFile(suffix=extension, delete=False) as f:
            f.write(part.get_content())
            # 再次去除 <> 以将 cid 的 email 形式转为 html 形式。
            partfiles[part['content-id'][1:-1]] = f.name
else:
    print("Don't know how to display {}".format(richest.get_content_type()))
    sys.exit()
with tempfile.NamedTemporaryFile(mode='w', delete=False) as f:
    f.write(magic_html_parser(body.get_content(), partfiles))
webbrowser.open(f.name)
os.remove(f.name)
for fn in partfiles.values():
    os.remove(fn)

# 当然，许多 email 消息都有可能破坏这个简单的程序，
# 但它将能处理最普通的消息。
```

​	直到输出提示，上面的输出是：

```
To: Penelope Pussycat <penelope@example.com>, Fabrette Pussycat <fabrette@example.com>
From: Pepé Le Pew <pepe@example.com>
Subject: Pourquoi pas des asperges pour ce midi ?

Salut!

Cette recette [1] sera sûrement un très bon repas.
```

​备注
[[1]({{< ref "/library/netdata/email/email_examples#id2" >}})]

​	感谢 Matthew Dixon Cowles 提供最初的灵感和示例。
