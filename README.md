# js-prototype
原文:https://segmentfault.com/a/1190000021232132#comment-area


![image](https://github.com/zhuxchong/js-prototype/blob/main/1.png)

图中Parent是构造函数，p1是通过Parent实例化出来的一个对象。
如果你看到这张图一脸懵，不要怕，往下看，下面会一步一步教你认识原型&原型链

前置知识
js的初学者一般很难理解原型和原型链的概念，但原型和原型链又是js中最重要的点之一。从jQuery到现在最火的框架之一Vue，原型的应用无处不在，那我们该怎么学好JavaScript的原型和原型链呢？

想要弄清楚原型和原型链，这几个属性必须要搞清楚，__proto__、prototype、 constructor。
其次你要知道js中对象和函数的关系，函数其实是对象的一种。
最后你要知道函数、构造函数的区别，任何函数都可以作为构造函数，但是并不能将任意函数叫做构造函数，只有当一个函数通过new关键字调用的时候才可以成为构造函数。如：
var Parent = function(){

}
//定义一个函数，那它只是一个普通的函数，下面我们让这个函数变得不普通
var p1 = new Parent();
//这时这个Parent就不是普通的函数了，它现在是一个构造函数。因为通过new关键字调用了它
//创建了一个Parent构造函数的实例 p1
如果到这你都能理解，
我们再引出一个概念，开始说过了要想清楚原型就要先搞清楚这三个属性，__proto__、prototype、 constructor。

我们记住两点
1.__proto__、 constructor属性是对象所独有的；
2.prototype属性是函数独有的；
3.上面说过js中函数也是对象的一种，那么函数同样也有属性__proto__、 constructor；

下面开始进入正题，我将上面的一张图拆分成3张图，分别讲解对应的3个属性。



1.prototype属性
为了方便举例，我们在这模拟一个场景，父类比作师父，子类比作徒弟。师父收徒弟，
徒弟还可以收徒弟。徒弟可以得到师父传授的武功，然后徒弟再传给自己的徒弟。
师父想要传授给徒弟们的武功就放到“prototype”这个琅琊福地中。徒弟徒孙们就去这里学习武功。

prototype属性可以看成是一块特殊的存储空间，存储了供“徒弟”、“徒孙”们使用的方法和属性。

![image](https://github.com/zhuxchong/js-prototype/blob/main/2.png)

它是函数独有的属性，从图中可以看到它从一个函数指向另一个对象，代表这个对象是这个函数的原型对象，这个对象也是当前函数所创建的实例的原型对象。
prototype设计之初就是为了实现继承，让由特定函数创建的所有实例共享属性和方法，也可以说是让某一个构造函数实例化的所有对象可以找到公共的方法和属性。有了prototype我们不需要为每一个实例创建重复的属性方法，而是将属性方法创建在构造函数的原型对象上（prototype）。那些不需要共享的才创建在构造函数中。
继续引用上面的代码，当我们想为通过Parent实例化的所有实例添加一个共享的属性时，

Parent.prototype.name = "我是原型属性，所有实例都可以读取到我";
这就是原型属性，当然你也可以添加原型方法。那问题来了，p1怎么知道他的原型对象上有这个方法呢，往下看↓↓↓

2.proto属性
__proto__属性相当于通往prototype（“琅琊福地”）唯一的路（指针）
让“徒弟”、“徒孙” 们找到自己“师父”、“师父的师父” 提供给自己的方法和属性

![image](https://github.com/zhuxchong/js-prototype/blob/main/3.png)

__proto__属性是对象（包括函数）独有的。从图中可以看到__proto__属性是从一个对象指向另一个对象，即从一个对象指向该对象的原型对象（也可以理解为父对象）。显然它的含义就是告诉我们一个对象的原型对象是谁。
prototype篇章我们说到，Parent.prototype上添加的属性和方法叫做原型属性和原型方法，该构造函数的实例都可以访问调用。那这个构造函数的原型对象上的属性和方法，怎么能和构造函数的实例联系在一起呢，就是通过__proto__属性。每个对象都有__proto__属性，该属性指向的就是该对象的原型对象。

p1.__proto__ === Parent.prototype; // true
__proto__通常称为隐式原型，prototype通常称为显式原型，那我们可以说一个对象的隐式原型指向了该对象的构造函数的显式原型。那么我们在显式原型上定义的属性方法，通过隐式原型传递给了构造函数的实例。这样一来实例就能很容易的访问到构造函数原型上的方法和属性了。
我们之前也说过__proto__属性是对象（包括函数）独有的，那么Parent.prototype也是对象，那它有隐式原型么？又指向谁？

Parent.prototype.__proto__ === Object.prototype; //true
可以看到，构造函数的原型对象上的隐式原型对象指向了Object的原型对象。那么Parent的原型对象就继承了Object的原型对象。由此我们可以验证一个结论，万物继承自Object.prototype。这也就是为什么我们可以实例化一个对象，并且可以调用该对象上没有的属性和方法了。如：

//我们并没有在Parent中定义任何方法属性，但是我们可以调用
p1.toString();//hasOwnProperty 等等的一些方法
我们可以调用很多我们没有定义的方法，这些方法是哪来的呢？现在引出原型链的概念，当我们调用p1.toString()的时候，先在p1对象本身寻找，没有找到则通过p1.__proto__找到了原型对象Parent.prototype，也没有找到，又通过Parent.prototype.__proto__找到了上一层原型对象Object.prototype。在这一层找到了toString方法。返回该方法供p1使用。
当然如果找到Object.prototype上也没找到，就在Object.prototype.__proto__中寻找，但是Object.prototype.__proto__ === null所以就返回undefined。这就是为什么当访问对象中一个不存在的属性时，返回undefined了。

3.constructor属性
constructor属性是让“徒弟”、“徒孙” 们知道是谁创造了自己，这里可不是“师父”啊
而是自己的父母，父母创造了自己，父母又是由上一辈人创造的，……追溯到头就是Function() 【女娲】。

![image](https://github.com/zhuxchong/js-prototype/blob/main/4.png)


constructor是对象才有的属性，从图中看到它是从一个对象指向一个函数的。指向的函数就是该对象的构造函数。每个对象都有构造函数，好比我们上面的代码p1就是一个对象，那p1的构造函数是谁呢？我们打印一下。

console.log(p1.constructor); // ƒ Parent(){}
通过输出结果看到，很显然是Parent函数。我们有说过函数也是对象，那Parent函数是不是也有构造函数呢？显然是有的。再次打印下。

console.log(Parent.constructor); // ƒ Function() { [native code] }
通过输出看到Parent函数的构造函数是Function()，这点也不奇怪，因为我们每次定义函数其实都是调用了new Function()，下面两种效果是一样的。

var fn1 = new Function('msg','alert(msg)');
function fn1(msg){
    alert(msg);
}
那么我们再回来看下，再次打印Function.constructor

console.log(Function.constructor); // ƒ Function() { [native code] }
可以看到Function函数的构造函数就是本身了，那我们也就可以说Function是所有函数的根构造函数。
到这里我们已经对constructor属性有了一个初步的认识，它的作用是从一个对象指向一个函数，这个函数就是该对象的构造函数。通过栗子我们可以看到，p1的constructor属性指向了Parent，那么Parent就是p1的构造函数。同样Parent的constructor属性指向了Function，那么Function就是Parent的构造函数，然后又验证了Function就是根构造函数。

总结
看到这我相信大家已经对原型和原型链有了一定的理解了，还没有特别理解的同学也不要担心，原型原型链以及闭包都是js中最难的几部分，需要一定的技术积累和时间沉淀。每天打开文章看一遍，自己画一遍这个原型链的图，因为其中有些原理是说不出来的，得靠自己去体会理解。不积跬步无以至千里不积小流无以成江海。
