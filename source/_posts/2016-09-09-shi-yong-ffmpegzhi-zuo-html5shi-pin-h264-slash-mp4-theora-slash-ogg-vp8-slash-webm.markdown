---
layout: post
title: "使用ffmpeg制作HTML5视频(h264/mp4, theora/ogg, vp8/webm)"
date: 2016-09-09 15:53:37 +0800
comments: true
categories: 
---

在web页面中使用HTML5 `<video>`标签播放视频，可以很方便的用ffmpeg将本地视频转换为相应格式的视频。

首先在[ffmpeg官网](https://ffmpeg.org/)下载并编译对应系统版本的ffmpeg，然后可以使用以下命令转换视频格式。

``` bash
// 基本转换
$ ffmpeg -i input.mov output.mp4

// 通过video options 自定义输出视频的参数
$ ffmpeg -i input.mov -b 1500k -vcodec libx264 -s 640x480 -vpre hq output.mp4
$ ffmpeg -i input.mov -b 1500k -vcodec libvpx -s 640x480 -vpre hq output.webm
$ ffmpeg -i input.mov -b 1500k -vcodec libtheora -s 640x480 -vpre hq output.mp4
```

具体参数列表和使用方法请用`ffmpeg -h`和`man ffmpeg`查看

制作过程中发现视频音量有些太小，依然可以用ffmpeg调整音量

```
$ ffmpeg -i inputfile -vcodec copy -af "volume=10dB" outputfile
```

将视频上传到服务器上，就可以直接在`<video>`标签中引用了

```
<video id="movie" autoplay poster="http://yourDomain.com/yourDirectory/posterFrame.jpg">
    <source src="http://yourDomain.com/yourDirectory/yourMovie.mp4" type='video/mp4' />
    <source src="http://yourDomain.com/yourDirectory/yourMovie.webm" type='video/webm' />
    <source src="http://yourDomain.com/yourDirectory/yourMovie.ogg" type='video/ogg; codecs="theora, vorbis"' />
</video>
```