# js-
高程读书笔记
# 第四章阅读笔记
不要问我为什么不从第一章开始，前边的几章是对JS的大致介绍，以及一些其他的内容。从第四章才开始进入主题
* 什么是基本类型值？什么又是引用类型的值？
在介绍这个之前，我们需要先看看JS都有一些什么类型的值。5种基本类型：Undefined、Null、Boolean、Number、String 它们都是按值访问的，当然了什么是按值访问。<br/>
# 1.1 关于面对对象以及类的概念
我们在学习编程的过程中，总是会遇到一些问题。比如什么是面对对象，以及什么是面对过程。编程着重在于解决我们的需求，类似制造汽车，制造月饼。我们造汽车、月饼之前需要一个图纸，一份月饼模子。以后在制造的时候，不用关心模子怎么制造的，我们只用关心找到哪个模子可以制作我们想要的月饼。接下来，看看使用JS怎么实现制造“月饼”。
``` javascript
ES5:
// 创建一个类
var Student = function(name, height) {
    // 用 this.变量名 来创造一个只有类的实例才能访问的变量
    this.name = name
    this.height = height
}
// 实例化
var s1 = new Student("ming", 180)
var s3 = new Student("ming", 190)
s3.name = "hong"

// 原型上增加显示名字这个方法
Student.prototype.greeting = function() {
    console.log(`hello, i'm${this.name}`)
}
s1.greeting() // hello,i'm ming
s3.greeting() // hello,i'm hong

// 原型上增加更新这个方法 
Student.prototype.update = function(name, age) {
   this.name = name
   this.age = age
}

s3.update("hua", 25) // hua 190 25
```
ES6 的写法
``` javscript
class Student {
    // 初始化的套路
    constructor(name, height) {
        this.name = name
        this.height = height
    }

    // 方法没有 var
    greeting() {
        console.log(`hello, I'm ${this.name}`)
    }

    update(name, age) {
        this.name = name
        this.age = age
    }
}
var s1 = new Student("ming", 180)
var s3 = new Student("ming", 190)
s3.update("xiaofang", 25)
