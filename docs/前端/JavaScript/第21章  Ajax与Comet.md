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
xhr.abort();
~~~

在终止请求后，还应该对XHR对象进行解引用操作。由于内存原因，不建议重用XHR对象。

### 21.1.3  HTTP头部信息

在发送XHR请求的同时，还会发生下列头部信息：

* Accept：浏览器能够处理的内容类型；
* Accept-Charset：浏览器能够显示的字符集；
* Accept-Encoding：浏览器能够处理的压缩代码；
* Accept-Language：浏览器当前设置的语言；
* Connection：浏览器与服务器之间连接的类型；
* Cookie：当前页面设置的任何cookie；
* Host：发送请求的页面所在的域；
* Referer：发送请求的页面URI；
* User-Agent：浏览器的用户代理字符串；

##### setRequestHeader(头部字段名，头部字段值)——可以设定自定义请求头部；

open()→setRequestHeader()→send()——成功发送请求头部信息；

~~~javascript
var xhr=createXHR();//创建xhr对象
xhr.onreadystatechange=function(){//检查状态码
    //判断异步请求中是否已经完成
    if(xhr.readyState==4){
        //判断状态码是否为200类或304
        if((xhr.status>=200&&xhr.status<=300)||xhr.status==304){
           //输出响应主体内容
            alert(xhr.responseText);
        }else{
            alert("Response was unscccessful :"+ xhr.status);
        }
    }
};
xhr.open("get","example.php",true);
xhr.setRequestHeader("My Header","My Values");
xhr.send(null);
~~~

xhr.getResponseHeader(头部字段名称)：可以取得相应的头部信息；

xhr.getAllResponseHeader()：可以取得一个包含所有头部信息的长字符串；

在服务器端，可以利用头部信息向浏览器发送额外的、结构化的数据。

### 21.1.4   GET请求

**GET：**最常用于向服务器查询某些信息；可以将查询字符串参数追加到URL的末尾，以便将信息发送给服务器。

查询字符串中每个参数的名称和值都必须使用encodeURIComponent()进行编码，并且所有的名-值对都必须由&隔开：

~~~javascript
xhr.open("get","example.php?name1=values1&name2=values2",true);
//辅助向现有的URL的末尾添加查询字符串参数
function addURLParam(url,name,value){
    //判断url是否含有字符串参数，若有则添加&，若无则添加？
    url+=(url.indexOf(?)==-1? "?":"&");
    //连接名-值对
    url+=encodeURICompoent(name)+"="+encodeURICompoent(value);
    return url;
}
~~~

### 21.1.5   POST请求

**POST：**通常用于向服务器发送应该被保存的数据。POST请求应该把**数据**作为请求的主体提交。POST请求的主体可以包括很多数据，且格式不限。

**发送POST请求步骤：**

1. 初始化POST请求：xhr.open("post","example.php",true);
2. 向send()中传入一些数据。（可以在此传入XML DOM文档，传入的文档经过序列化后作为请求主体被提交到服务器。）

##### 使用xhr来模仿表单提交：

1. 将Content-Type的头部信息设置为application/x-www-form-urlencoded。
2. 以适当的格式创建一个字符串。

POST的数据格式与查询字符串格式相同。如果需要将页面中表单的数据进行序列化，然后再通过XHR发送到服务器，可以使用serialize()函数来创建字符串；

如果不设置Content-Type头部信息，那么发送信息就不会出现在$_POST超级全局变量中，而要借助

 $HTTP_RAW_POST_DATA才能访问同样的数据。

***与GET请求相比，POST请求消耗的资源会更多一点，从性能方面来看，以发送相同的数据计算，GET请求的速度最多可以达到POST请求的两倍***

## 21.2   XMLHttpRequest 2级

### 21.2.1  FormData

##### FormData:

为序列化表单以及创建与表单格式相同的数据（用于通过XHR传输）提供了便利。

~~~javascript
var data=new FormData();
data.append("name","Nicholas");
//append(表单字段的名字（键），字段中的值（值）)
//也可用表单元素的数据预先向其中填入键值对
var data=new FormData(document.forms[0]);
//创建完FormData的实例后，可以直接传给XHR的send方法
                       ·······
   var form=document.getElementById("user-info");
   xhr.send(new FormData(form);
~~~

使用FormData的方便之处在于不必明确的在XHR对象上设置请求头部。XHR对象能够识别传入的数据类型是FormData的实例，并配置相对应的头部信息。

### 21.2.2   超时设定

##### timeout：

  表示请求在多少毫秒之后就会终止。在给timeout设置一个时间后，如果在规定时间内没有接收到相应，则会触发timeout事件，进而触发**ontimeout**事件处理程序：

~~~javascript
var xhr=createXHR();//创建xhr对象
xhr.onreadystatechange=function(){//检查状态码
    //判断异步请求中是否已经完成
    if(xhr.readyState==4){
        //避免浏览器报错，将status封装在try-catch语句中
        try{
        //判断状态码是否为200类或304
        if((xhr.status>=200&&xhr.status<=300)||xhr.status==304){
           //输出响应主体内容
            alert(xhr.responseText);
        }else{
            alert("Response was unscccessful :"+ xhr.status);
        }
        }catch(ex){
            //假设由ontimeout事件处理程序处理
        }
    }
};
xhr.open("get","example.php",true);
xhr.timeout=1000//如果在1s内没有返回就会自动终止，调用ontimeout
xhr.ontimeout=function(){
    //在调用ontimeout时，readyState可能已经变为4，可是如果在超时终止后再访问status属性就会导致错误
    alert("Request did not return in a second");
};
xhr.send(null);
~~~

### 21.2.3   overrideMimeType()

用于重写XHR响应的MIME类型。

例如：服务器返回的MIME类型时text/plain，但数据中实际包含的是XML。根据MIME类型，即使数据是XML，但在responseXML中仍然是null；所以通过调用overrideMimeType()。可以保证把响应当作XML而非纯文本来处理。

~~~javascript
var xhr=createXHR();
xhr.open("get","example.php",true);
xhr.overrideMimeType("text/xml");//强迫XHR对象将响应当作XML而非纯文本来处理。
//调用overrideMimeType必须在send()之前，才能保证重写相应的MIME类型。
xhr.send(null);
~~~

## 21.3   进度事件

##### Progress Events 6个进度事件：

* loadstart：在接收到相应数据的第一个字节时触发。
* progress：在接收响应期间持续不断的触发；
* error：在请求发生错误时触发；
* abort：在因为调用abort()方法而终止连接时触发。
* load：在接收到完整的响应数据时触发。
* loadend：在通信完成或者触发error、abort、load事件后触发。

每个请求都从loadstart开始——>一个或多个progress事件——>触发error、abort或load事件中的一个——>最后触发loadend结束。

### 21.3.1   load事件

load事件是用以代替readystatechange事件出现。响应接收完毕后将触发load事件，因此也没有必要去检查readystate属性。

而onload事件处理器会接收到一个event对象，其target属性就指向XHR对象实例，因而可以访问到XHR对象的所有方法和属性。

只要浏览器接收到服务器的响应，不管其状态如何，都会触发load事件，而这意味着你必须要检查status属性，才能确定数据是否真的可用。

### 21.3.2   progress事件

这个事件会在浏览器接收新数据期间周期性的触发。**而onprogress**事件处理器会接收到一个event对象，其target属性时XHR对象。

三个额外的属性：

1. lengthComputable：表示进度信息是否可用的布尔值；
2. position：表示已经接收的字节数；
3. totalSize：表示根据Content-Length响应头部确定的预期字节数；

进度指示器：

~~~javascript
xhr.onprogress=function(event){
    var divStatus =document.getElementById("status");
    if(event.lengthComputable){
        divStatus.innerHTML = "Received"+event.position+"of"+event.totalSize+"bytes";
    }
};
//为了保证正常执行，必须在调用open之前添加onprogress事件处理器。
~~~

## 21.4   跨域资源共享

##### CORS（cross-origin Resource Sharing, 跨域资源共享）

使用自定义的HTTP头部让浏览器与服务器进行沟通，从而决定请求或响应是应该成功还是失败。

例如：

GET/POST请求，无自定义头部，主体内容时text/plain。——>发送请求时，需要附加一个Origin头部，其中包含请求页面的源信息（协议、域名、端口）——>服务器根据这个端口来决定是否给予响应。——>服务器接受请求——>在Access-Control-Allow--Origin头部中回发相同的源信息（公共资源可以回发 “*”）——>如果没有头部或者源信息不匹配，浏览器就会驳回请求。

请求和响应都不包含cookie信息，。

~~~javascript
Origin：http://www.nnn.net
Access-Control-Allow--Origin:http://www.nnn.net
~~~

### 21.4.1   IE对CORS的实现

**XDR(XDomainRequest)与XHR不同点**：

1. cookie不会随请求发送，也不会随响应返回。
2. 只能设置请求头部信息中的Content-Type字段
3. 不能访问响应头部信息
4. 只支持GET和POST请求

这些变化使CSRF和XSS的问题得到了缓解。被请求的资源可以根据它认为合适的任意数据（用户代理、来源页面等）来决定是否设置Access-Control-Allow--Origin头部。作为请求的一部分，Origin头部的值表示请求的来源域，以便远程资源明确的识别XDR请求。

XDR的open对象只接受前两个参数，因为所有的XDR请求都是异步执行的。

请求返回后会触发load事件，响应的数据也会保存在responseText中。

在接收到响应后，只能访问响应的原始文本，没有办法确定响应的状态代码。而且只要响应了就会触发load事件，失败就会触发error事件且除了错误本身没有其他信息可用。要检测错误，需要指定一个onerror事件处理程序：

~~~javascript
var xdr= new XDomainRequest();
xdr.onload=function(){
    alert(xdr.responseText);
}
xdr.onerror=function(){
    alert("An error occurred");
};
xdr.open("get","url```");
//为了支持POST请求，XDR对象提供了ContentType属性，用来表示发送数据的格式。
//该属性使通过XDR对象影响头部信息的唯一方式。
xdr.ContentType="application/x-www-form-urlencode"
xdr.send(null);
//在请求返回前调用abort 可以终止请求
xdr.abort();
~~~

XDR同样支持timeout属性和ontimeout事件处理程序。

### 21.4.2   其他浏览器对CORS的实现

要请求另一个域中的资源，使用标准的XHR对象并在open()中传入绝对URL即可。

与IE中的XDR不同，通过跨域XHR对象可以访问status和statusText属性，并且还支持同步请求。对跨域XHR对象也有限制：

* 不能使用setRequestHeader()设置自定义头部
* 不能发送和使用cookie
* 调用getALllResponseHeader()方法总会返回空字符串

由于无论同源请求还是跨域请求都使用相同的接口，因此对于本地资源，建议使用相对URL，对于跨域资源使用绝对URL，这样做能消除歧义，避免出现限制访问头部或本地cookie信息等问题。

### 21.4.3   Preflighted Request

 Preflighted Request:透明服务器验证机制；支持开发人员使用

1. 自定义头部
2. GET/POST之外的方法
3. 不同的主体内容

在使用高级选项来发送请求的时候，会向服务器发送一个 Preflighted请求，这种请求使用OPTIONS方法；

浏览器向服务器发送下列头部：

* Origin：与简单请求相同
* Access-Control-Request-Methods：发送自身使用的方法
* Access-Control-Request-Headers：自定义头部，以逗号分隔多个头部

发送完请求后，服务器可以决定是否允许这类型的请求，服务器通过在响应中发送如下头部与浏览器进行沟通

* Access-Control-Allow-Origin：与简单请求相同
* Access-Control-Allow- Methods：允许的方法，多个以逗号隔开
* Access-Control-Allow- Headers：允许的头部，多个以逗号隔开
* Access-Control-Max-Age：应将此Preflighted 请求缓存多长时间（以秒表示）

Preflighted 请求结束后，结果将按照响应中制定的时间缓存起来。在第一次发送这种请求的时候会多一次HTTP请求。

### 21.4.4   带凭据的请求

默认情况下，跨域请求不提供凭据，通过将withCredentials属性设置为true，可以指定某个请求应该发送凭据，如果服务器接收带凭据的请求，则会返回一个HTTP头部来响应：

~~~javascript
Access-Control-Allow-Credentials:true;
~~~

如果服务器的响应中没有这个头部，那么浏览器就不会把响应提供给JavaScript（于是responseText中的内容为空字符串，status的值为0，并且会调用onerror(）事件处理程序；

服务器还可以在Preflighted 响应中发送这个HTTP头部，表示允许源发送带凭据的请求。

### 21.4.5   跨浏览器的CORS

要兼顾所有浏览器：

~~~javascript
function createCORSRequest(){
    var xhr=new XMLHttpRequest();
    //检测XHR是否支持CORS的虽简单方法，就是检测是否存在withCredentials属性； 
    if("withCredentials"in xhr){
        xhr.open(method,url,true);
        //检测XDomainRequest对象是否存在
    }else if(typeof XDomainRequest!="undefined")
       {
            xhr=new XDomainRequest();
            xhr.open(method,url);
        }else{
            xhr=null;
        }
        return xhr;
    
    var request=createCORSRequest("get","http://mmmm.com/page");
    if(request){
        request.onload=function(){
            //对request.requestText进行处理
        };
         request.send();
    }
}

~~~

两个对象共同的属性/方法：

* abort()：用汉语停止正在进行的请求；
* onerror：用于替代onreadystatechange检测错误；
* onload：用于替代onreadystatechange检测成功；
* responseText：用于取得响应内容。
* send()：用于发送请求

## 21.5   其他跨域技术

### 21.5.1   图像Ping

 图像Ping是与服务器进行简单、单向跨域通信的一种方式。

请求的数据是通过查询字符串发送的，而响应可以是任意内容，但通常使像素图或204响应。

通过 图像Ping，浏览器得不到任何具体的数据，但通过侦听load和error事件，能知道响应是何时接收到的。

 图像Ping最常用于跟踪用户点击页面和动态广告曝光次数。

缺点 ：

1. 只能发送GET请求
2. 无法访问服务器的响应文本。

所以 图像Ping只能用于浏览器与服务器之间的单向通信。

### 21.5.2   JSONP（JSON with padding）

**JSONP：**包含在函数调用中的JSON。

~~~javascript
callback（{"name":"Nicholas"})
~~~

##### 组成：

1. 回调函数：回调函数是当响应到来时应该在页面中调用的函数。回调函数的名字一般都是在请求中指定的
2. 数据：传入回调函数中的JSON数据

##### 使用：

* JSONP是通过动态<script>元素来使用的。这里的script元素与img元素类似，都有能力不受限制的从其他域中加载资源。
* JSONP是有效的JavaScript代码，所以在请求完成后，即在JSONP响应加载到页面中以后，就会立即执行。

##### 优点：

1. 与图像Ping相比，他能够直接访问响应文本。
2. 支持在浏览器和服务器之间双向通信。

##### 缺点：

1. JSONP是从其他域加载代码执行的，如果其他域不安全，很有可能会在响应中夹带一些恶意代码，而此时除了完全放弃JSONP调用之外，没有办法追究。因此在使用不是自己运维的web服务时，一定要确保安全可靠。
2. 不容易确定JSONP的请求是否失败。开发人员需要使用计时器检测指定时间内是否接收到响应。

### 21.5.3   Comet(服务器推送)

Ajax是一种从页面向服务器发送请求数据的技术，而Comet则是从服务器向页面推送数据的基数。

Comet可以让信息近乎实时的推送到页面上，非常适合处理体育比赛的分数和股票报价。

#### 实现Comet的方式：

##### 1.长轮询：

长轮询是传统轮询（短轮询）的一个翻版。短轮询即浏览器定时向服务器发送请求，看是否有需要更新的数据

长轮询则是页面发起一个到服务器的请求，然后服务器一直保持连接打开，直到有数据可以发送。在发送完数据后，浏览器关闭连接，随即又发起一个到服务器的新请求，这一过程在页面打开期间一直持续不断。，

无论长/短轮询，浏览器都要在接收数据之前，先发起对服务器的连接。长短轮询两者最大的区别，就在于服务器是如何发送数据：

* 短轮询是服务器立即发送响应，无论数据是否有效。
* 长轮询是等待发送响应

轮询的优势是所有的浏览器都支持，只要使用XHR的setTimeout()函数就可以实现，而我们只要确定何时发送请求即可。

##### 2. 流（HTTP流）

流在页面的整个生命周期内只使用一个HTTP连接。也就是说，浏览器向服务器发送一个请求，而服务器保持连接打开，然后周期性的向浏览器发送数据。

所有服务器端都支持打印到输出缓存然后刷新的功能。——实现HTTP流的关键

##### 流的实现过程：

通过侦听readystatechange事件以及检测readystate是否为3，就可以利用XHR的HTTP流。

当readystate为3时，responseText中就会保存接收到的所有数据，此时比较之前接收到的数据，决定从什么位置开始取得最新的数据。

~~~javascript
//progress接收数据时的调用，finished是关闭数据时的调用
function createStreamingClient(url,progress,finished){
    var xhr=new XMLHttpRequest();
    received=0;//记录处理的字符数
        xhr.open("get",url,true);
        xhr.onreadystatechange=function(){
            var result;
        if(xhr.readyState==3)
       {//只取得最新数据并调整计数器
           result=xhr.responseText.substring(received);//对responseText分割取得最新值
           received+=result.length;
           //处理新传入的数据
           progress(result);
        }else if(xhr.readyState==4){
            //传入响应返回的全部内容
            finished(xhr.responseText);
        }
      
    };
    xhr.send(null);
    return xhr;
}

~~~

### 21.5.4   服务器发送事件（SSE）

SSE API用于创建到服务器的单向连接，服务器通过这个连接可以发送任意数量的数据。

服务器响应的MIME类型必须是text/event-stream，而且时浏览器中的JavaScript API能解析格式输出。

SSE支持短轮询、长轮询和HTTP流，而且能够在断开连接时自动确定何时重新连接。

#### 1. SSE API

##### 1. 创建过程：

创建一个新的EventSource对象，并传进一个入口点：

~~~javascript
var source = new EventSource("myevents.php")
~~~

传入的URL必须与创建对象页面同源（相同的url模式，域与接口）。EventSource的实例有一个readyState属性：

* 0：正连接服务器
* 1：打开了连接
* 2：关闭了连接

还有以下三个属性：

1. open：在建立连接时触发
2. message：从服务器接收到新事件时触发
3. error：在无法建立连接时触发

服务器发回的数据以字符串形式保存在event.data中。如果想强制立即断开连接且不再重连，则可以用close（）方法。

#### 2.事件流

服务器事件会通过一个持久的HTTP响应发送，其MIME类型为text/event-stream。格式为纯文本。

1. 最简单的一个情况是每个数据前都有前缀data：

~~~javascript
data:foo
         //包含data的数据后有空行才会触发message事件
data:bar

data:foo
data:bar//属于一个事件“foo/nbar"
//对于多个连续以data开头的数据行，将作为多段数据解析，每个值之间以换行符隔开
~~~

2. 通过id：前缀可以给事件指定一个关联的ID，设置ID后，EventSource对象会跟踪上一次触发的事件。如果连接断开，会向服务器发送一个包含名为Last-Event-ID的特殊HTTP头部，以便服务器知道下一次该触发哪个事件。在多次连接的事件流中，这种机制可以确保浏览器以正确的顺序收到连接的数据段。  

### 21.5.5   Web Sockets

**Web Sockets**:目标是在一个单独的持久连接上提供双工、双向通信。在创建Web Sockets后，会有一个HTTP请求发送到浏览器以发起连接，在取得服务器连接后，建立的连接会使用从HTTP协议交换的Web Sockets协议。所以未加密连接变为：ws://；加密连接：wss://。

##### 使用自定义协议的好处：

1. 能够在客户端和服务器之间传送非常少量的数据，而不必担心HTTP那样字节级的开销。
2. 因为传递的数据包很小，所以比较适合移动应用

##### 使用自定义协议的缺点：

1. 制定协议的时间比制定JavaScript API的时间还要长
2. Web Sockets存在一致性和安全性的问题

#### 1. Web Sockets API

1. 实例一个WebSocket对象并传入要连接的URL：

   ~~~javascript
   var socket = new WebSocket("ws://...");
   //必须传入绝对URL，Web Sockets不适用同源策略
   ~~~

2. 浏览器尝试创建连接，Web Sockets表示当前状态的readyState属性：

   * WebSocket.OPENGING(0):正在建立连接；
   * WebSocket.OPEN(1):已经建立连接；
   * WebSocket.CLOSING(2):正在关闭连接；
   * WebSocket.CLOSE(3):已经关闭连接；

   readyState永远从0开始

3. 关闭Web Sockets连接，可以在任何时候调用close()，调用后readyState变为2然后变为3

#### 2.发送和接收数据

1. 向服务器发送数据，使用send()方法并传入任意字符串。
2. Web Sockets只能通过连接发送纯文本数据，所以对于复杂的数据结构，再通过连接发送之前必须进行序列化。
3. 服务器向客户端发来消息时，WebSocket对象就会触发message事件，返回的数据保存在event.data为字符串需要解析。

#### 3. 其他事件

WebSocket对象还有三个事件，在连接生命周期的不同阶段触发：

* open：成功连接时触发
* error：发生错误时触发，连接不能持续；
* close：连接关闭时触发；

WebSocket对象必须使用DOM 0级语法分别定义每个事件处理程序：

~~~javascript
socket.onclose=function(event){
    console.log("was clean"+event.wasClean+"code="+event.code+"reason="+event.reason);
};
~~~

close事件的event对象有三个额外的属性：

* wasClean:布尔值，表示连接是否明确关闭
* code：服务器返回的数值码；
* reason：字符串，包含服务器发回的信息。

### 21.5.6 SSE与Web Sockets

考虑该用 SSE与Web Sockets：

1. 是否有自由度建立和维护Web Sockets服务器。
2. 需不需要双向通信。组合XHR和SSE也可以实现双向通信

## 21.6   安全

##### **CSRF(跨站点请求伪造):**未被授权系统有权访问某个资源的情况。

确保通过XHR访问的URL安全，以下有几种方式：

* 要求以SSL连接来访问可以通过XHR请求的资源
* 要求每一次请求都要附带经过相应算法计算得到的验证码。

下列措施对防范CSRF无作用：

* 要求发送POST而不是GET——很容易改变
* 检查来源URL以确定是否可信——来源记录很容易伪造
* 基于cookie信息进行验证——同样容易伪造