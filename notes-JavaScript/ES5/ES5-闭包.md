## 闭包

### 定义

当函数能够记住并访问当前的词法作用域，就形成了闭包。即使该函数执行时的作用域不在“记住的那个词法作用域中”

### 解释产生原因 & 执行流程

> 代码执行顺序（预编译 -> 变量对象/可执行的代码 -> 执行） ->
> 执行前一刻：函数预编译（初始化`[[scope]]`：采用当前上下文的作用域 /变量对象（AO） -> 将AO 加到`[[scope]]`中 -> ） ->
> call stack 执行代码 + 产生闭包（无法销毁：内存泄露）

#### 执行期上下文

需要预编译的js代码，生成执行期上下文有如下三种：

- 全局代码创建全局执行上下文：独一份，整个页面的生命周期。
- 函数执行创建函数执行上下文：函数体内的代码会被编译，正常情况函数执行后上下文会销毁。
- eval 的代码也会被编译，创建上下文。

对于每个执行上下文，都有三个重要属性：

- 变量对象(Variable object，VO)
- 作用域链(Scope chain)
- this

#### 函数上下文

在函数上下文中，我们用活动对象(activation object, AO)来表示变量对象。活动对象和变量对象其实是一个东西，只是变量对象是规范上的或者说是引擎实现上的，不可在 JavaScript 环境中访问。只有到当进入一个执行上下文中，这个执行上下文的变量对象才会被激活。

活动对象是在进入函数上下文时刻被创建的，它通过函数的 arguments 属性初始化。

活动对象的初始化过程为：

- 产生作用域链（scope chain）
- 初始化arguments
- 初始化形参，将arguments中的值赋值给形参
- 找出所有的变量声明，按照变量名加入AO，如果已存在，忽略。
- 找出所有的函数声明，按照函数名加入AO，如果已经存在同名变量或者函数，替换。
- this初始化

#### 解释调用栈溢出

**什么是调用栈？**

调用栈：函数在执行过程中会创建一个执行上下文，JavaScript 引擎 为了 便于管理函数之间的调用关系，使用栈结构管理这些上下文。

> 栈是物理（例如链表、线性表）结构进行实现的，只要满足LIFO 的逻辑关系即可。 因此会存在越界情况，当越界时会发生错误通常称之为爆栈。

**递归易爆栈：(递归是函数调函数的一种编程思想)**

函数在调用时会创建执行上下文，并且只有在函数执行完毕后才会销毁。由于编写递归不规范、边界情况判断失误 或者 调用层数过大都会导致一直在call 函数，把调用栈填爆。

函数尾调用实际在浏览器支持度并不高，基本不可用。因此还是需要通过经典的方式进行解爆栈：循环解递归

在实际开发过程中，通过IDE调试工具、浏览器调试工具可以方便的断点调试函数，查看函数调用来源、环境变量等，因此了解函数调用栈和上下文对开发调试非常有帮助。

### 应用

- 函数防抖、函数节流

- 延长作用域（var声明语法）

```javascript
var obj = {};
for(var i = 0;i < 3;i ++) {
 obj[i] = function () {
  console.log(i)
 }
}
obj[0]()
obj[1]()
obj[2]()

// 都会打印3
```

利用闭包优化后为：

```javascript
var obj = {};
for(var i = 0;i < 3;i ++) {
 obj[i] = function (i) {
  function () {
  console.log(i)
 }
} (i)
}
obj[0]()
obj[1]()
obj[2]()

// 0 、 1 、 2
```

- 单例设计模式

### 危害

- 内存泄露
