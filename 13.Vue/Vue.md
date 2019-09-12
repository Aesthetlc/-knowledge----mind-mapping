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

## 1.11 v-bind

* 作用：
  
  * 绑定标签上的任何属性
  
* 语法：
  
  * `<p v-bind:属性="数据对象中的属性名"></p>`
 ### 1.11.1 v-bind-绑定class -`对象`语法

* 语法：
  * 绑定class对象语法    :class="{ class名称": 布尔值 }"
  * `<p :class="{left:showClass}">内容</p>`

### 1.11.2 v-bind-绑定class -`数组`语法

- 语法：
  - 绑定class数组语法 :class="[class变量1,class变量2..]"
  - `<p :class="[activeClass,selectClass]" class="default">内容</p>`


### 1.11.3 v-bind-绑定`style-对象`语法

- 语法：
  - :style="{css属性名: 变量}"
  - `<p :style="{fontSize:fontsize}"></p>`

### 1.11.4 v-bind-绑定`style-数组`语法

- 语法：

  - :style="[对象1,对象2...]"
- 注意：对象可以是多个属性的 集合  同样里面的css属性需要遵从小驼峰命名的规则

  

## 1.12 v-model

* 作用

  * **`表单元素`**的绑定
* 特点

  * 数据发生变化可以更新到界面 => 响应式数据
  * 通过界面可以更改数据  => 表单数据变化 => viewmodel => 数据
  * ` v-model` 会忽略所有表单元素的 `value`、`checked`、`selected` 特性的初始值而总是将 Vue 实例的数据作为数据来源。应该在 `data`选项中声明初始值。
* 使用技巧：
  * v-model.lazy （变懒=>失去焦点显示）
  * v-model.number (能转换number就转，不能转换number就不转)
  * v-model.trim （去首位空格）

###  1.12.1 语法糖

* 通过v-on指令和 v-bind指令 组合实现v-model效果

  ```JS
  <body>
      <div id="app">
          <p>{{ name }}</p>
          <input @input="changeMsg" type="text" :value="name">
      </div>
      <script src="../vue.js"></script>
      <script>
          var vm = new Vue({
              el: '#app',
              data: {
                  name: "",
              },
              methods: {
                  changeMsg() {
                      this.name = event.target.value;
                  }
              }
          });
      </script>
  </body>
  ```

###  1.12.2-v-model-绑定其他表单元素

表单元素:  input  textarea checkbox radio  select 

注意：

*  checkbox在input标签中需要给定value值
* 所有表单元素一旦绑定了 v-model  就会忽略掉 原有的value值 checked值 selected值  需要从数据对象中取默认值

##  1.13 v-cloak

* 场景
  
  * 解决页面初次渲染时 页面模板闪屏现象
  
* 解决
  * 1.编写元素标签
  
  * 2.写入v-cloak指令
  
    * ```CSS
      [v-cloak]{
          display:none
      }
      ```
  
  * 3.将v-cloak指令 属性加上style
  
* 注意

  > 问：既然是加入属性选择器标签，那么为什么必须写v-cloak，不能使用其他名称么？
  >
  > 答:  不能写其他的名称，因为vue只认它

## 1.14 v-once

* 作用：

  * 使得所在元素只渲染一次  
* 补充事件修饰符
  * 阻止冒泡.stop =>e.stopPropagation();
  * 阻止默认行为 .prevent=>e.preventDefault();
  * 点了当前元素才会触发（触发源是自己的时候）.self
  * 只渲染一次 .once
  * 按下回车执行 .center（其他关键按钮，同理可证，键值也可以设置）

## 1.15 过滤器

> - 场景: data中的数据格式(日期格式/货币格式/大小写等)需要数据时
> - 使用位置:{{}}和v-bind="表达式 | 过滤器名称"
> - 具体用法:{{msg | 过滤器名字}}
> - 分类:**`本地(局部)`**和全局  全局 **`所有实例`**均可使用  Vue  局部过滤器 只有**`当前实例`**才可以使用
> - 本地: 通过el/data/methods**`选项`**filters
> - 局部和全局的区别 => 注册位置不同,应用范围不同
> - 全局: 在new Vue上面 Vue.filter()  => 所有实例都可以使用
> - 局部: 在Vue实例上 的选项上 filters => 所有过滤器集合 =>当前实例使用

* 全局过滤器

  * 在创建 Vue 实例**`之前`**定义全局过滤器Vue.filter()

  * Vue.filter('该过滤器的名字',(要过滤的数据)=>{return 对数据的处理结果});

  * 在视图中通过{{数据 | 过滤器的名字}}或者v-bind使用过滤器

    ```JS
    Vue.filter("toUpper", function(value) {
    	return value.charAt(0).toUpperCase() + value.substr(1);
    });// 过滤器核心代码
    ```

* 局部过滤器

  * 在vm对象的选项中配置过滤器filters:{}

  * (key)过滤器的名字: (value)(要过滤的数据)=>{return 过滤的结果}

  * 在视图中使用过滤器:  {{被过滤的数据 | 过滤器的名字}}

    ```js
    filters: {
        toUpper(value) {
          return value.charAt(0).toUpperCase() + value.substr(1);
        }
    }
    ```

* `注意： `

  * `局部过滤器只能用在当前Vue实例视图上`
  * `过滤器可以传递参数,第一个参数永远是前面传递过来的过滤值`
  * `过滤器也可以多个串行起来并排使用`
  
    

## 1.16 ref 操作 DOM

* 作用: 通过ref特性可以获取元素的dom对象
* 使用:  给元素定义 ref属性, 然后通过$refs.名称 来获取dom对象
* $refs是Vue实例的属性 
* $data/$event => $开头的属性和方法都是Vue实例的方法和属性

```JS
<input type="text" ref="myInput" /> // 定义ref
    
focus() {
	this.$refs.myInput.focus();
}  // 获取dom对象 聚焦
```

## 1.17 自定义指令

* 全局自定义指令

  * 在创建 Vue 实例之前定义全局自定义指令  Vue.directive()

  * 表达式对象 => value  => 表达式计算结果的值

  * 在视图中通过"v-自定义指令名"去使用指令

  * 指令名称要全小写

    > // 定义指令
    > // 自定义指令是不需要加v-前缀的
    > // 第二个参数为一个`对象`  对象中要实现 inserted的方法
    > // inserted中的参数为当前指令所在元素的dom对象
    > Vue.directive("focus", {
    >         inserted(dom,expression) {
    >           dom.focus();
    >         }
    >  });

* 局部自定义指令

  * 需要注册在`当前实例`上

  * `directives`这里注意是带s的与全部自定义指定名称不同

    > `directives`: {
    >
    > ​                htmlcopy: {
    >
    > ​                    inserted(dom, expression) {
    >
    > ​                        dom.innerHTML = expression.value;
    >
    > ​                    }
    >
    > ​                }
    >
    > ​            }

## 1.18 计算属性

- 场景:当(插值表达式/v-bind表达式)过于复杂的情况下 可以采用计算属性 对于任何**`复杂逻辑`**都可以采用计算属性,**`简单逻辑也可以采用计算属性`**

- 使用: 在Vue实例选项中 定义 computed:{ (key)计算属性名: **`带返回值`**的函数 }

- 说明: 计算属性的值 依赖 数据对象中的值  数据对象发生改变 => 计算属性发生改变=> 视图改变

  ### 1.12.8.1 计算属性与方法的区别

  > - methods 每次都会执行  
  > - 计算属性 每计算一次 都会讲结果`缓存`,如果 data中的数据没变化,则直接从缓存中取数据
  > - 如果 data中变化了 , 则会执行 新的计算方法 => 缓存
  > - 计算属性 会每次比较**`data更新`**前后的值 如果前后一致 则不会引起视图变化
  > - methods每次都会执行 `性能`较计算属性`较差`
  > - 计算属性是有`缓存机制`的,更智能化 ,更有`效率`
  > - 计算属性直接名称，方法除了名称之外还要有（）括号

  ### 1.12.8.2 计算属性与方法的基本使用

  > - 由于计算属性中 要return 值 => 立刻返回 => 同步/异步 => 计算属性方法 => **`必须写同步代码`**
  > - **`ajax/setTimeout`**  不能写在计算属性中

  >  computed: {
  >
  > ​                //通过计算属性实现**`字符串的翻转`**
  >
  > ​                fanzhuan() {
  >
  > ​                    return this.content.split("").reverse().join("");
  >
  > ​                },
  >
  > ​	}

##  1.19 Vue中实现发送网络请求

1. vue-resource: Vue.js的插件，已经不维护，不推荐使用
2. [axios](https://www.kancloud.cn/yunye/axios/234845) :**不是vue的插件**，可以在任何地方使用，

```js
//-----------------------------*************-------------------
// promise 是为了解决回调
$.ajax({
 url:'',
 data:{},
 success:function(data){
     // data为后端响应回来的数据
     $.ajax({
         url:''
         data:data,
         success:function(data1){
          $.ajax({
              url:'',
              data:data,
              success:function(data2){
                  
              }
          })  
       }
     })
 }
})
// -----------------------------*************-------------------
new Promise(function(resolve,reject){ 
 if(flag === 1) {
       resolve("success")  // 正确执行  返回一个字符串 success
 }else {
     reject(new Error("fail")) // reject 抛出错误 throw一个错误
 } 
}).then(result => {
  return  axios({url,data:result }) // 第一个请求 生成result1
}).then(result1 => {
  return axios(url,data:result) // 第二个请求生成result2
}).then(result2 => {
  return axios({url,data:result}) //第三个请求result3
}).then(result3 => {
  return axios({})
}).catch(err=>{
  
})
// 链式调用
// 如果reject  相当于执行失败 =>  执行失败=>不会进入到then => 会进入到promise的catch
// -----------------------------*************-------------------
//axios 返回的是一个promise对象 
axios ===  new Promise(function(reslove,reject){
       
})
axios().then(拿到返回数据(接口返回数据)).catch(err=> )
```

## 1.20 RESTFUL的接口规则

> - RESTful是一套**`接口设计规范`**
> - 用**`不同的请求类型`**发送**`同样一个请求标识`** 所对应的处理是`不同的`
> - 同样的请求标识 => 相同的请求地址**`url`**
> - 通过Http请求的不同类型(POST/DELETE/PUT/GET)来判断是什么业务操作(CRUD ) 
> - CRUD => 增删改查 => C =>Create(增)  R => Read(查)    U => UPDATE(改)  D =>DELETE(删)
> - json-server应用了RESTful规范

**HTTP方法规则举例**

| **HTTP方法** | **数据处理** | **说明**                                           |
| ------------ | ------------ | -------------------------------------------------- |
| POST         | Create       | 新增一个没有id的资源                               |
| GET          | Read         | 取得一个资源                                       |
| PUT          | Update       | 更新一个资源。或新增一个含 id 资源(如果 id 不存在) |
| DELETE       | Delete       | 删除一个资源                                       |

1. 查询数据  GET  /brands 获取db.json下brands对应的所有数据 **`列表`**
2. GET /brands/1 查询id=1数据 **`单条`**
3. 删除数据 DELETE   /brands/1 删除id=1数据
4. 修改数据 PUT  /brands/1 请求体对象 {name:'李四' }
5. 上传/添加 POST /brands 请求体  {name:'李四'}

​     6.   查询 GET /brands?title_like=关键字  -> 模糊搜索

## 1.21 axios

* Axios 是一个基于 promise 的 HTTP 库，简单的讲就是可以发送get、post请求。
  * 特点：
    * 1、可以在浏览器中发送 XMLHttpRequests
    * 2、可以在 node.js 发送 http 请求
    * 3、支持 Promise API
    * 4、拦截请求和响应
    * 5、转换请求数据和响应数据
    * 6、能够取消请求
    * 7、自动转换 JSON 数据
    * 8、客户端支持保护安全免受 XSRF 攻击
* 除去post请求 ,所有接口的正确请求返回码都是**`200`** 但是post的状态码是**`201`**

```JS
//get请求：
axios.get('http://localhost:3000/brands')
.then(res => {
console.log(res.data);
})
.catch(err => {
console.dir(err)
});
```

```JS
//delete请求：
axios.delete('http://localhost:3000/brands/109')
.then(res => {
console.log(res.data);
})
.catch(err => {
console.dir(err)
});
```

```js
//post请求
axios.post('http://localhost:3000/brands', {name: '小米', date: new Date()})
.then(res => {
console.log(res);
})
.catch(err => {
console.dir(err)
});
```

## 1.22 watch

* 当需要根据**`数据变化`** 进行相应业务操作,且该操作是**`异步操作`**时,**`计算属性不能再使用`**,可以使用监听watch特性

- watch=> 是监听data数据 中数据项的变化对象 => data中数据变化  => watch 监控函数执行 =>监控函数 
- 监控函数 =>  newValue.oldValue
- watch选项不需要返回值 不需要return  只需要在函数体中执行对应的逻辑即可\
- watch:  {  data属性名(监视谁写谁的名字): function(newValue(新值),oldValue(旧值)){} } 

##  1.23 组件

* 组件特点: 
  * 组件是一个**`特殊的 Vue实例`**
  * vue实例有**`el`**选项  组件实例没有el 但是**`templete`**页面结构
  * template 代表其页面结构 (**`有且只要一个根元素`**)
  * 每个组件都是**`独立`**的 运行作用域  **`数据 逻辑没有任何关联`**
  * 和实例相似之处:   data/methods/computed/watch  等一应俱全  
* 注意 
  * 不能和已有的标签名重名
  * 命名规范 （单词全小写）
    *  abc
    * abc-b
  * data是一个函数带有返回值的函数，并不在是一个对象
  
  ### 1.23.1 全局组件

```JS
      Vue.component("content-a", {
        template: `<div>
       				 {{count}}
       			   </div>`,
        data() {
          return {
            count: 1
          };
        }
      });
```

