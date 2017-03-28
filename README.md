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
</br></br>
css模块化设计原则
-----
#### 我们说模块是一个功能相对独立且完整的结构体，其实这应该是 组件 的概念，我们这里只从css的范围内来探讨模块化，那么模块的定义就可以缩窄到：一个（组）样式相对独立且完整的类。比如：
```
/* copy_right */
.m-copy_right {
  color: #ccc;
  background: #666;
  font-size: 14px;
  text-align: center;
  padding: 20px 0;
  line-height: 1.8;
}
/* nav */
.m-nav {
  color: #ccc;
  background: #666;
  font-size: 14px;
}
.m-nav .u-logo { ... }
.m-nav .list { ... }
.m-nav .list .item { ... }
```
##### 原则上来讲，一个css模块应该遵循以下几点要求：
  ● `只对外暴露一个类名；`（css模块设计原则第一点）
 ``` /**
 * 正确示范，所有模块相关的代码都挂在模块的选择器名下
 */
.m-nav { ... }
.m-nav .list { ... }
.m-nav .list .item { ... }
/**
 * 错误示范，暴露了.m-nav和.list两个类名，污染了空间
 */
.m-nav { ... }
.list { ... }
.list .item { ... }
```
 ##### ● 不影响周围布局：一般情况下，`尽量不要使用一个脱离文档流的布局（既使用了float:left/right，position:absolute/fixed的布局），尽量不要使用外边距（margin）`。这是为了使得模块更加稳定、具备更高的可塑性；（css模块设计原则第二点）
```
/**
 * 正确示范，在common中定义一个模块，在页面css中对模块进行定位和偏移
 */
/* common */
.u-logo {
  width: 100px;
  height: 100px;
}
.cm-news { //common中定义大体模块样式
  width: 200px;
  height: 100px;
}
/* index */
.u-logo {
  position: absolute;
  left: 20px;
  top: 20px;
}
.cm-news {  //页面css中进行定位和偏移
  margin-top: 50px;
}
/**
 * 错误示范，在common中定义一个模块并固定它的位置
 */
 ```
 
 ##### ● 模块尽量设计为方便复用的量级，避免大而全，求精巧；（css模块设计原则第三点）
 ```
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
  <title>index</title>
</head>
<body>
  <div class="g-index">
    <div class="g-bd">
      <!-- 正确的示范 -->
      `<!-- 创建一个大的内容块article_box，而不是一个大模块 -->`
      <div class="article_box">
        <div class="hd">
          最新文章
        </div>
        <div class="bd">
          <div class="list">
            `<!-- 这里我们把每一个项作为可复用的私有模块 -->`
            <div class="m-list_item">
              <img class="cover" />
              <div class="info">
                <div class="title">
                  <a href="#">文章标题</a>
                </div>
                <div class="desc">文章简介</div>
              </div>
            </div>
          </div>
        </div>
        <div class="ft">
          `<!-- 这里我们直接引入了一个公共分页模块 -->`
          <div class="cm-page">
            <a href="#" class="pg">1</a>
            <a href="#" class="pg">2</a>
            <a href="#" class="pg">3</a>
            <a href="#" class="pg">4</a>
          </div>
        </div>
      </div>
    </div>
  </div>
</body>
</html>
<!-- index.html -->
```
##### 值得注意的是，这里的原则第三点，并不只是出于css模块化的考虑，事实上这更适用于 组件化 的设计思路，这在后面讲组件化的时候我们会提到。

</br></br>
通过scss弥补css模块化的缺陷
------
#### 大体scss模块化结构（先把结果结构抛出来）
```
<!-- common.scss -->
@import './reset'; //或者normalize.css
@import './bootstrap.min';
@import './mixin';  //如果common中没用到mixin，就不引用

<!-- index.scss -->
@import './common';
@import './mixin';
.g-index {
  ...
}
```
#### css模块化的缺陷：
  * 冗余 体积庞大的common；
  * 使用cm-模块区别m-模块，使得后期开发过程中，m-模块向cm-模块转变过程比较繁琐；
#### 优化方式
##### 1、mixin.scss 模块化（引用mixin的scss文件才产生空间）
如果我们使用mixin来代替直接书写模块，由于mixin并不直接生成代码，而是通过主动引用，才能生成对应内容，那么理论上，common就可以无限多地存放模块而不必占用一点空间
把common中的模块全部打包成mixin：
```
/* common.css */
.m-nav { ... }
.m-news { ... }
.m-copy_right { ... }
改造后
// common.scss
@mixin m-nav {
  .m-nav { ... }
}
@mixin m-news {
  .m-news { ... }
}
@mixin m-copy_right {
  .m-copy_right { ... }
}
```
##### 2.公共样式如css reset，bootstrap，.clearfix等不与mixin混合一起。
我们思考一个问题，common除了存放模块之外，还有没有其他内容？答案是肯定的，大家一定知道有个东西叫做css reset（或者normalize.css），这肯定是全局的；另外，如果是做后台管理系统的话，可能还会有bootstrap。当然，还有一些自定义的全局的样式，比如常见的.clearfix，等等。
</br>
这些东西目前我们也堆积在common当中，而且合情合理，因为它们都是全局的样式。但是对比起mixin来说，这些实际内容代码显得很少量，有种被淹没的感觉，使得整个common看上去就像只有mixin。但是这些实际内容代码的作用却又非常重要。`为了使common的构成更加直观，我们把mixin全部都抽离出来，单独存放一个叫做mixin.scss的文件中`，然后在common引用它，这样，mixin的管理更加的规范，而且common的结构也更加清晰了。
</br>
抽离mixin还有另外一个重要原因，后面会讲到的，`我们希望mixin作为一个纯粹定义类代码文件，随处可以引用而不会生成多余的代码`
使用mixin：
```
<!-- index.scss -->
@import 'common';  // 引入common，如果有需要，common里一样可以引入mixin
@import 'mixin';  // 引入mixin
.g-index {
  ...
}
```
##### 3.插件样式的引用：
当我们需要在项目中全局引入bootstrap.min.css，说到全局暴露，我们第一时间想到的是common，没错，我们可以在common中引入
有人问，插件的.css文件怎么import？额，改一下扩展名为.scss就可以了，scss是兼容原生css语法的～
</br>
所以最终，我们的common大概是这样子的：
```
<!-- common.scss -->
@import './reset';
@import './bootstrap.min';
@import './mixin';
```
事实上，如果我们common中不需要使用到 mixin.scss 中的变量和mixin的话，我们可以不引用它。
</br>
那么我们的页面scss应该是这样的：
```
<!-- index.scss -->
@import './common';
@import './mixin';
.g-index {
  ...
}
```
干净，整洁。
</br>
#### mixin编写规范的原因：
##### 场景一：
 项目里有mixin.scss、a.scss（假设这是某个功能文件）、index.scss三个文件，mixin中定义了一个变量$fontSize: 16px;，a中定义了一个变量$color: #ccc;，我们在index中同时引用这两个文件，那么我们在index中是可以直接使用$fontSize和$color这两个变量的——我的意思是，尽管在index中我们并没有看到这两个变量的声明和定义，但它们就这么存在了。

这是好事还是坏事呢？直觉告诉我，这可能有问题。没错，这是不是跟我们之前讨论过的 污染 很像？只不过我们之前是引用了common之后，index什么都还没写就已经被占用了很多模块名，而现在是因为引用了其他文件，而占用了index的很多变量名。另外，就维护的角度来看，这也是有问题的，如果我不事先告诉你，或者你不事先看过一遍mixin和a，你知道index中的$color是哪里来的吗？假设我们需要字体大小，你知道去哪个文件修改吗？另外，你怎么保证同时引用mixin与a的时候，他们之间有没有可能存在同名的变量？那谁覆盖谁呢？这些问题看起来很小，但是当你项目规模大的时候，这可能是无法挽回的灾难（吓唬谁呢？？？）。

##### 场景二：
 假设我们的项目有一个主题色，边框、tab背景、导航栏背景，以及字体颜色等等，都是这个主题色，为了方便使用，不想总是用取色器去取值，于是我们在mixin中定义了一个全局变量$color: #ff9900，然后就可以愉快地到处使用了！

整个网站开发完了，一个月后，设计师突然过来跟你说：“老板说，这个主题色要改改，有点土，咱们换个大红。”，于是你一脸不情愿然而内心却窃喜地打开mixin，把$color的值改成了大红，然后得瑟地对设计师说：“幸好我早有准备，搞定了，你看看吧。”，保存，打开页面一看，设计师和你的脸都绿了，页面怎么这么丑，有些字原本是主题色，但背景是红色，而现在一改，整块都变成红的，内容都看不清了，有些边框原本就是红色的，但是字体是原本的主题色，然而现在一改，边框跟字体都变成红的了。

#### mixin编写规范：
  1. 全局变量只在mixin中定义，其他scss文件定义的变量（无论是暴露到全局还是局部）都只看作局部变量，不在当前文件以外的地方使用（即便是在能引用到的情况下，也避免使用）；
  2. 需要使用全局变量的地方直接import mixin；
  3. 一般来说，定义全局变量应该慎重，全局变量的数量应该尽量少；
  4. 尽可能不改动，如果需求变动，除非是对用途十分确定的情况，否则请新增一个全局变量来逐步替换需要修改的地方；
  5. 不要使用太过笼统的名词来作为全局变量，比如color，建议直接是用色值的描述，比如$orange: #ff9900，这使得我们在维护上更方便扩展，如果色值需要修改，但是又不是所有的地方都需要修改，那么我们可以新定义一个变量来扩展它，比如$red： red。
