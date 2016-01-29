---
layout: post
title: XSLT 学习笔记
tags: 杂货箱
---

XSLT 初学者，整理一些用法，加深记忆：

## 普通语法

### 0. 输出 HTML5 `<!DOCTYPE html>`

{% highlight xslt %}
<xsl:output method="html"
  encoding="UTF-8"
  indent="yes" />

<xsl:template match="/">
  <xsl:text disable-output-escaping='yes'>&lt;!DOCTYPE html></xsl:text>
  <html></html>
</xsl:template>
{% endhighlight %}

### 1. 创建变量

{% highlight xslt %}
<xsl:variable name="variable_name"> </xsl:variable>

<xsl:variable name="variable_name" select=" " />
{% endhighlight %}

调用变量：`$variable_name`, Emmet 缩写 `val`

### 2. 创建模板与调用模板

{% highlight xslt %}
<xsl:template name=""></xsl:template>
{% endhighlight %}

Emmet 缩写 `tn`

{% highlight xslt %}
<xsl:call-template name=""/>
{% endhighlight %}

Emmet 缩写 `call`

### 3. `value-of` 与 `copy-of`

{% highlight xslt %}
<xsl:value-of select=" " />

<xsl:copy-of select=" " />
{% endhighlight %}

后者包含 HTML 标签, Emmet 缩写 `val` `co`

### 4. 条件选择

{% highlight xslt %}
<xsl:choose>
  <xsl:when test=""></xsl:when>
  <xsl:otherwise></xsl:otherwise>
</xsl:choose>
{% endhighlight %}

Emmet 缩写 `ch` `wh` `ot`

### 5. `if` 判断

{% highlight xslt %}
<xsl:if test=""></xsl:if>
{% endhighlight %}

Emmet 缩写 `if`

### 6. `for-each` 输出

{% highlight xslt %}
<xsl:for-each select=""></xsl:for-each>
{% endhighlight %}

Emmet 缩写 `each`

### 7. 修改属性

{% highlight xslt %}
<xsl:attribute name=""></xsl:attribute>
{% endhighlight %}

例如, for-each 输出的第一项添加 `offset1` class

{% highlight xslt %}
<xsl:for-each select="">
  <div class="span4">
    <xsl:choose>
      <xsl:when test="positon() = 1">
        <xsl:attribute name="class">span4 offset1</xsl:attribute>
      </xsl:when>
    </xsl:choose>

    <!-- code -->
  </div>
</xsl:for-each>
{% endhighlight %}

Emmet 缩写 `attr`

### 8. 获取 handle 值

{% highlight xslt %}
<xsl:value-of select="entry/@handle"/>
{% endhighlight %}

### 9. 选择 handle 是某个具体值的条目

{% highlight xslt %}
<xsl:value-of select="entry[@handle='yes']"/>
{% endhighlight %}

### 10. 选择第 n 个条目

{% highlight xslt %}
entry[n]
{% endhighlight %}


## XSLT 函数

XSLT 1.0 函数列表 [http://zvon.org/xxl/XSLTreference/Output/xpathFunctionIndex.html](http://zvon.org/xxl/XSLTreference/Output/xpathFunctionIndex.html)

### 1. for-each 输出位置

{% highlight xslt %}
position()
{% endhighlight %}

选择奇数项:

{% highlight xslt %}
position() mod 2 = 1
{% endhighlight %}

### 2. 选择节点本身

{% highlight xslt %}
self::node()
{% endhighlight %}

### 3. 截取内容

{% highlight xslt %}
substring(string, start, length)
{% endhighlight %}

### 4. 连接字符串

{% highlight xslt %}
<xsl:variable name="new-string" select="concat($variable-1, 'string', $variable-2)"/>
{% endhighlight %}

### 5. for-each 循环 n 次

{% highlight xslt %}
<xsl:for-each select="(//node())[n >= position()]"></xsl:for-each>
{% endhighlight %}

[http://stackoverflow.com/questions/9076323/xslt-looping-from-1-to-60](http://stackoverflow.com/questions/9076323/xslt-looping-from-1-to-60)
