@[TOC](基础知识常被8：ES6中的相关语法（持续更新中）)
在2019年的8月的最后一天，有必要写点总结的东西，所以今天就来讲讲ES6中的那点事儿。

## let，const，var的对比：
### let
####  1.使用let声明得变量不能再次声明
```js
   let a = 5;
   let a = 6;
   console.log(a); 
   错误提示：Uncaught SyntaxError: Identifier 'a' has already been declared
```
#### 2.具有块级作用域
```js
 {
   let a = 5;
   console.log(a);
 }
   console.log(a); //a is not defined
```
#### 3.let没有变量提升，必须要先定义在使用
```js
   console.log(a);  //undefined
   var a = 5;

   console.log(a); //Cannot access 'a' before initialization
   let a = 5;
```
#### 4.let的声明不会像var声明一样作用到window中
```js
   var abc = function(x, y) {
      return x + y;
   }
   console.log(abc(1,2));
   console.log(window) // 在这里window中已经存在abc的属性了，作用到了abc中


	let abc = function(x, y) {
      return x + y;
    }
    console.log(abc(1,2));
    console.log(window) // 因为是let定义的函数，所以在window的属性中就看不见abc的这个方法了
```
### const
#### 1.const 定义常量必须给初始值
```js
	const a;
    console.log(a);//Missing initializer in const declaration
```
#### 2.const 一但定义了就不能修改了
```js  
    const a = 5;
    const a = 10;
    console.log(a);Uncaught SyntaxError: Identifier 'a' has already been declared
```
#### 3.具有块级作用域（同let）
#### 4.常量是独立的，和window对象没有关系（同let）
#### 5.常量必须先定义再使用，没有提升（同let）
## 数组的解构：
#### 1.模式=数组
```js
模式=数组
        let [a,b,c] = ['测试1','测试2','测试3'];
        console.log(a); //测试1
        console.log(b);	//测试2
        console.log(c); //测试3
```
#### 2.变量多，值少
```js
变量多，值少
        let [a,b] =  ['测试1','测试2','测试3'];
        console.log(a); //测试1
        console.log(b); //测试2
```
#### 3.变量少，值多
```js
变量少，值多
        let [a,b,c] = ['测试1','测试2']
        console.log(a);//测试1
        console.log(b);//测试2
        console.log(c);undefined
```
#### 4.按需取值
```js
 按需取值
        let [a, , b] = ['测试1', '测试2', '测试3']
        console.log(a);//测试1
        console.log(b);//测试3
```
#### 5. 剩余值
```js
 剩余值
        let [a,b,...c] = ['测试1', '测试2', '测试3','测试4']
        console.log(a);//测试1
        console.log(b);//测试2
        console.log(c);//["测试3", "测试4"]
```
#### 6. 复杂情况
```js
  复杂情况
        let[,,[a,b],c] = ['aa','bb',['cc','dd'],'ee']
        console.log(a)//cc
        console.log(b)//dd
        console.log(c)//ee
```
## 对象的解构：
#### 1. 基本的解构
```js
 基本的解构
		let {id,name,age} = {id:1,name:"张三",age:18};
        console.log(id)//1
        console.log(name)//张三
        console.log(age)//18
```
#### 2. 按需取值
```js
 按需取值
		let {id,name} = {id:1,name:"张三",age:18};
        console.log(id)//1
        console.log(name)//张三
```
#### 3.剩余值
```js
 剩余值
		let {id,...c} = {id:1,name:"张三",age:18};
        console.log(id) //1
        console.log(c)// object{
        					age:18,
        					name:"张三"，
        				}
```
#### 4. 有冲突，可以为变量定义其他名字
```js
 有冲突，可以为变量定义其他名字
		let id = 5;
        let {id} = {id:1,name:"张三 ",age:18};//Identifier 'id' has already been declared
        let {id:nameId} = {id:1,name:"张三 ",age:18};//Identifier 'id' has already been declared
        console.log(nameId);//张三
```
#### 5. 复杂的情况
```js
 复杂的情况
		 let {code, data} = {
            code: 200, 
            msg: '成功', 
            data: [
                {id:1, title: 'aaa'}, 
                {id:2, title: 'aaa'}, 
                {id:3, title: 'aaa'}
            ]
        };
        console.log(code);
        console.log(data);
```
## 箭头函数
#### 样式：
```js
let fn = () =>{
			  }
()小括号内放入参数，{}大括号内放入函数体
```
#### 特点：
*	如果箭头函数参数只有一个，则小括号可以省略
*	如果箭头函数的函数体只有一行代码，则大括号可以省略，并且默认表示返回函数体的值（自带return效果）
*	箭头函数的内部没有arguments对象
	* 没有argument对象那怎么遍历呢？	
	* let abc = (...z) =>{
	            console.log(z);                 
	             }
	   abc(1,2,3,4,5,6);
* 箭头函数不能当作构造函数
* 箭头函数的内部没有自己的this,箭头函数的this指向外部作用域的this
## 函数参数的默认值
（如果某个参数有默认值，则需要把该参数放到参数列表后面）
```js
function abc (x,y=3){
	console.log(x+y);
}
```
## 内置对象的扩展：
### Array
#### 1.拼接
```js
拼接
        let arr1 = [1,2,3,4];
        let arr2 = [4,5,6,7];
        let arr3 = [...arr1,...arr2];
        console.log(...arr3);  //1,2,3,4,4,5,6,7
```
#### 2.将为数组转换成数组
```js
将为数组转换成数组
        let weishuzu = {
            0:"张三",
            1:"李四",
            2:"王五",
            length:3
        }
        let arr = Array.from(weishuzu);
        console.log(...arr)//张三，李四，王五
```
#### 3.forEach遍历
```js
forEach 遍历
        ES5
        [1,2,3,4,5,6,7,8,9].forEach(function(item){
            console.log(item)
        });
        ES6
        [1,2,3,4,5,6,7,8,9].forEach(item=>console.log(item));
```
#### 4.find(值)和findIndex(索引)
```js
find(值)和findIndex(索引)
        let result = [1,2,3,4,5,6,7,8,9].find(function(item,index,arr){
            return item>5;
        });
        console.log(result);
        let result = [1,2,3,4,5,6,7,8,9].findIndex(function(item,index,arr){
            return item>5;
        });
        console.log(result);

```
#### 5.includes（'查找的值',[开始的位置]）
```js
includes
		let arr = [1,2,3,4,5,6,7,8,9];
        console.log(arr.includes(4,2));//true
        console.log(arr.includes(4,9));//false
        console.log(arr.includes(10));//false
        console.log(arr.includes(1));//true
```
### String
#### 1.模板字符串
```js
模板字符串
        let a ="你好";
        let b = {
            name:"张三",
            age:18,
        };
        let c = `${b.name}${a}今天是一个号好天气,我今年${b.age}岁`
        console.log(c);//张三你好今天是一个号好天气,我今年18岁
```
#### 2.includes查找字符串中是否包含了某个字符串
```js
includes查找字符串中是否包含了某个字符串
        let a = 'ABCDEFGHIJKLMN';
        console.log(a.includes('A',0));//true  从索引为0的地方开始寻找A 返回boolean
        console.log(a.includes('A',1));//false
```
#### 3.startWith() 查找字符串开头是否含有某个字符串
```js
startWith() 查找字符串开头是否含有某个字符串
        let a = 'ABCDEFGHIJKLMN';
        console.log(a.startsWith('A'));//true
        console.log(a.startsWith('B'));//false
```
#### 4.endsWith() 查找字符串结尾是否含有某个字符串
```js
endsWith() 查找字符串结尾是否含有某个字符串
        let a = 'ABCDEFGHIJKLMN';
        console.log(a.endsWith('LMN'));//true
        console.log(a.endsWith('kMN'));//false
```
####  5.repeat() 重复一个字符串 参数为重复的次数
```js
repeat() 重复一个字符串 参数为重复的次数
        let a = 'ABCDEFGHIJKLMN';
        console.log(a.repeat(10));
```
####  6.trim()  去掉字符串两边的空白
```js
trim()  去掉字符串两边的空白
        let a = '        ABCDEFGHIJKLMN      ';
        console.log(a.trim());
```
### Number
```js
		Number.parseInt()
        Number.parseFloat()
        console.log(Number.parseInt('123abc'));
```

## 数据结构Set、Map
### set
```js
Set
        //Set的特点就是该对象里面的成员不会有重复。
        // - `size`：属性，获取 `set` 中成员的个数，相当于数组中的 `length`
        // - `add(value)`：添加某个值，返回 Set 结构本身。
        // - `delete(value)`：删除某个值，返回一个布尔值，表示删除是否成功。
        // - `has(value)`：返回一个布尔值，表示该值是否为`Set`的成员。
        // - `clear()`：清除所有成员，没有返回值。

        let s = new Set();
        let arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 8, 7, 6, 5, 4, 3, 2, 1];
        arr.forEach(function (item) {
            s.add(item) //set的add方法
        });
        console.log(...s); //去重  方式1
        console.log(Array.from(s)); // from是将伪数组变为数组; 去重 方式2
        console.log(s.size) //获取个数
        console.log(s.delete(9)); //删除数据返回boolean
        console.log(...s); //输出删除之后的set
        console.log(s.has(2)); //返回一个boolean，告知参数是否是set的成员
        s.clear(); //清空所有的成员，没有返回值
        console.log(s); //查看清空之后的set


 完成字符串去重
        let set = new Set('ababbc');
        let str = ([...set]).join('');
        console.log(str); // abc
```
### 遍历
```js
			let arr = ['add', 'delete', 'clear', 'has'];
            let list = new Set(arr);
			********************1.***********************
            for (let key of list.keys()) {
                console.log('keys', key);
            }
            //  keys add
            //  keys delete
            //  keys clear
            //  keys has
            **********************************************
            ********************2.***********************
			for (let value of list.values()) {
                console.log('value', value);
            }
            //  value add
            //  value delete
            //  value clear
            //  value has
            **********************************************
            ********************3.***********************
			for (let [key, value] of list.entries()) {
                console.log('entries', key, value);
            }
            //  entries add add
            //  entries delete delete
            //  entries clear clear
            //  entries has has
            **********************************************
             ********************4.***********************
			list.forEach(function (item) {
                console.log(item);
            })
            //add  delete  clear  has
            **********************************************
```
### weakset
* 方法

  * WeakSet.prototype.add(value)：向 WeakSet 实例添加一个新成员。

  * WeakSet.prototype.delete(value)：清除 WeakSet 实例的指定成员。
  * WeakSet.prototype.has(value)：返回一个布尔值，表示某个值是否在 WeakSet 实例之中

* 注意

   * WeakSet 没有size属性和forEach方法，没有办法遍历它的成员。
  * WeakSet的元素只能是对象， WeakSet中的对象是弱引用， 只是把地址拿过来， 没有clear属性， 不能遍历

* 原因：

  * WeakSet 不能遍历，是因为成员都是弱引用，随时可能消失，遍历机制无法保证成员的存在，很可能刚刚遍历结束，成员就取不到了。WeakSet 的一个用处，是储存 DOM 节点，而不用担心这些节点从文档移除时，会引发内存泄漏。 
```js
        let weakList = new WeakSet();
        let arg = {
            a: '1'
        };
        weakList.add(arg);
        weakList.add({
            b: '2'
        });
        console.log('weakList', weakList);
        //weakList WeakSet {Object {b: "2"}, Object {a: "1"}}
```
### map
* Map中的key可以是任意数据类型：字符串、数组、对象等 要注意集合**Set**添加元素用**add()**，而集合**Map**添加元素用**set()**
```js
		*******************************1.***************************
		let map = new Map();
        let arr = ['123'];
        map.set(arr, 456);
        console.log('map', map, map.get(arr));
        ************************************************************
        *******************************2.***************************
        let map = new Map([
            ['a', 123],
            ['b', 456]
        ]);
        console.log('map args', map);
        //map args Map {"a" => 123, "b" => 456}

        //size delete clear方法 与 遍历同set一样
        console.log('size', map.size); //size 2
        console.log('delete', map.delete('a'), map); //delete true Map {"b" => 456}
        map.clear();
        console.log('clear', map); //clear Map {}
        ************************************************************
```
### weakmap
* 方法：
	* 1.没有遍历方法clear()、keys()、values()、entries()、size属性

	* 2.只有get()、set()、has()、delete()
* 注意：
	*	同WeakSet一样接收的key值必须是对象， 没有size属性， clear方法， 也是不能遍历 
```js
        let weakmap = new WeakMap();
        let o = {};
        weakmap.set(o, 123);
        console.log(weakmap.get(o)); //123
````
## 定义对象的简洁方式
```js
		let id = 1;
		let name = 'zs';
		let age = 20;
		
		// 之前定义对象的方案
		// let obj = {
		//     // 属性: 值
		//     id: id,
		//     name: name,
		//     age: age,
		//     fn: function () {
		//         console.log(this.age);
		//     }
		// };
		
		// obj.fn();
		
		// ES6的新方案
		let obj = {
		    id,  // 属性名id和前面的变量id名字相同，则可以省略 :id
		    name,
		    nianling: age,
		    // 下面的函数是上面函数的简化写法，可以省略 :function  。但是注意这里仅仅是上面函数的简化，不是箭头函数
		    fn () {
		        console.log(this.name);
		    }
		};
		obj.fn();
```

***
**好了，今日就更新到这里，有时间我会持续更新ES6的最新语法的，欢迎大家前来评论！**

