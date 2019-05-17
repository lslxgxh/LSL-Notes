### this误解

##### 指向自身

~~~javascript
function foo(num) {
        console.log("foo:" + num);
        //记录foo被调用的次数
        this.count++
    };
    foo.count = 0;
    var i;
    for (i = 0; i < 10; i++){
        if(i>5){
            foo(i);
        }
    }
    console.log(foo.count)
~~~

![1550563636855](C:\Users\L\AppData\Local\Temp\1550563636855.png)

三种改正方法：

1. 利用词法作用域，var data={ count:0 };
2. 利用foo标识符，将this.count改为foo.count。
3. 强制this指向foo

~~~javascript
 function foo(num) {
        console.log("foo:" + num);
        //记录foo被调用的次数
        this.count++
    };
    foo.count = 0;
    var i;
    for (i = 0; i < 10; i++){
        if(i>5){
            //使用call（...)可以确保this指向函数对象foo本身
            foo.call(foo,i);
        }
    }
    console.log(foo.count)
~~~

##### 指向它的作用域

this在任何情况下都不指向函数的词法作用域。

~~~javascript
function foo(){
    var a=2;
    this.bar();
}
function bar(){
    console.log(this.a);
}
foo();//ReferenceError:a is not defined
~~~

这段代码试图利用this联通foo和bar之间的词法作用域，从而让bar能够访问foo函数中的a，这是不可能实现的。

##### this到底是什么

this是在**运行时进行绑定**的，并不是在编写时进行绑定的，它的上下文取决于函数调用时的各种条件。

this的绑定和函数声明的位置没有任何关系，只取决于**函数的调用方式**。

thi**s既不指向函数自身也不指向函数的词法作用域**。

### this的全面解析

**调用位置**：就是函数在代码中被调用的位置。（并非声明位置

~~~javascript
 function baz() {
        //当前调用栈是：baz
        //因此，当前的调用位置是全局作用域
        console.log("baz");
        bar();//<--bar调用位置
    }
    function bar() {
        //当前调用栈是：baz-->bar
        //因此，当前调用位置在baz中
        console.log("bar");
        this.foo();//<--foo的调用位置
    }
    function foo() {
        //当前调用栈是baz->bar->foo
        //因此当前调用位置在bar中
        console.log("foo")
    }
    baz();//<--baz的调用位置
~~~

#### 默认绑定

独立函数调用。（看作是无法应用其他规则时的默认规则

~~~javascript
 function foo(){
       console.log(this.a);
   }
   var a=2;
   foo();//2
~~~

当调用foo()时，this.a被解析成了全局变量a，在本例中函数调用时应用了this的默认绑定，因此this指向全局对象。

在代码中，foo()时直接使用不带任何修饰的函数引用进行调用的，因此只能使用默认绑定，无法应用其他规则。

#### 隐式绑定

隐式绑定规则会把函数调用中的this绑定在这个上下文对象

~~~javascript
function foo(){
        console.log(this.a);
    }
    var obj2={
        a:42,
        foo:foo
    };
    var obj1={
        a:2,
        obj2:obj2
    };
    obj1.obj2.foo();//42

~~~

对象属性引用链中只有上一层或者说最后一层在调用位置中起作用。

##### 隐式丢失

被隐式绑定的函数会丢失绑定对象，也就是说他会应用默认绑定，将this绑定到全局作用域或者undefined上，（取决于是否是严格模式

~~~javascript
  function foo(){
        console.log(this.a)
    };
    var obj={
    a:2,
    foo:foo
    }
    //此时的bar是一个不带任何修饰的函数调用
    var bar=obj.foo;//引用函数foo本身
    var a="oops";
    bar();//"oops"
~~~

#### 显式绑定

通过foo.call(...)，我们可以在调用foo时强制把它的this绑定到obj上。

~~~javascript
function foo(){
    console.log(this.a)
};
var obj={
    a:2
};
//强制将this绑定到foo上
foo.call(obj);//2
~~~

*装箱*：如果你传入了一个原始值（字符串类型、布尔类型或者数字类型）来当作this的绑定对象，z这个原始值会被转换成它的对象形式（也就是new String,new Boolean,new Number).

##### 硬绑定

~~~javascript
    function foo(){
        console.log(this.a)
    };
    var obj={
        a:2,
    }
    var bar=function(){
        foo.call(obj);
    };
    bar();//2
    setTimeout(bar,100)//2
    bar.call(window);//2
    //硬绑定的bar不能再修改他的this
~~~

我们创建了函数bar，并在他的内部手动调用了foo.call(obj)，因此强制把foo的this绑定到了obj。无论之后再怎么调用函数bar，它总会手动在obj上调用foo.这种绑定是一种显式的强制绑定，因此我们称之为硬绑定。

创建包裹函数：

~~~javascript
 function foo(something){
        console.log(this.a);
     return this.a+something;
    };
    var obj={
        a:2,
    }
    var bar=function(){
        foo.apply(obj,arguments);
    };
   var b= bar(3);//2 3
  console.log(b);//5
~~~

创建一个可以重复使用的辅助函数：

~~~javascript
 function foo(something){
        console.log(this.a);
     return this.a+something;
    };
    var obj={
        a:2,
    }
    var bar=function(){
        foo.apply(obj,arguments);
    };
   var b=bind(foo,obj)
   var b= bar(3);//2 3
  console.log(b);//5
~~~

提供了内置方法，Function.prototype.bind:

~~~javascript
function foo(something){
        console.log(this.a);
     return this.a+something;
    };
    var obj={
        a:2,
    }
   var bar=foo.bind(obj)
//bind函数会返回一个硬编码的新函数，它会把你指定的参数设置为this的上下文并调用原始函数
   var b= bar(3);//2 3
  console.log(b);//5
~~~

##### API调用的“上下文”

e.g:

~~~javascript
function foo(el){
    console.log(el,this.id);
}
var obj={
    id:"awesome"
};
//调用foo时把this绑定到obj
[1,2,3].forEach(foo,obj);
//1 awesome 2 awesome 3 awesome
~~~

#### new绑定

使用new来调用函数的时候，会自动执行下面的操作：

1. 创建一个全新的对象
2. 这个新对象会被执行[[prototype]]连接
3. 这个新对象会被绑定到函数调用的this
4. 如果函数没有返回其他对象，那么new变大时中的函数调用会自动返回这个新对象

~~~javascript
function foo(a){
    this.a=a;
}
var bar = new foo(2);
console.log(bar.a);//2
~~~

使用new来调用foo时，我们会构造一个新对象并把它绑定到foo调用中的this上来。

#### 优先级

**判断this:**

![1551065726631](C:\Users\L\AppData\Local\Temp\1551065726631.png)

