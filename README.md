从CSS谈模块化
-----------
### 传统css痛点大致归纳为以下几点：</br>
  * 选择器繁琐冗长；</br>
  * 命名冲突；(命名污染)</br>
  * 层级结构不清晰；</br>
  * 代码难以复用；</br>
#### 复用（添加common公共样式表）</br>
要实现代码复用很简单，我们只需要提供一个公共css库，来存放我们的公共样式以及公共模块即可：</br>
common中书写公共模块，在具体页面的私有css里书写私有模块</br>
/* common.css */（公共库还能起到缓存的作用）
```
body {
  background: #fff;
  color: #333;
  font-size: 16px;
}
.box ... {
  background: #333;
  color: #fff;
  ...
}
```
#### 这里我们来探究几种情况：
  1. 假设我们这个项目非常大，大概有20个页面这么多，那么我们每做一个页面就会往common里面补充3～4个公共样式/模块，那么在这个项目开发完成以后，`common的体积可能要比其他css的体积都大`；</br>
  2. 假设有几个这样的页面，他们本身内容非常少，`比如404页面，可能只需要用到少量的公共样式，但是由于考虑到维护问题，我们还是要引入common（单独写样式会使得该页面在common更新的时候无法同步得到更新），这就使得一个页面变得很“重”`；</br>
  3. 由于common越写越大，它所占用的命名就越多，那么我们在引入common的时候，即使我们页面还什么都没有，但已经默认被`占用了很多的命名，使得我们在某个页面的可用命名变少，而且是越来越少`；</br>
  4. 我们在common中书写公共模块，在具体页面的私有css里书写私有模块，`假设现在我们需要全局添加一个公共模块.nice-box，我们发现，这个模块名已经在index.css中被占用了，于是我们试着把名字改成.handsome-box，却又发现这个名字在about.css中被占用了`，哦买噶的！
  
  
### 制定规范和css框架前缀：
#### 框架
我们假设有个页面index.html，它的整体最外围表现为一个class为.g-index的div，然后它由页头（.g-hd）、主体（.g-bd）、页脚（.g-ft）三个部分组成：</br>
```
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
  <title>index</title>
</head>
<body>
  <div class="g-index"> //g-index 框架
    <div class="g-hd"></div>
    <div class="g-bd"></div>
    <div class="g-ft"></div>
  </div>
</body>
</html>
```
#### 模块
还是以index.html为例，我们假设页头有个导航栏模块（.m-nav），主体有个新闻列表模块（.m-news），页脚有个版权声明模块（.m-copy_right）：</br>
```
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
  <title>index</title>
</head>
<body>
  <div class="g-index">
    <div class="g-hd">
      <div class="m-nav"> // m-nav模块
        nav
      </div>
    </div>
    <div class="g-bd">
      <div class="m-news">
        news
      </div>
    </div>
    <div class="g-ft">
      <div class="m-copy_right">
        copy_right
      </div>
    </div>
  </div>
</body>
</html>
```
#### 元件
我们假设这个页面还需要在页头放个logo（.u-logo），在导航栏中放置一个登录按钮（.u-login_btn）：</br>
```
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
  <title>index</title>
</head>
<body>
  <div class="g-index">
    <div class="g-hd">
      <img class="u-logo" alt="logo">  //u-logo元件
      <div class="m-nav">
        nav
        <a href="/logoin" class="u-login_btn">登录</a>  //u-logo元件
      </div>
    </div>
    <div class="g-bd">
      <div class="m-news">
        news
      </div>
    </div>
    <div class="g-ft">
      <div class="m-copy_right">
        copy_right
      </div>
    </div>
  </div>
</body>
</html>
```

### 总结：搭建内容的命名规范
在搭建框架的时候，我给框架元素命名用g-开头，给模块命名使用m-，给元件命名使用u-。这是命名规范的一部分，我们使用这三个前缀给相应结构体命名，就是为了更好地标志一个结构体，更好地展示它的功用，`这也是我们常说的语义化` ，同时也能实现隔离作用，起到类似命名空间的效果。</br>
  * `框架的命名以g-开头`，一般与页面同名，比如index.html，那框架就是最外层就是.g-index，about.html就是.g-about，以此类推，其他常用的内部结构有.g-hd（header）、.g-bd（body）、.g-ft（footer）、.g-sd（side）、.g-mn（main）等等；
  * `模块命名以m-开头`，一般以相对应的用途来命名，比如导航栏m-nav、新闻m-news、版权m-copy_right等等，一般来说模块名是唯一的，而且模块本身应该是可移植、可复用的；
  * ` 元件命名以u-开头`，一般以自身含义来命名，比如u-logo表示一个logo，u-btn表示一个按钮。
##### 那么除却框架、模块、元件的相关命名内容之外，命名规范还有以下几点内容：
  *  命名尽量以缩写的方式，言简意赅地表达，`比如用bd表达body，用nav表达navigator等`，使用长长的单词显得多余又臃肿；
  *  前缀与名称之间用'-'连接，而名称之间的若干单词以'\_'连接，组合单词除外，如`m-copy_right`；
  *  `z-开头表示状态，如z-active、z-succ、z-disabled等等`；
  *  `为公共模块单独使用一种前缀cm-来做区分，所有m-前缀的模块都是私有模块`；
  
##### 重构common
```
/* common.css */
body {
  background: #fff;
  color: #333;
  font-size: 16px;
}
.cm-nav { ... } //公共模块 （通过cm-nav和m-nav的方式来构建css唯一不足是，命名冲突时，要公共文件和私有文件来回修改）
.cm-news { ... }
.cm-copy_right { ... }

而我们的私有模块是这样的：
/* index.css */
.g-index {
  background: #fff;
  color: #333;
  font-size: 16px;
}
.m-nav { ... } //私有模块 ,私有模块可以用来覆盖或者增加公共模块的样式
.m-news { ... }
.m-copy_right { ... }
```
