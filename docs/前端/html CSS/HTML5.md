### 1.video

**Ogg     MPEG 4    WebM**

##### 标签属性

autoplay                视频在就绪后马上播放

controls                  显示控件（比如播放按钮）

loop                         媒介文件完成播放后再次开始播放

preload                   视频在页面加载时进行加载，并预备播放

video元素允许多个source元素。source元素可以链接不同的视频文件，浏览器将使用第一个可识别的格式；

### 2.audio

**Ogg Vorbis    Mp3    Wav**

autoplay                 音频在就绪后马上播放

controls                  显示控件（比如播放按钮）

loop                         媒介文件完成播放后再次开始播放

preload                   音频在页面加载时进行加载，并预备播放

### 3.拖放（Drag  drop)

设置元素可拖放：draggable=“true”；

拖动什么：ondragstart和setData（）；

放到何处：ondragover；

进行放置：ondrop；

### 4.Canvas

cavans元素用于在网页上绘制图形；

cavans元素本身没有绘图能力，所有的绘图工作都在js里完成；

步骤：

~~~javascript
<canvas id="myCanvas" width="200" height="100"></canvas>//创建cavans对象，并设置宽高
<script type="text/javascript">
var c=document.getElementById("myCanvas");//获取cavans
var cxt=c.getContext("2d");//创建context对象，getContext("2d")对象是内建的HTML5对象，拥有多种绘制路径、矩形、圆形、字符以及添加图像的方法；
cxt.fillStyle="#FF0000";//设置图像颜色
cxt.fillRect(0,0,150,75);//设置图像形状、位置、尺寸
//在画布上绘制150×75的矩形，从左上角（0，0）开始
</script>
~~~

##### 绘制矩形：

[`fillRect(x, y, width, height)`](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/fillRect)

绘制一个填充的矩形

[`strokeRect(x, y, width, height)`](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/strokeRect)

绘制一个矩形的边框

[`clearRect(x, y, width, height)`](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/clearRect)

清除指定矩形区域，让清除部分完全透明。

##### 绘制路径

1. 首先，你需要创建路径起始点。
2. 然后你使用[画图命令](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D#Paths)去画出路径。
3. 之后你把路径封闭。
4. 一旦路径生成，你就能通过描边或填充路径区域来渲染图形。

`beginPath()`

新建一条路径，生成之后，图形绘制命令被指向到路径上生成路径。

`closePath()`

闭合路径之后图形绘制命令又重新指向到上下文中。

`stroke()`

通过线条来绘制图形轮廓。

`fill()`

通过填充路径的内容区域生成实心的图形。

##### 移动笔触

moveTo( x , y )将笔触移动到制定的坐标x以及y上；

lineTo( x , y )绘制一条从当前位置到指定x以及y的直线；

### 5.SVG

* 指可伸缩矢量图形
* 用于定义用于网络的基于矢量的图形
* 使用XML格式定义图片
* 在放大或改变尺寸的情况下其图形质量不会有损失
* 万维网联盟的标准

##### 优势：

* 可通过文本编辑器来创建和修改；
* 可被搜索、索引、脚本化或压缩
* 可伸缩的
* 任何分辨率下都可被高质量的打印
* 在图像质量不下降的情况下被放大；



### Canvas  VS  SVG

##### SVG:

* 是一种使用XML描述2D图形的语言
* 基于XML，意味着SVG DOM中的每个元素都是可用的，可以为某个元素附加JS事件处理器。
* 每个被绘制的图形都被视为对象，如果SVG对象的属性发生变化，那么浏览器能够自动重现图像。

##### Canvas

* 通过JS来绘制2D图形
* 逐像素进行渲染的
* 一旦图形被绘制完成，就不会继续得到浏览器的关注，如果其位置发生变化，那么整个场景也需要重新进行绘制，包括任何或许已经被图像覆盖的对象。

#### vs

##### canvas

* 依赖分辨率
* 不支持事件处理器
* 弱的文本渲染能力
* 能够以.png/.jpg格式保存结果图像
* 最适合图像密集型的游戏，其中的许多对象会被频繁重绘

##### SVG

* 不依赖分辨率
* 支持事务处理器
* 最适合带有大型渲染区域的应用程序（谷歌地图）
* 复杂度高会减慢渲染速度（任何过度使用DOM的应用都不快）
* 不适合游戏应用

### 6.Web存储

##### localStorage——无时间限制的数据存储

##### sessionStorage——针对一个session的数据存储（当用户关闭浏览器窗口后，数据会被删除）

cookie不适合大量数据的存储



