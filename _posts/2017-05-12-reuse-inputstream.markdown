---
layout: post
title: InputStream的重用
tags: Java
---

有时候我们需要对同一个InputStream对象使用多次。比如，客户端从服务器获取数据 ，利用HttpURLConnection的getInputStream()方法获得Stream对象，这时既要把数据显示到前台（第一次读取），又想把数据写进文件缓存到本地（第二次读取）。

但第一次读取InputStream对象后，第二次再读取时可能已经到Stream的结尾了（EOFException）或者Stream已经close掉了。

而InputStream对象本身不能复制，因为它没有实现Cloneable接口。此时，可以先把InputStream转化成ByteArrayOutputStream，后面要使用InputStream对象时，再从ByteArrayOutputStream转化回来就好了。

部分代码：

```Java

InputStream input =  httpconn.getInputStream();

ByteArrayOutputStream baos = new ByteArrayOutputStream();

byte[] buffer = new byte[1024];

int len;

while ((len = input.read(buffer)) > -1 ) {

    baos.write(buffer, 0, len);

}

baos.flush();

InputStream stream1 = new ByteArrayInputStream(baos.toByteArray());

//TODO:显示到前台

InputStream stream2 = new ByteArrayInputStream(baos.toByteArray());

//TODO:本地缓存

```
