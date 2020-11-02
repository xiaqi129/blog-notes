# Scss 学习笔记

​																																	author: Elon Xiang Qi



[TOC]

## 如何编译Sass

全局安装

```
npm install sass -g
```

编译

watch 单一文件

```bash
sass watch scss/app.scss css/app.css
```

watch 整个文件夹

```
sass watch scss:css
```

注意中间有个冒号！！！

## Partials

你可以创建一些只包含很少的代码片段的partial Sass files，然后可以把它们 import 到其他 Sass文件中。这种方式可以很好地模块化你的 CSS, 便于以后维护。一个 partial Sass 文件命名通常以下划线开头，比如 `_partial.scss`. 

**这样命名的好处是：Sass 会知道这是一个 partial 文件，它需要被import到其他文件中，在编译时，不会单独编译它**。

例如：定义了以下 partial 文件，并在 app.scss 中引入。

![1544078570513](http://wx4.sinaimg.cn/mw690/0060lm7Tly1fxy5ftcummj306t0a40st.jpg)

```scss
// app.scss
// IMPORT ALL SASS DIRECTORY FILES

@import '0-plugins/_plugins-dir';
@import '1-base/_base-dir';
@import '2-layouts/_layouts-dir';
@import '3-modules/_modules-dir'
```

在 import 文件时，并不需要包含文件的扩展名`.scss`不需要写。

当watch整个文件夹，在terminal敲入

```
sass --watch scss:css
```

会发现，Sass只编译了 app.scss

![1544078660175](http://wx3.sinaimg.cn/mw690/0060lm7Tly1fxy5d9k9itj30el01x0sv.jpg)



![1544078703722](http://wx1.sinaimg.cn/mw690/0060lm7Tly1fxy5f0rutjj306n07r0so.jpg)



这是我们想要的结果，不会编译其他多余的partial 文件。如果命名不是以下划线开始，所有文件都会编译了。就会变成这样：

![](http://wx2.sinaimg.cn/mw690/0060lm7Tly1fxy5cecamhj308n0g4wf1.jpg)

另外，在 app.scss 中 import partial 文件时，可以不加下划线。

```scss
// IMPORT ALL SASS DIRECTORY FILES

@import '0-plugins/plugins-dir';
@import '1-base/base-dir';
@import '2-layouts/layouts-dir';
@import '3-modules/modules-dir'

```

编译是没有问题的。



## 变量(Variable)

变量可以帮助我们保存一些想要重复使用的信息，比如 colors, font stacks, padding的值等等。 Sass 使用 `$` 来识别一个变量名. 

### colors

```scss
// _box.scss

// VARIABLES
$base-color: yellow;

.box {
    height: 100px;
    width: 100px;
    background-color: $base-color;
}
```

### font stacks

```scss
//_base.scss

// VARIABLES

$font-stack-serif: Georgia, Times, serif;
$font-stack-sans-serif: Helvetica, Arial, sans-serif;

body {
    background: orangered;
    color: white;
}

h1 {
    font-family: $font-stack-serif;
}

h2, h3 {
    font-family: $font-stack-sans-serif;
}
```

### 全局变量

如果我们在不同的 scss 文件中都定义了自己的变量，会显得很乱，也不便于以后维护。

推荐的做法是，定义一个全局的`_variables.scss`文件，将所有的变量放在这里。

```scss
// GLOBAL VARIABLES

$brand-color:pink;
$base-color: yellow;
$base-font-color: white;

$font-stack-serif: Georgia, Times, serif;
$font-stack-sans-serif: Helvetica, Arial, sans-serif;

$font-size-base: 18px;
```



然后在 app.scss 的第一行引入它。

```scss
// app.scss
// IMPORT ALL SASS DIRECTORY FILES

@import 'variables';
@import '0-plugins/plugins-dir';
@import '1-base/base-dir';
@import '2-layouts/layouts-dir';
@import '3-modules/modules-dir'
```

在其他文件中使用变量。

```scss
// _base.scss

body {
    background: $brand-color;
    color: $base-font-color;
    font-size: $font-size-base;
}

h1 {
    font-family: $font-stack-serif;
}

h2, h3 {
    font-family: $font-stack-sans-serif;
}
```

### 混合(Mixin)

mixin 是用来解决重复代码的，比较像没有返回值的函数，可以传入参数。

官网示范的一个用法是，解决CSS3属性的不同vender prefies的问题。其实，任何你需要reuse的代码，都可以使用mixin。

在CSS3的一些属性中，有些属性由于不同 vender prefixes的存在，变得非常冗长，例如 `transform` `box-shadow` `border-radius` 等等。

```css
.box {
    -webkit-transform: rotate(30deg);
    -ms-transform: rotate(30deg);
    transform: rotate(30deg);
}
```

rotate(30deg) 写了3遍。

使用 mixin 可以解决这个问题，因为 mixin 可以像函数一样传入参数。

```scss
@mixin transform($property) {
  -webkit-transform: $property;
  -ms-transform: $property;
  transform: $property;
}

.box { @include transform(rotate(30deg)); }
```

> 在Angular中，编译器会自动加上vender prefixes，所以在写scss时，可以不加这些prefixes。

### 全局mixin

再复杂一点：

```scss
// _box.scss

.box {
    height: 100px;
    width: 100px;
    display: inline-block;
    -webkit-border-radius: 100%;
    -moz-border-radius: 100%;
    border-radius: 100%;
    
    -webkit-box-shadow: 0 5px 10px #555;
    -moz-box-shadow: 0 5px 10px #555;
    box-shadow: 0 5px 10px #555;

    background: orangered;
    background: -webkit-linear-gradient(orangered, yellow);
    background: -o-linear-gradient(orangered, yellow);
    background: -moz-linear-gradient(orangered, yellow);
    background: linear-gradient(orangered, yellow);
}
```



为了保持各个module的清爽，新建一个文件`_mixins.scss`来设置全局的mixins。在 app.scss 中导入它。

```scss
// app.scss

@import 'variables';
@import 'mixins';
@import '0-plugins/plugins-dir';
@import '1-base/base-dir';
@import '2-layouts/layouts-dir';
@import '3-modules/modules-dir'
```

定义mixin

```scss
// _mixins.scss

// GLOBAL MIXINS

@mixin box($radius, $shadowColor, $gradientColor1, $gradientColor2) {
    -webkit-border-radius: $radius;
    -moz-border-radius: $radius;
    border-radius: $radius;
    
    -webkit-box-shadow: 0 5px 10px $shadowColor;
    -moz-box-shadow: 0 5px 10px $shadowColor;
    box-shadow: 0 5px 10px $shadowColor;

    background: $gradientColor1;
    background: -webkit-linear-gradient($gradientColor1, $gradientColor2);
    background: -o-linear-gradient($gradientColor1, $gradientColor2);
    background: -moz-linear-gradient($gradientColor1, $gradientColor2);
    background: linear-gradient($gradientColor1, $gradientColor2);
}
```

在 _box.scss 中使用

```scss
// BOX

.box {
    height: 100px;
    width: 100px;
    display: inline-block;
    @include box(100%, #555, orangered, yellow);
}
```

这样可以传入不同的参数来实现不同的box啦。

```scss
#box2 {
    @include box(20px, #000, #639, #fc3);
}

#box3 {
    @include box(5px, #ddd, black, teal);
}
```

消除了很多重复性代码。

## 引用父级选择器“&”

Sass的这一特性依然是为了遵循`Dry principle`，减少重复代码。你可以使用 `@extend` 来共享属性。 

比如，我们要创建一个message module，它有3个状态，success、warning和error。

```html
// index.html
<body>
   <div class="message success"></div>
   <div class="message warning"></div>
   <div class="message error"></div>
</body>
```

我们可以使用嵌套，将样式定义成这样：

```scss
// _message.scss

.message {
  width: 100%;
  height: 50px;
  background: #000; 
  margin: 10px auto;
  &.success {
    background: green;
  } 
  &.warning {
    background: orange;
  } 
  &.error {
    background: red;
  } 
}
```

无论CSS规则嵌套的深度怎样，关键字"&"都会使用父级选择器级联替换全部其出现的位置：

```scss
// SCSS
#main {
  color: black;
  a {
    font-weight: bold;
    &:hover { color: red; }
  }
}

// CSS
#main {
  color: black; }
  #main a {
    font-weight: bold; }
    #main a:hover {
      color: red; }
```

"&"必须出现在复合选择器开头的位置，后面再连接自定义的后缀，例如：



```scss
// SCSS
#main {
  color: black;
  &-sidebar { border: 1px solid; }
}

// CSS
#main {
  color: black; }
  #main-sidebar {
    border: 1px solid; }
```

如果在父级选择器不存在的场景使用&，Scss预处理器会报出错误信息。

## 继承(Extend)

使用extend来共享属性。

```scss
%message {
  width: 100%;
  height: 50px;
  background: #000; 
  margin: 10px auto;
}

.success {
    @extend %message;
    background: green;
}

.warning {
    @extend %message;
    background: orange;
}

.error {
    @extend %message;
    background: red;
}
```

html中不需要再加message class，更加简洁。

```html
<body>
   <div class="success"></div>
   <div class="warning"></div>
   <div class="error"></div>
</body>
```

## Maps

Sass提供了可以保存键值对的Maps结构。

```
$map: (key1: value1, key2: value2, key3: value3);
```

例如，我们在scss中会定义很多颜色值，一般使用变量就可以了。

```scss
$primary-color: #005DFF;
$accent-color: #FFF6BB;
$border-color: #ddd;

// other variables

body {
    background-color: $primary-color;
    color: $accenet-color;
}
```

当还定义了其他很多变量后，就显得十分杂乱。可以单独将所有颜色值封装成一个Maps结构，再使用`map-get($map,$key)`来获取值。

```scss
$colors: (
    primary: #005DFF,
    accent: #FFF6BB,
    border: #ddd
);

body {
    background-color: map-get($colors, primary);
    color: map-get($colors, accent);
}
```

## Function

function是有返回值的，最后要加 `@return`

比如在上例中，为了获取颜色值，我们会多次使用`map-get($map,$key)` ，看起来代码很长，可以自定义一个获取颜色的function来reuse这短代码。

```scss
$colors: (
    primary: #005DFF,
    accent: #FFF6BB,
    border: #ddd
);

@function color($color-name) {
    @return map-get($colors, $color-name);
}

body {
    background-color: color(primary);
    color: color(accent);
}
```

### 可选参数(Optional Arguments)

正常的在sass的function中定义的每一个参数，在调用的时候都必须存在。但是，您可以通过定义一个默认值来让一个参数成为可选的，如果那个参数没有被传递，这个默认值将被使用。使用方法在参数名后面加":"然后接上默认值。

```scss
// scss
@function invert($color, $amount: 100%) {
  $inverse: change-color($color, $hue: hue($color) + 180);
  @return mix($inverse, $color, $amount);
}

$primary-color: #036;
.header {
  background-color: invert($primary-color, 80%);
}
```

```css
// css
.header {
  background-color: #523314;
}
```

### 任意参数(Arbitrary Arguments)

很多时候我们需要接受任意数量参数的函数，如果@function声明中的最后一个参数以…结尾，然后将该函数的所有额外参数作为列表传递给该参数。

```scss
// scss
@function sum($numbers...) {
  $sum: 0;
  @each $number in $numbers {
    $sum: $sum + $number;
  }
  @return $sum;
}

.micro {
  width: sum(50px, 30px, 100px);
}
```

```css
// css
.micro {
  width: 180px;
}
```

## 插值(Interpolation)

属性的名称可以包括插值表达式，可以根据需要动态生成属性。甚至可以插入整个属性名。

```scss
// scss
@mixin prefix($property, $value, $prefixes) {
  @each $prefix in $prefixes {
    -#{$prefix}-#{$property}: $value;
  }
  #{$property}: $value;
}

.gray {
  @include prefix(filter, grayscale(50%), moz webkit);
}
```

```css
// css
.gray {
  -moz-filter: grayscale(50%);
  -webkit-filter: grayscale(50%);
  filter: grayscale(50%);
}
```

## 嵌套属性

CSS许多属性都位于相同的命名空间（例如font-family、font-size、font-weight都位于font命名空间下），Scss当中只需要编写命名空间一次，后续嵌套的子属性都将会位于该命名空间之下，请看下面的代码：

```scss
// SCSS
.demo {
  // 命令空间后带有冒号:
  font: {
    family: fantasy;
    size: 30em;
    weight: bold;
  }
}

// CSS
.demo {
  font-family: fantasy;
  font-size: 30em;
  font-weight: bold; }
```

命令空间上可以直接书写CSS简写属性，但是日常开发中建议直接书写CSS简写属性，尽量保持CSS语法的一致性。

```css
.demo {
  font: 20px/24px fantasy {
    weight: bold;
  }
}

.demo {
  font: 20px/24px fantasy;
    font-weight: bold;
}
```

