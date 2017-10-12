# js-
高程读书笔记

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
``` javascript
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
```
# 1.2 什么是this 
在学习this之前，我一直被网上的东西唬住。什么this很复杂了，是一个难点了等等。等我翻了一遍高程之后，感觉有许多自己之前不懂的东西，好像渐渐的去掉了那层迷雾。接下来就说说什么是this、以及call apply bind的区别。<br/>
首先必须要说的是，"this的指向在函数定义的时候是确定不了的，只有函数执行的时候才能确定this到底指向谁，实际上this的最终指向的是那个调用它的对象"(这句话有一些问题，在说明this的问题中不够严谨，不过后边会提到)<br/>
``` javascript
function a(){
    var user = "USER";
    console.log(this.user); //undefined
    console.log(this); //Window 从这里看this是谁
}
a(); 
```
这里的this指向的是Window<br/>
``` javascript
var o = {
    user:"UESR",
    fn:function(){
        console.log(this.user);  //USER
    }
}
o.fn();
```
this指向的是对象o，在调用fn是通过o.fn()执行的，那自然指向就是对象o<br/>
接下来让我们看一些好玩的事
``` javascript
var o = {
    user:"USER",
    fn:function(){
        console.log(this.user); // USER
        console.log(this) // o
    }
}
window.o.fn(); // 为什么这里没有指向window
```
有的人可能会问了，为什么这个代码没有指向window，而是console一个o对象出来
``` javascript
var o = {
    a:10,
    b:{
        a:12,
        fn:function(){
            console.log(this.a); //12
        }
    }
}
o.b.fn(); 
```
这里同样也是对象o点出来的，但是同样this并没有执行它.那么具体原因是什么？
情况1：如果一个函数中有this，但是它没有被上一级的对象所调用，那么this指向的就是window，这里需要说明的是在js的严格版中this指向的不是window，但是我们这里不探讨严格版的问题，你想了解可以自行上网查找。<br/>

情况2：如果一个函数中有this，这个函数有被上一级的对象所调用，那么this指向的就是上一级的对象。<br/>

情况3：如果一个函数中有this，这个函数中包含多个对象，尽管这个函数是被最外层的对象所调用，this指向的也只是它上一级的对象<br/>
下边的例子就可以对其进行一个证明：
``` javascript
var o = {
    a:10,
    b:{
        fn:function(){
            console.log(this.a); //undefined
        },
    }
}
o.b.fn(); 这里的this指向的是b b中没有一个a对象,因而输出undefined
``` 
不过还有一些特殊情况，当我们使用赋值时，类似下边这样：你会发现输出的是undefined
``` javascript
var o = {
    a:10,
    b:{
        a:12,
        fn:function(){
            console.log(this.a); //undefined
            console.log(this); //window
        }
    }
}
var j = o.b.fn;
j();
```
this永远指向的是最后调用它的对象，也就是看它执行的时候是谁调用的，例子4中虽然函数fn是被对象b所引用，但是在将fn赋值给变量j的时候并没有执行所以最终指向的是window

# 1.3 与this相关的apply、call、bind
学完this之后，可能我们会比较迷糊。因为this的用法可能看起来很混乱。所以，我们需要一些新的方法，来使this的指向显示的很清楚、或者很明确。apply、call、bind它们的作用就是这样。
<1> call
还是看看我们的需求，我们希望在调用的时候，将a中的user显示出来。当然，使用下边的代码是没有问题的，在调用fn的时候，将a中的user显示出来。
``` javascript
var a = {
    user:"guo",
    fn:function(){
        console.log(this.user); // guo
    }
}
a.fn(); // this 指向的是a
```
不过，有时候我们会遇到一些其他的问题。这样就会导致，一些this的功能与我们预期的不一样。看下边的代码：
``` javascript
    var a = {
    user:"guo",
    fn:function(){
        console.log(this.user); //
    }
}
var b = a.fn; // 
```
在这里，我们会发现user不能显示出来。只会将整个a显示出来
``` javascript
var a = {
    user:"guo",
    fn:function(){
        console.log(this.user); //guo
    }
}
var b = a.fn;
b.call(a); // 这里直接将b的this指向进行一个确定。明确规定它指向a
```
在call中，可以对其加参数.<br/>
第一个参数是将其指定在特定环境中。<br/>
后边的参数会一个个传递给函数。<br/>
``` javascript
var a = {
    user:"guo",
    fn:function(e,ee){
        console.log(this.user); // guo
        console.log(e+ee); // 3
    }
}
var b = a.fn;
b.call(a,1,2);
```
<2> apply
 apply 接受两个参数<br/>
 第一个参数也是指定了环境<br/>
 第二个参数是要传给函数的参数列表, 类型是 数组, apply 会把数组拆成一个个的参数传给函数<br/>
``` javascript
var a = {
    user:"guo",
    fn:function(){
        console.log(this.user); //guo
    }
}
var b = a.fn;
b.apply(a); 
```
类似这样：apply(console, arguments)(实际上 arguments 不是数组, 但是表现和数组一模一样, 你就暂时当它是一个数组)
``` javascript 
var a = {
    user:"guo",
    fn:function(){
        console.log(this.user);
    }
}
var b = a.fn;
b.bind(a);
``` 
这里你会发现它没有输出guo，当然了它还是输出一个函数。其实这个函数已经将this进行了一个小小的修改。<br/>
看下边的代码，你就会理解。
``` javascript
var a = {
    user:"guo",
    fn:function(){
        console.log(this.user);
    }
}
var b = a.fn;
var c = b.bind(a);
c() // guo
```
同样bind也可以有多个参数，并且参数可以执行的时候再次添加，但是要注意的是，参数是按照形参的顺序进行的.
``` javascript
var a = {
    user:"guo",
    fn:function(e,d,f){
        console.log(this.user); //guo
        console.log(e,d,f); //10 1 2
    }
}
var b = a.fn;
var c = b.bind(a,10);
c(1, 2); guo 10, 1, 2
```

