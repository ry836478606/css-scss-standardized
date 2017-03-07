# css-scss-standardized
sort out some css adn scss rules for standardized

从CSS谈模块化
复用

要实现代码复用很简单，我们只需要提供一个公共css库，来存放我们的公共样式以及公共模块即可：

/* common.css */

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

.another-box ... {
  ...
}
然后我们在其他的css文件中引用这个common.css，这样就实现了代码的复用，只要是想全局共享的样式和模块，只要在这里添加进去就可以了。

<!-- index.html -->

<!DOCTYPE html>
<html>
<head>
  <title>index</title>
  <link rel="stylesheet" type="text/css" href="./style/common.css">
  <link rel="stylesheet" type="text/css" href="./style/index.css">
</head>
<body>
  <div class="box">
    ...
  </div>
  <div class="another-box">
    ...
  </div>
</body>
</html>

nimen
