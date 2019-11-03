## 理解JavaScript模块化开发

### 一、前言

​		最近公司有AngularJS项目需要用到我开发的第三方库，我用Webpack将自己的项目打包成一个JS文件，但在引用的时候遇到了一些坑，这篇文章用来梳理一下自己的趟坑之路。在梳理的过程中，了解了JS模块化的进化历程，学习到了CommonJS、AMD规范、CMD规范和ES6模块化等，下面我会根据自己的理解一一介绍。

### 二、什么是模块化

​		还记得刚刚入行看的几个js原生和jQuery项目，学习前辈代码的过程中，给我最大的感受就是头疼，动辄一个文件好几千行，有种无从下手的窘迫。而模块化的诞生与演化，就是愈发合理的解决一坨坨JS代码的过程。

​		读一个优秀程序员的代码，就像读一本善心悦目的书一样。从目录即可了解整本书的结构和中心思想，模块化就是可以从目录结构和模块名称，了解到项目中，划分明确的功能模块。

### 三、为什么要用到模块化呢？

​		模块化可以将复杂的程序拆分。块内由私有化属性和方法实现，对块外暴露出通用的接口，方便相关模块之间的互相调用。简单来说，模块化有如下三点好处：

​		**1.可复用性：**可复用性的终极目的，就是防止我们将一段相同的代码在不同的文件之间复制粘贴。将这段代码抽成一个独立的公用模块，需求的文件引入一下，岂不更加优雅？

​		**2.可维护性：**模块化，方便我们的代码实现教科书式的高内聚低耦合标准，良好的模块与外部代码完全拆分开，这样更易于开发者对该模块进行独立的改进和维护。

​		**3.避免命名空间污染：**没有模块化之前，所有函数外定义的变量都是全局变量，这样不仅会造成全局变量和局部变量命名重复的问题，而且由于全局变量谁都可以访问，同时造成了数据安全的问题。

### 四、模块化演化过程

#### 	（一）无模块化

​		HTML中通过script标签注入大量js文件。

```html
        <script src="a.js"></script>　　
        <script src="b.js"></script>
        <script src="c.js"></script>
        <script src="d.js"></script>
        <script src="e.js"></script>
        <script src="f.js"></script>
```
​		这样做最大的问题就是依赖模糊，比如我引入了依赖jQuery的库，那么我必须要在这个库之前引入jQuery。引入的模块必须要弄清楚依赖关系，在实际工作中真的很令人头疼。

#### 	（二）CommonJS规范

​		CommonJS规范起初是在服务器端运行Node.js的，因为文件资源已经都存在于服务器上，所以CommonJS起初的设计是一套同步的方案，从服务器上同步require模块化的文件，并不会耗费很多时间。不过要在前端浏览器采用CommonJS规范，需要将模块提前编译打包处理。2009年，CommonJS规范带来了，前端预编译的概念。

**基本语法：**

- 暴露模块：

  ```javascript
  module.exports = value
  ```

   或

  ```javascript
  exports.xxx = value
  ```

- 引入模块：

  ```
  require(xxx)
  ```

**浏览器实现commonJS规范：（借助Browserify预编译）**

1. 创建项目结构

   ```
   |-dist //打包生成文件的目录
   |-src //源码所在的目录
     |-module1.js
     |-module2.js
     |-app.js //应用主源文件
   |-index.html //运行于浏览器上
   ```

2. npm install -g browserify

3. 填写代码

   ```javascript
   // module1.js
   module.exports = {
   	 msg: 'module1',
       foo() {
            console.log(this.msg)
        }
   }
   ```

   ```javascript
   //module2.js
   module.exports = function () {
        console.log('module2')
   }
   ```

   ```javascript
   // app.js文件
   let module1 = require('./module1')
   let module2 = require('./module2')
   module1.foo() //module1
   module2() //module2
   ```


   <!-- index.html -->   
   <script type="text/javascript" src="./dist/bundle.js"></script>

   4.打包处理js

   根目录下运行

   ```
   browserify src/app.js -o dist/bundle.js
   ```

   ​		**CommonJS解决了HTML中注入大量js文件的问题，规范了模块的声明和引用。同时引领了浏览器预编译的前端浪潮。**

   #### 	（三）AMD规范

   ​		CommonJS规范是同步加载模块，并不适用于浏览器端，所以诞生了AMD，这个***异步加载模块的规范，且可以指定回调函数***。AMD规范是RequireJS在推广过程中对模块定义的规范化产出。

   **基本语法：**

   - 暴露模块

   ```javascript
   //定义没有依赖的模块
   define(function(){
      return 模块
   })
   ```

   ```javascript
   //定义有依赖的模块
   define(['module1', 'module2'], function(m1, m2){
      return 模块
   })
   ```

   - 引入模块

   ```javascript
   require(['module1', 'module2'], function(m1, m2){
      使用m1/m2
   })
   ```

   **使用require.js实现AMD规范**

   - 官网：<https://requirejs.org/>

   - GitHub：<https://github.com/requirejs/requirejs>

     ```
     < 题外话 >	在向前辈们探讨之前js代码如何模块化引入的过程中，得知angular1项目和一些简单的js项目，大家都比较倾向于使用require.js（AMD规范）这种方式去模块化导出和引入。
     ```

   下面连接就是require.js官网的一个单页面的例子，直接clone下来后执行命令

   ```
   node tools/r.js -o tools/build.js
   ```

   <https://github.com/volojs/create-templat>

   #### 	（四）CMD规范

   ​		CMD规范专门用于浏览器端，模块的加载也是异步执行的，且CMD规范整合了CommonJS和AMD规范的特点。CMD规范推崇懒加载和按需使用，不同于AMD规范的声明时执行。 **AMD 推崇依赖前置、提前执行**，***CMD推崇依赖就近、延迟执行***。CMD是SeaJS在推广过程中对模块定义的规范化产出。

   ```javascript
   // AMD
   define(['./a','./b'], function (moduleA, moduleB) {
     // 依赖前置
     moduleA.mehodA();
     console.log(moduleB.dataB);
     // 导出数据
     return {};
   });
   
   // CMD
   define(function (requie, exports, module) {
     // 依赖就近
     var moduleA = require('./a');
     moduleA.mehodA();     
   
     // 按需加载
     if (needModuleB) {
       var moduleB = requie('./b');
       moduleB.methodB();
     }
     // 导出数据
     exports = {};
   });
   ```

   #### 	（五）ES6模块化（推荐）

   ​		在ES6中，我们可以通过import关键字引入模块，通过export关键字导出模块。JavaScript终于在语言标准的层面上，实现了模块功能。ES6 模块的设计思想是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。CommonJS 和 AMD 模块，都只能在运行时确定这些东西。**但是由于ES6目前无法在浏览器中执行，所以，我们只能通过babel将不被支持的import编译为当前受到广泛支持的 require**。 

**基本语法：**

- 暴露模块

```javascript
/** 定义模块 math.js **/
var basicNum = 0;
var add = function (a, b) {
    return a + b;
};
export { basicNum, add };
```

- 引入模块

```javascript
/** 引用模块 **/
import { basicNum, add } from './math';
function test(ele) {
    ele.textContent = add(99 + basicNum);
}
```

**ES6模块和CommonJS规范差异**：

1.  *CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。*

   ```javascript
   ** CommonJS **
   
   // lib.js
   var counter = 3;
   function incCounter() {
     counter++;
   }
   module.exports = {
     counter: counter,
     incCounter: incCounter,
   };
   // main.js
   var counter = require('./lib').counter;
   var incCounter = require('./lib').incCounter;
   
   console.log(counter);  // 3
   incCounter();
   console.log(counter); // 3
   
   ** ES6 **
   
   // lib.js
   export let counter = 3;
   export function incCounter() {
     counter++;
   }
   // main.js
   import { counter, incCounter } from './lib';
   console.log(counter); // 3
   incCounter();
   console.log(counter); // 4
   ```

2.  *CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。*

   ​	因为 CommonJS 加载的是一个对象（即module.exports属性），该对象只有在脚本运行完才会生成。而 ES6 模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成。

### 五、总结

​		JS从最开始一个方法一个模块，到一个对象封装为一个模块，再到IIFE模式——匿名函数自调用（闭包）的方式形成一个模块。但由于市场需求对于前端要求越来越高，庞大的代码库不便于维护，由此诞生了CommonJS、AMD、CMD和ES6等一些列的模块化规范。

​		CommonJS规范起初用于服务器编程，同步加载模块，这并不适合在浏览器环境，因为同步意味着阻塞加载，浏览器资源是异步加载的，因此有了AMD、CMD解决方案。

​		接着到了AMD和CMD二分天下的时代，两位带代表库分别是Require.js和Sea.js。AMD实现了浏览器资源的异步加载，并且可以指定回调函数。而CMD兼容了AMD和CommonJS，推崇按需加载的原则。

​		最后，**ES6 在语言标准的层面上，实现了模块功能，而且实现得相当简单，完全可以取代 CommonJS 和 AMD 规范，成为浏览器和服务器通用的模块解决方案**。

### 六、后记

​		这是我的博客处女座，主要是想记录一下自己学习和爬坑的过程，加深印象。自己还是菜鸟一枚，还有很长很长的路要走。如果文中有不正确的地方，还希望各位可以帮忙指出，在此先提前表示感谢。希望我们都能，踏上技术之巅，护住我们的发际线。

