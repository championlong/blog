# CodeReview中的问题

### 变量命名不规范

> 好的代码本身就是注释，我们要尽量规范和美化自己的代码来减少不必要的注释

“计算机科学只存在两个难题：缓存失效和命名。” ——Phil KarIton

[命名格式参考](https://www.cnblogs.com/liqiangchn/p/12000361.html)

[命名神器](https://unbug.github.io/codelf/)

### 重复代码

两个方法中存在相同的逻辑

*   提炼函数：将重复的代码复制到新的函数中，将引用的局部变量通过参数传递

重复代码只是相似而不是完全相同

*   移动语句：让存在关联的东西一起出现

![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RbGBLPrMqhQzj1K1/cf0963eea2444ee29fde38895b7de52d3264.png)

### 过长函数

> 小函数如果有良好的命名很易于理解，如果一段代码需要用注释解释清楚的时候就可以考虑将其抽成一个独立的函数，通过函数命名来解决。

函数内有大量的参数和临时变量，提炼函数就会很麻烦

*   以查询取代临时变量：getValue()

*   引入参数对象：传递结构体

循环

*   拆分循环：循环内的代码提炼到一个独立的函数中

条件表达式

*   以多态取代条件表达式

*   分解条件表达式：每个分支都应该通过提炼函数变成独立的函数调用

### 过长参数列表

> 过长的参数列表会让人更加迷惑

*   以查询取代临时变量

*   引入参数对象

*   移除标记参数![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RbGBLPrMqhQzj1K1/c5480140c6b54c49b1e5b15603cf60e63264.png)

*   函数组合成类：当多个函数有同样的几个参数可以使用![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RbGBLPrMqhQzj1K1/68b130f2292b442bad963dc371e909453264.png)

### 全局数据

> 从代码库的任何一个角落都可以修改它，而且没有任何机制可以探测出到底哪段代码做出了修改。容易引发bug

*   封装变量：用函数封装，控制变量的作用域![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RbGBLPrMqhQzj1K1/e371357e38e743dfa80538a1a78087cd3264.png)

### 可变数据

> 对数据的修改经常导致出乎意料的结果和难以发现的 bug，在一处更新数据，却没有意识到软件中的另一处期望着完全不同的数据

*   封装变量

*   拆分变量![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RbGBLPrMqhQzj1K1/a3e025efd935497b8010022d1e8dd4ff3264.png)

*   将查询函数和修改函数分离：把逻辑从处理更新操作的代码中搬移出，确保调用者不会调到有副作用的代码

*   移除设值函数![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RbGBLPrMqhQzj1K1/016fc95beb3c4c488d04b54af2258a723264.png)

*   以查询取代派生变量：如果可变数据的值能在其他地方计算出来，更容易引发bug，所以进行封装![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RbGBLPrMqhQzj1K1/00641c21e9cc4160bcc8826f28f8db813264.png)

*   将引用对象改为值对象：返回一个新的对象

### 发散式变化

> 每当要对某个上下文做修改时，我们只需要理解这个上下文，而不必操心另一个

*   拆分阶段![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RbGBLPrMqhQzj1K1/df76ac3a7769463d87150c3260b99bc93264.png)

### 霰弹式修改

> 需要修改的代码散布四处，不但很难找到它们，也很容易错过某个重要的修改。

*   搬移函数、搬移字段:  模块化，保证相互关联的逻辑最为恰当的组织到一起。

*   函数组合成类

*   函数组合成变换![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RbGBLPrMqhQzj1K1/ace61e7d24b943fa9976b0c2d2aa8f8c3264.png)

*   拆分阶段

*   内联函数、内联类：把本不该分散的逻辑拽回一处

### 依恋情结

> 一个函数跟另一个模块中的函数或者数据交流格外频繁，远胜于在自己所处模块内部的交流，这就是依恋情结的典型情况

*   搬移函数

### 数据泥团

> 两个类中相同的字段、许多函数签名中相同的参数。这些总是绑在一起出现的数据应该拥有属于它们自己的对象。有用的类被创建出来，大量的重复将会被消除。

*   提炼类：提炼到一个独立对象中

*   引入参数对象：将参数列表缩短，简化函数调用。

### 基本类型偏执

> 不愿意创建对自己的问题域有用的基本类型。一开始会用一个字符串来表示“电话号码”的概念，但是随后它又 需要“格式化”“抽取区号”之类的特殊行为。这类逻辑很快便会占领代码库，制造 出许多重复代码，增加使用时的成本。

*   以对象取代基本类型

### 重复的 switch

*   以多态取代条件表达式

### 过长的消息链

> 意味客户端代码将与查找过程中的导航结构紧密耦合。一旦对象间的关系发生任何变化，客户端就不得不做出相应修改。

*   隐藏委托关系![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RbGBLPrMqhQzj1K1/142cc12906a544b697451fdbc5eb54273264.png)