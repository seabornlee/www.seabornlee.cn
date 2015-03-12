---
title: Windows环境下用jwplayer+Nginx搭建视频点播服务器
author: SeabornLee
layout: post
permalink: /2011/03/07/vod-with-jwplayer-and-nginx-on-windows
categories:
  - 工作
--- 

flv视频可以采用两种方式发布：
>1. 普通的 HTTP 下载方式
>2. 基于Flash Media Server 或 Red5 服务器的 rtmp/rtmpt 流媒体方式。
 
多数知名视频网站都采用的是前一种方式。
两种方式对比如下：    
    
| Http                   |             Rtmp/rtmpt       |
-------------------------|------------------------------|
| 生成关键帧后可拖动播放     | 任意拖动播放                   |
| 下载完成后不再消耗服务器资源 | 无缓存，每次播放都会消耗服务器资源 |
 
这里说一下怎么用nginx 搭建http 方式的视频点播服务器，主要分为三步：

#### 一、nginx 安装及配置

1. 下载nginx 最新win32 版本解压到任意目录
2. 打开cmd 进入nginx 目录，使用命令`start nginx` 启动nginx （默认使用\conf\nginx.conf 配置，可用-c 选项指定要使用的配置文件，更多参数可使用nginx –h 查看）

#### 二、nginx 对flv 的支持

1. 编辑配置文件\conf\nginx.conf
在http 块加入启动gzip 的配置：
```apache
gzip  on;
gzip_min_length 1100;
gzip_buffers 4 8k;
gzip_types text/plain;
```
在server 块中加入启用flv 的配置：
```apache
limit_rate_after 1m; # 下载1M 后开始限速
limit_rate 100k; # 将速度限制为100k/s
location ~ \.flv {
  flv;
}
```
	
2. 使用命令`nginx –s reload` 重新加载配置文件

#### 三、客户端播放器

1. 下载开源的flash 播放器jwplayer
2. 将文件解压到nginx 下的html 目录
3. 访问http://localhot/player.swf?type=http&file=1.flv 即可播放视频
 
**注意**：
现在视频能播放了，但并不支持拖动功能，因为没有给flv 文件添加“关键帧”，可以的工具有“yamdi” 和“flvtool” ，以yamdi win32 版为例，下载解压后进入cmd 执行:
`yamdi –i src.flv –o dst.flv`
意思是将src.flv 添加关键帧，并输出为dst.flv 。

OK ，这时我们再访问
>http://localhot/player.swf?type=http&file=dst.flv 

即可自由拖动播放了。
