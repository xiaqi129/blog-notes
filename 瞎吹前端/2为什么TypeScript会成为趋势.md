## 为什么TypeScript会成为趋势？

### 一、什么是TypeScript？

- TypeScript 是 JavaScript 的类型的超集，它可以编译成纯 JavaScript。
- 编译出来的 JavaScript 可以运行在任何浏览器上。
- TypeScript 编译工具可以运行在任何服务器和任何系统上。
- TypeScript 是开源的。

### 二、为什么要用到TypeScript？

1. TypeScript 增加了代码的可读性和可维护性
   1. 类型系统实际上是最好的文档，大部分的函数看看类型的定义就可以知道如何使用了
   2. 可以在编译阶段就发现大部分错误，这总比在运行时候出错好
2. TypeScript 非常包容
   1. TypeScript 是 JavaScript 的超集，.js 文件可以直接重命名为 .ts 即可 
   2. 可以定义从简单到复杂的几乎一切类型
   3. 兼容第三方库，即使第三方库不是用 TypeScript 写的，也可以编写单独的类型文件供 TypeScript 读取
3. TypeScript 拥有活跃的社区
   1. 大部分第三方库都有提供给 TypeScript 的类型定义文件
   2. Angular 和 VSC 都是使用 TypeScript 编写的



*对比JS与TS的区别，来了解TS的妙处。*

```javascript
/*
    阅读下面这个JS方法，我们知道它获得一个参数a。
    那么关于这个参数a的任何信息，我们都不清楚。
    我们只能通过去阅读这个方法，来推测这个参数a的类型格式。
*/
function sortName(a) {
    var result = a.slice(0);
    result.sort(function (x, y) {
        return x.name.localeCompare(y.name);
    });
    return result;
}

```

```typescript
/*
    阅读下面ts代码，我们看到我们为参数a明确了类型。
    参数a类型映射为一个Person的接口，其中必须有name和age参数。
    且name参数类型为string，age参数类型为number。
    通过这样明确的类型定义，我们不去看方法体，就能了解方法的作用已经传参方式。
    这样可以在编译前，更轻松的找到我们代码种错误所在。
*/
interface Person {
    name: string;
    age: number;
}

function sortName(a: Person[]) {
    var result = a.slice(0);
    result.sort(function (x, y) {
        return x.name.localeCompare(y.name);
    });
    return result;
}
```



### 三、安装和编译方式

​	**安装**

```powershell
	npm install -g typescript
```

**手动编译ts文件**

```powershell
tsc helloworld.ts
```

**自动编译ts文件**

*生成tsconfig文件：*

```powershell
tsc -init
```

点击终端  => 运行任务 => tsc: 监视 - tsconfig.json

