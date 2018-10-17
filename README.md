此项目fork自[gaohaoyang.github.io](https://github.com/Gaohaoyang/gaohaoyang.github.io),原项目[README-zh-cn.md](https://github.com/cndaqiang/cndaqiang.github.io/blob/master/README-zh-cn.bak.md)

# 本地搭建过程
此操作只是为了能够本地预览博客效果，GitHub-page上已有环境
## windows安装本地环境
### 安装 ruby
[下载页面](https://rubyinstaller.org/downloads/)
下载安装
### 安装Ruby DevKit   
我安装的是 Ruby 2.4.0 (推荐2.4.x,2.5以上报错了)
使用 ridk install 安装DevKit   
### 安装jekyll
使用github-pages包
```
gem install github-pages
```

## ubuntu16.04安装本地环境
```
sudo apt-get install ruby2.3
sudo apt-get install ruby2.3-dev
sudo apt install zlib1g-dev
sudo gem update
sudo gem install github-pages
```
## 运行
进入网站目录
```
jekyll s [--port 端口号(不设置默认端口4000)]
```
浏览器访问`http://127.0.0.1:4000`

# 此项目使用
## 目录结构
参考[目录结构](http://jekyllcn.com/docs/structure/)

主要结构
```
/_config.yml 网站主要配置
/page 导航栏指向的界面
	
     里面的文本内容的格式 
	 ---
     layout: default
     title: 标题 #显示在导航上的内容
     permalink: 链接 #页面的固定链接
     icon: 图标 #导航栏上的图标
     type: page #页面类型，选择page页面
---
/_inclouds 页脚footer，页头head，评论系统等html模块化元素，用于引用组成一个html网页
/_layouts  主要页面的html内容，引用_incloud,_post等文件内容组成网页
/_site 自已生成不需操作
/_post 博客文章内容
```
## git clone已有博客页面

## 修改页面

 
修改，jekyll s 本地预览，git push推送

http://utf7.github.io/2016/09/30/setting-up-your-github-pages-site-locally-with-jekyll/

### 主要修改参考参考[README-zh-cn.md](https://github.com/cndaqiang/cndaqiang.github.io/blob/master/README-zh-cn.bak.md)
### 评论系统
参考[README-zh-cn.md](https://github.com/cndaqiang/cndaqiang.github.io/blob/master/README-zh-cn.bak.md)
#### 注
disqus的shortname，不是用户名，一个网站一个，setting里有
### 摘要预览
摘要由`_config.yml`中的`excerpt_separator: "摘要内容分隔符"`决定,此项目设置为`excerpt_separator: "\n\n\n\n"`即文章中连续四个回车前的内容显示在主页
#### 问题
windows下的换行是`^M\n`,windows版的GitBash好像提交代码时或自动修改为Unix的`\n`,但是之前在windows下的文件在linux下提交就不会了，所以，主页一整篇文章就是一个摘要
<br>比较好的解决方案是替换`_config.yml`中的`excerpt_separator: "摘要内容分隔符"`的`摘要内容分隔符`为非换行符，然后替换掉文章中的`摘要内容分隔符`<br>
也可以
```
sudo apt install dos2unix
dos2unix *.md
```

# 发布日期不能大于当前时间

# 添加页面底部联系方式的，图标
使用图标如简书 iconfont
参考http://www.jianshu.com/p/5d4a39cdf96d
在head.html添加 //at.alicdn.com/t/font_461356_6dhj8mgwisozjjor.css
在footer.html里修改
照着修改，在_config.xml   里添加用户名

# 博客设置

~~简书发布/保存成草稿~~
~~转发到github-page 解决图片缓存问题~~

