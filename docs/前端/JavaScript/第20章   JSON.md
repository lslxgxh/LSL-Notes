# 第20章   JSON

**JOSN——JavaScript Object Notation** 

## 20.1  语法

### 20.1.1  简单值

使用于JavaScript相同的语法，可以在JSON中表示字符串、数值、布尔值和null，但JSON不支持JavaScript中的undefined值。

JavaScript与JSON字符串最大的区别在于，JSON字符串必须用双引号（单引号会导致语法错误）；

### 20.1.2   对象

对象作为一种复杂数据类型，表示的是一组无序的键值对，而每个键值对中的值可以是简单值，也可以是复杂数据类型的值。

在JSON中的对象要求给属性加引号：

~~~javascript
//在JavaScript中的对象字面量法
var object={                 
    "name":"Nicholas",//name:"Nicholas",
    "age":29//age:29
}；
//JSON
{
    "name":"Nicholas"，
    "age":29
}
~~~

##### 与JavaScript对象字面量相比，JSON有两处不同：

1. JSON没有声明变量（JSON中没有变量的概念）；
2. 没有末尾分号

**对象的属性必须加双引号**。

属性的值可以是简单的也可以是复杂的，所以支持嵌套：

~~~javascript
{
    "name":"Nicholas",
    "age":29,
        "school":{
            "name":"xxx",
             "location":"xxx"
        }
}
//虽然有两个name属性，但是它们分别属于两个不同的对象，但是如果是同一个对象则绝对不可出现两个同名属性。
~~~

### 20.1.3   数组

JSON中的第二种复杂类型是数组。采用数组字面量法。

~~~javascript
var values=[25,"hi",true];//javascript
[25,"hi",true] //JSON
~~~

JSON数组同样没有变量和分号。

将数组与对象相结合：

~~~javascript
[
    {
    "title":"Professional JavaScript",
        "authors":[
            "Nicholas"
        ],//authors属性值是一个数组
        "edition":2,
        "year":2009
    }
]
~~~

对象和数组通常是JSON数据结构的最外层形式。

## 20.2  解析与序列化

### 20.2.1   JSON对象

##### eval():

早期的JSON解析器基本上是使用JavaScript的eval()函数，由于JSON是JavaScript语法的子集，因此eval()函数可以解析、解释并返回JavaScript对象和数组。对于不能原生支持JSON的浏览器，可以添加一个shim在浏览器中。

##### 方法1. stringify()

将JavaScript对象序列化为JSON字符串：

~~~javascript
var book={
    title:"profession JavaScript"
     authors:[
            "Nicholas"
        ],
        edition:2,
        year:2009
};
var jsonText=JSON.stringify(book);
~~~

使用JSON.stringify()把一个JavaScript对象序列化为一个JSON字符串。

默认情况下，JSON.stringify()输出的JSON字符串不包含任何空格字符或缩进。

在序列化JavaScript对象时，所有函数及原型成员都会被有意忽略，不体现在结果中，此外值为undefined的任何属性也都会被跳过。结果中最终都是值为有效JSON数据类型的实例属性。

##### 方法2.parse()

将JSON字符串直接传递给JSON.parse()就可以得到相应的JavaScript值。

~~~javascript
var bookcopy=JSON.parse(jsonText);
~~~

虽然book和bookcopy拥有同样的属性，单数他们是两个互相独立的对象。

如果传给JSON.parse()的字符串不是有效的JSON，该方法会抛出错误。

### 20.2.2   序列化选项

JSON.stringify()除了要序列化JavaScript对象外，还可以接收另两个参数，这两个参数用于指定以不同的方式序列化JavaScript对象。

第一个是**过滤器**，可以是一个数组，也可以是一个函数。

第二个是一个**选项**，表示是否在JSON字符串中保留缩进。

#### 1.过滤结果

#####   1.数组

如果过滤器是数组，那么JSON.stringify()的结果中将只包含数组中列出的属性：

~~~javascript
 var book={
    "title":"Professional JavaScript",
        "authors":[
            "Nicholas"
        ],
        "edition":2,
        "year":2009
    }；
 var jsonText=JSON.stringify(book，["title","edition"]);
~~~

JSON.stringify()的第二个参数是一个数组包含"title","edition"两个字符串，其中这两个属性与将要序列化的对象中的属性是相对应的，因此在返回的结果中就将只包含这两个属性：

~~~JavaScript
{ "title":"Professional JavaScript","edition":2}
~~~

#####   2.函数

第二个参数是函数，则传入的函数接受两个参数——属性（键）名和属性值。根据属性名可以知道该如何处理要序列化的对象中的属性，属性名只能是字符串，***而在值并非键值对结构的值时*，键名可以是空字符串。**

**如果函数返回了undefined，那么相应的属性会被忽略。**

~~~JavaScript
 var book={
    "title":"Professional JavaScript",
        "authors":[
            "Nicholas"
        ],
        "edition":2,
        "year":2009
    }；
    var jsonText=JSON.stringify(book，function(key,value){
                                swithc(key){
        case"authors"://authors属性值以，隔开
        return value.join(",")
        case "year"://year属性返回5000
        return 5000;
        case "edition"://"edition"属性被删除
        return undefined;
        default:
        return value;
    }
                                });
~~~

**一定要提供default项，此时返回传入的值，以便其他值都能正常出现在结果中。**

实际上第一次调用这个函数过滤器，传入的键是一个空字符串，而值就是book对象。

要序列化的对象中的每一个对象都要经过过滤器，因此数组中的每个带有这些属性的对象经过过滤器之后，每个对象都只会包含 "title"，"authors"，"year"属性。

#### 2.字符串缩进

##### 1.数值

JSON.stringify()的第三个参数用于控制结果中的缩进和空白符。如果该参数是一个数值，那么他代表每个级别要缩进的空格数。

~~~JavaScript
 var jsonText=JSON.stringify(book，null,4);//缩进四个空格
~~~

只要传入有效的控制缩进的参数值，结果字符串中就会包含换行符。

最大缩进格数为10，所有大于10的值都会自动转换成10。

##### 2.字符串

如果缩进参数是一个字符串，则这个字符串将在JSON字符串中被用作缩进字符（不再使用空格）。

~~~JavaScript
 var jsonText=JSON.stringify(book，null," - - ");
~~~

缩进字符串长度不能超过10个字符长。超过10个结果字符串中只会出现前10个字符。

#### 3.toJSON()方法

可以为任何对象添加toJSON()方法：

~~~JavaScript
 var book={
    "title":"Professional JavaScript",
        "authors":[
            "Nicholas"
        ],
        "edition":2,
        "year":2009
     toJSON:function(){
     return this.title;//返回title
 }
    }；
     var jsonText=JSON.stringify(book);
~~~

此时title对象也将被序列化为一个简单的字符串而非对象。可以让toJSON()方法返回任何值，它都能正常工作。

如果让这个方法返回undefined，此时如果包含它的对象嵌入在另一个对象中，会导致它的值变成null，而如果它是顶级对象，则结果就是undefined。

##### 序列化对象的顺序：

1. 如果存在toJSON()方法，且能通过它取得有效值，则调用该方法。否则返回对象本身。
2. 如果提供了第二个参数，应用这个函数过滤器，传入函数过滤器的值是**第一步返回的值**。
3. 对第二部返回的每个值进行相应的序列化。
4. 如果提供第三个参数，执行相应的格式化。

### 20.2.3   解析选项

JSON.parse()也可以接收另一个参数，该参数是一个函数。

如果还原函数返回undefined，则表示要从结果中删除相应的键；如果返回其他值，则将该值插入到结果中。

~~~javascript
var book={
    "title":"Professional JavaScript",
        "authors":[
            "Nicholas"
        ],
        "edition":2,
        "year":2009，
    releaseData:new Data(2011,11,1)//新增releaseData属性，保存Data对象。
};
   var jsonText=JSON.stringify(book);//序列化，变为有效JSON字符串
var bookcopy=JSON.parse(jsonText,function(key,value){//解析还原威威Data对象
    if(key==="releaseData"){
        return new Data(value);
    //还原函数在遇到releaseData键时会基于相应的值创建一个新的Data对象
    }else{
        return value;
    }
});
alert(bookcopy.releaseData.getFullYear())
//bookcopy.releaseData属性中保存了一个Data对象，所以可以调用getFullYear()。
~~~

