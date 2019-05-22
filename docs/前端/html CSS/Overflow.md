## Overflow

### 基本属性

visible   尺寸超出显示

hidden  超出部分隐藏

scroll     超出产生滚动条

auto      自动分配

inherit  

**overflow-x:hidden    overflow-y:hidden**  

 **如果overflow-x overflow-y相同则等同于overflow  如果overflow-x overflow-y值不同且其中一个属性的值被赋予visible 另一个被赋予hidden scroll auto 那么visible 会被重置为auto** 

如何才能让overflow属性起作用：

1. 非display：inline
2. 对应方位的尺寸限制。width/height/max-width/max-height/absolute

overflow：visible妙用

ie7浏览器下，文字越多，按钮两侧的padding留白就越大

给所用按钮添加css样式overflow：visible 即恢复正常

### overflow与滚动条

**滚动条出现的条件：**

1. overflow：auto/overflow：scroll
2. 尺寸超出

***无论什么浏览器，默认滚动条来自HTML标签而不是body***

body/html与滚动条-JS与滚动高度

- chrome浏览器是：document.body.scrollTop
- 其他浏览器：document.documentElement.scrollTop;
- 目前两者不会同时存在，因此大家流传这种写法：

var st=document.body.scrollTop||document.documentElement.scrollTop;

overflow的padding-bottom缺失现象:

导致：不同的scrollHeight（元素内容的高度）

滚动条的宽度机制：

滚动条会占用容器的可用宽度或高度

结果：ie7+/chrome/firefox——17px

修复水平居中跳动问题：

html{overflow-y : scroll ; }

.container{ paddeing-left : calc(100vw-100%)；} 100vw-浏览器宽度；100%-可用内容宽度

### overflow与BFC

#### BFC:块级格式上下文

