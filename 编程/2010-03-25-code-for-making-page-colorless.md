Title: 网站变黑白只要一行css代码
Url: /2010/03/25/code-for-making-page-colorless
Tags: css 

当国家发生一些大事，比如地震。我们会看到几乎所有的大型网站都会在一夜之间变成黑白的。我很好奇他们是怎么做的，于是google之，总结如下：

最简洁的把页面变成灰色的代码是在head 之间加：
```css
<style type="text/css">  
  html {  
    FILTER: gray  
  }  
</style>  
```

以下为全站CSS代码： 
```css
html { filter:progid:DXImageTransform.Microsoft.BasicImage(grayscale=1); } 
```

使用方法：**将代码加到CSS最顶端就可以实现素装。** 

如果网站没有使用CSS，可以在网页/模板的HTML代码<head>和</head> 之间插入： 
```html
<style>
  html{filter:progid:DXImageTransform.Microsoft.BasicImage(grayscale=1);}  
</style>  
```

有一些网站可能使用这个CSS不能生效，是因为网站没有使用最新的网页标准协议 
```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd"> 
<html xmlns="http://www.w3.org/1999/xhtml"> 
```
请将网页最头部的<html>替换为以上代码。 

有一些网站FLASH动画的颜色不能被CSS滤镜控制，可以在FLASH代码的<object …>和</object>之间插入： 
```html
<param value="false" name="menu"/>  
<param value="opaque" name="wmode"/>  
```