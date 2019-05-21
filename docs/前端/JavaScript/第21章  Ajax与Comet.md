# 第21章  Ajax与Comet

**Ajax：**能够向服务器请求额外的数据而无需卸载页面。技术核心是**XMLHttpRequest**(XHR),在XHR出现之前，Ajax式的通信必须通过一些hack手段来实现，大多数是隐藏的框架或内嵌框架。

虽然名字中有XML但是Ajax通信与数据格式无关；这种技术就是可以无需刷新页面就可从服务器取得数据，但不一定是XML数据。

**XHR的特性**：

1. XHR为向服务器发送请求和解析服务器响应提供了流畅的接口。
2. 能够以异步的方式从服务器取得更多信息，意味着用户单击后，不必刷新页面也能够取得新数据，也就是说可以通过XHR对象取得新数据，然后再通过DOM将新数据插入到页面中。
3. XHR将浏览器原生的通信能力提供给了开发人员，简化了同样实现操作的任务。

## 21.1   XMLHttpRequest对象

### 21.1.1   XHR的创建

创建一个函数，此函数会根据IE中可用的MSXML库的情况创建最新版本的XHR对象：

~~~javascript
function createXHR(){
    //注释地方加上则会支持IE的早期版本，对原生XHR对象的支持
    //if(typeof XMLHttpRequest != "undefined"){
      //  return new XMLHttpRequest();
   // }else if(typeof ActiveXObject != "undefined"){
    if(typeof arguments.callee.activeXString!="string"){
        var versions=["MSXML2.XMLHttp.6.0","MSXML2.XMLHttp.3.0",:"MSXML2.XMLHttp"],
            i,len;
        for(i=0,len=version.length;i<len;i++){
            try{
                new ActiveXObject(versions[i]);
                arguments.callee.activeXString=versions[i];
                break;
            }catch(ex){
                //跳过
            }
        }
    }
    return new ActiveXObject(arguments.callee.activeXString);
   // }else{
    //    throw new Error("No XHR object available.");
   // }
}
var xhr = createXHR()//创建XHR对象
~~~

支持原生XHR的浏览器要创建XHR对象要使用XMLHttpRequest构造函数：

~~~javascript
var xhr=new XMLHttpRequest();
~~~

### 21.1.2   XHR的用法

#### open()——使用XHR调用的第一个方法：

open( 要发送的请求类型：get\post\ ... , 请求的URL，表示是否异步发送的布尔值)

~~~javascript
xhr.open("get","example.php",false);
~~~

***注意：***

1. 其中 URL：相对于执行代码的当前页面（也可使用绝对路径），只能向同一个域中使用相同端口和协议的URL发送请求，如果URL与启动请求的页面有任何差别，都会引发安全错误。
2. 调用open方法并不会真正发送请求，而只是启动一个请求以备发送。

#### 发送特定请求——send（）

send（作为请求主体发送的数据）：如果不需要通过请求主体发送数据，则必须传入null。对于某些浏览器来说这个参数是必须的。调用send之后，请求就会被分派到服务器。

##### 1.同步请求

如果请求是同步的，则JavaScript代码会等到服务器响应之后再继续执行。收到响应后，响应的数据会自动填充XHR对象的属性：

* responseText:作为响应主体被返回的文本。
* responseXML：若响应内容类型为“text/xml"或”application/xml"，此属性将保存包含响应数据的XML DOM文档。
* status：响应的HTTP状态；
* statusText:HTTP状态的说明。

![1558440477740](C:\Users\L\AppData\Local\Temp\1558440477740.png)

***注意：***

1. 决定下一步操作时，最好通过检测status，不要依赖statusText，因为它在跨浏览器使用时不太可靠。
2. 无论内容类型是什么，响应主体的内容都会保存到responseText属性中；而对于非XML数据而言，responseXML属性的值为null。

##### 2. 异步请求

检测XHR对象的readyState属性，该属性表示请求/响应过程的当前活动阶段，可取值：

* 0：未初始化。尚未调用send（）；
* 1：启动。已调用open()，还未调用send()；
* 2：发送。已调用send()，还未接收到响应；
* 3：接收。已经接收到部分响应数据；
* 4：完成。已经接收到全部响应数据，而且可以在客户端使用。

readyState属性的值发生变化，都会触发一次readystatechange事件，可以利用这个事件来检测每次状态变化后readystate的值。

在接收到响应之前还可以调用abort()来取消异步请求：

~~~javascript

~~~

在终止请求后，还应该对XHR对象进行解引用操作。由于内存原因，不建议重用XHR对象。