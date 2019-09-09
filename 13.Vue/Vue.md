# 0. 题外话

## 0.1 问：为什么要在body里面引入js？

答：

* 要在body的结尾处添加
* 因为html渲染的顺序是从上到下，如果遇到script就会去解析
* 如果遇到太大的script，就会造成暂时的白屏，给用户一种很不好的感受

## 0.2 问：JavaScript new对象的四个过程

* new对象

  ```JS
  function Person(name,age){
       this.name=name;
       this.age=age;
  }
  var person=new Person("Alice",23);
  ```

* new 一个对象的四个过程：

  * 创建一个空对象

  ```JS
  var obj = new Object();
  ```

  * 让Person中的this指向obj，并执行Person的函数体

  ```JS
  var result = Person.call(obj);
  ```

  * 设置原型链，将obj的`__proto__`成员指向了Person函数对像的prototype成员对象

  ```JS
  obj.__proto__ = Person.prototype
  ```

  * 判断Person的返回值类型，如果是值类型，返回obj。如果是引用类型，就返回这个引用类型对象

  ```JS
  if(typeof(result)=="object")
       person=result;
  else
       person=obj;
  ```

  

# 1. Vue基础

本笔记记录的全是干货，过多的寒暄的话就不多说了，直接上重要的代码！

## 1.1 安装方式

方式1：采用本地文件引入的方式

方式2：采用 **`在线cdn`**（相当于把文件存入到一个库，调用的时候就近给你调用）引入的方式

方式3：采用 **`npm`** 安装的方式 



## 1.2 使用方式

> 1. body中,设置Vue管理的视图 `<div id="app"></div>`
> 2. 引入vue.js
> 3. 实例化Vue对象 new Vue();
> 4. 设置Vue实例的选项:如el、data...     
> 	new Vue({选项:值});
> 5. 在`<div id='app'></div>`中通过{{ }}使用data中的数据



## 1.3 实例选项

* el
  * 作用:当前Vue实例所管理的html视图
  * 值:**`通常`**是id选择器(或者是一个 HTMLElement 实例)  因为后边所有的项目都是id选择器 而且el一旦确定,就不再更改
  * **`不要让el所管理的视图是html或者body!`**
  * class选择器 只匹配第一个满足条件的元素 (Vue实例 => 管理视图 1对1)
* data
  * Vue 实例的数据对象，是响应式数据(数据驱动视图) 数据变化 => 视图变化
  * 可以通过 `vm.$data` 访问原始数据对象
  * Vue 实例也代理了 data 对象上所有的属性，因此访问 `vm.a` 等价于访问 `vm.$data.a`
  * 视图中绑定的数据必须**`显式`**的初始化到 data 中
  * 数据对象的更新方式 直接 采用 **实例.属性 = 值**
* methods
  * methods是一个**`对象`**
  * 可以直接通过 VM 实例访问这些方法，或者在**`插值表达式中使用`**。
  * 方法中的 `this` 自动绑定为 Vue 实例。
  * methods中所有的方法 同样也被代理到了 Vue实例对象上,都可通过this访问
  * 在data中命名时 不能和methods中的方法重名
  * 注意，**不应该使用箭头函数来定义 method 函数** (例如 `plus: () => this.a++`)。理由是箭头函数绑定了**`父级作用域`**的上下文，所以 `this` 将不会按照期望指向 Vue 实例，`this.a` 将是 undefined

##  1.4 插值表达式

> 1.作用:会将绑定的数据实时的显示出来: => 数据变化  => 所有绑定插值表达式的部分都会更新
>
> 2.形式: 通过 **`{{ 插值表达式 }}`**包裹的形式 
>
> 3.通过任何方式修改所绑定的数据,所显示的数据都会被实时替换(**响应式数据**)**数据驱动视图**
>
> 4.**`需要注意的是`**Vue实例上代理里 data中所有的属性 和 methods中的方法,而我们的el作用的视图直接可使用这些**`属性和方法`**  但是并不需要**`写this.属性 和 this.方法()`** 
>
> 5.**`注意`**:不能写 `var a = 10; 分支语句 循环语句`



## 1.5 v-text/v-html/{{ 插值表达式 }}

* v-text
  * 更新整个标签中的内容，相当于`innerText`
* v-html
  * 可以渲染到页面（不建议使用，会造成XSS跨站脚本攻击）相当于`innerHtml`
* {{ 插值表达式 }}
  * 只是更新局部作用域内的内容

## 1.6 v-if/v-show

* v-if

  * 元素的删除与增加
  * `适用于：`运行时条件很少改变
  * `缺点：`更高的切换开销

* v-show

  * style="display:none" 样式来决定
  * `适用于：`标签显示与隐藏切换频繁
  * `缺点：`更高的初始渲染开销

  问：如果有多个元素需要使用v-if或者v-show控制，但是又不能增加外部额外的标签怎么办？

  答：可以使用`template`标签进行包裹多个元素



## 1.7 v-on事件绑定

> - 使用: 绑定 v-on:事件名.修饰符="方法名"   可使用 @事件名="方法名的方式"
> - **注意** 方法名 中 可以采用$event的方式传形参  也可以直接写事件名 默认第一个参数为event事件参数
> - 修饰符(可不写)
>   - `.once` - 只触发一次回调。
>   - `.prevent` - 调用 `event.preventDefault()`。
> - 事件传参有两种形式 
>   - 匿名传参  =>当`只写`方法名时 =>  方法中默认第一个参数就是event(事件参数)
>   - 显示传参 => 当写方法名() => 如果想要获取event,必须显示的用$event传到方法里
>     - 方法名写括号和不写括号是有区别滴!!!!

### 1.7.1 补充（input中change事件与input中input事件）

* input中change只有失去光标时才会执行
* input中input时间只要数据变化就会执行

##  1.8 v-for / v-for-key

* v-for

  * `item in items` 或者 `item of items`   // item为当前遍历属性数组项的值

  * (item,index) in items   //item为当前遍历属性数组项的值 index为数组的索引

*  v-for-key

    * 使用: 通常是给列表数据中的唯一值 也可以用**`索引值`**
    
    * key通常是一个唯一值 => 身份证(循环项的身份证)
    
    * :属性="表达式" => 后面的表达式是变量
    
    * 属性="123"
    
        ```js
         <li v-for="(item,index) in list" :key="index">{{item}}---{{index}}</li>
        ```
    
    
    > 问：如果遇到循环，我们应该将v-for写在哪里呢？
    >
    > 答:   应该是重复的标签上  不是其父级元素上
## 1.9 v-for-数组/对象

* 数组
  * item in items   // item为当前遍历属性数组项的值
  * (item,index) in items   //item为当前遍历属性数组项的值 index为数组的索引
* 对象
  * item in items  // item为当前遍历属性对象的值
  * (item, key, index) in  items //item为当前遍历属性对象的值 key为当前属性名的值  index为当前索引的值

## 1.10 v-if与v-for相遇

> 问：v-for循环元素时,标签可使用item属性, 如果这个时候用v-if来进行操作 会产生什么效果?
>
> 例：`<p v-if="index>1" v-for="(item,index) in list"></p>`
>
> 结果： 以上代码执行: 会将数组中前两个元素忽略掉
>
> 说明：v-for 的优先级大于v-if ,所有v-if才能使用v-for的变量