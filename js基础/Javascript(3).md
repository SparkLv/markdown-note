# Javascript(3)

---

## BOM

### window对象

BOM对象的核心是window，他表示浏览器的一个实例。在浏览器中，window对象既是通过JS访问浏览器的一个接口，又是ECMA规定的Global对象，所以在网页中定义的任何一个对象、变量和函数。都以window作为Global对象。

#### 与全局定义变量的不同

与全局变量不同的是，在window对象上定义的变量可以使用delete删除，而全局变量不可以。

另外，尝试访问未定义的变量会抛出错误，而使用window访问，可以知道某个变量是否存在。

#### 窗口及框架

如果页面中包含框架，则每个框架都有自己的window对象，并且保存在frames集合中。在frames集合中，可以通过数值索引（从左至右，从上至下）或者框架名称来访问相应的window对象，每个对象都有一个name属性，其中包含框架的名称

最好使用top.frames[1]或者top.frames['name']来访问框架，而不是window.frames[1]或者window.frames['name']，因为在一个子框架之中想要访问其他框架，使用自身的window对象是访问不到的。

parent对象是访问当前框架的直接上层框架。

self属性始终指向window。

以上所有的属性都可通过window访问，即window.top window.parent。

另外，除非最高层框架是通过window.open()打开的，否则其window对象的name属性不会包含任何值。

#### 窗口大小

由于各个浏览器标准不同，无法获取准确的浏览器大小，但是可以获取视口大小

* innerWidth,innerHeight:可以获取视口大小，但是IE8及以下不支持

* document.documentElement.clientWidth,document.documentElement.clientHeight:也可以获取视口大小，但是混杂模式下不可用

* document.body.clientWidth,document.body.clientHeight:可以获取，但是是包含滚动内容的

#### 导航和打开窗口

window.open()方法既可以导航到一个特定的URL，也可以打开一个新的窗口。这个方法可以接受4个参数:

* 要加载的URL

* 窗口目标：如果传递了这个参数，则会在指定名称窗口或框架中加载第一个参数指定的URL，也可以是`"_self","_parent","_top","_blank"`

* 一个特性字符串：逗号分隔的字符串，属性名值间是=号。例如top,left,width,height,toolbar,scrollbars

* 表示新页面是否取代浏览器历史记录中当前加载页面的布尔值

这个方法可以返回一个指向新窗口的引用。

通过closed属性可以检测窗口是否关闭。

调用close()方法，可以关闭新打开的窗口。

对于top.close()可以使新窗口自己关闭自己，但是对于浏览器主窗口则不会关闭。

通过新窗口的window.opener可以指向调用window.open()的窗口或框架。如果将opener设置为null，即表示在单独的进程中运行新标签页，不需要彼此通信。一旦切断，将没有办法恢复

#### 超时调用和间歇调用

* 超时调用：使用setTimeout(function,time)
  * 注意：经过time毫秒后并不一定会立即执行，因为js是一个单线程解释器，因此一定时间内只能执行一段代码，所以过time毫秒后，会将function添加到任务队列中，如果不是空的，则会在前面的代码执行完后执行
  * 使用clearTimeout()可以取消
* 间歇调用：使用setInterval(function,time)
  * 同超时调用
  * clearInterval

最好使用超时调用模拟间歇调用。因为后一个间歇调用可能会在前一个间歇调用结束前启动，例如

```javascript
function fun(){
    setTimeout(fun,500);
}
setTimeout(fun,500);
```

#### 系统对话框

* alert():只接受一个字符串展现给用户
* confirm():只接受一个字符串用于展现给用户，返回一个布尔值
* prompt():接受两个字符串，一个是展示的字符串，二是输入文本域的默认值
* print():打印对话框

### location对象

location是一个特别的对象，它既是window对象的属性，也是document对象的属性

#### 属性

* hash：hash值
* host：服务器名和端口号
* hostname：服务器名
* href：完整URL
* pathname：目录或文件名
* port：端口号
* protocol：协议
* search：查询字符串

#### 位置操作

* assign()方法：改变浏览器位置，同样直接改变属性值亦可
* replace()方法：不会在浏览器历史记录中生成新的记录，因此无法后退返回
* reload()方法：重新加载当前显示的页面，如果不传递任何参数，并且页面没有改变，那么将从缓存中重新加载。如果传递一个true。那么，会从服务器中加载。

### navigator

用于检测显示网页的浏览器类型

在ie中检测插件使用new ActiveXObject('name')

其他浏览器可以使用navigator.plugins(返回一个数组，数组的对象有name等属性)

### history

这个对象保存着用户上网的历史记录。

方法：

* history.go(前进或退后页数的数字)
* history.back():退后一页
* history.forward():前进一页

## DOM1

### 节点层次

可以将任何HTML,XML文档描绘成一个由多层节点构成的结构。

文档节点是每个文档的根节点，文档节点只有一个根节点,即`<html>`元素，也称为文档元素，任何文档只能有一个文档元素（包括XML）

JS中所有节点类型都继承自Node类型，因此所有节点都共享着相同的基本属性和方法。

#### 类型

```javascript
Node.ELEMENT_NODE(1)
Node.ATTRIBUTE_NODE(2)
Node.TEXT_NODE(3)
Node.CDATA_SECTION_NODE(4)
Node.ENTITY_REFERENCE_NODE(5)
Node.ENTITY_NODE(6)
Node.PROCESSING_INSTRUCTION_NODE(7)
Node.COMMENT_NODE(8)
Node.DOCUMENT_NODE(9)
Node.DOCUMENT_TYPE_NODE(10)
Node.DOCUMENT_FRAGMENT_NODE(11)
Node.NOTATION_NODE(12)
```

可以使用nodeType属性获取类型，最好和数字比较，因为IE不兼容（没有公开node类型的构造函数）

#### nodeName和nodeValue

对于元素节点，nodeName为标签名，nodeValue是null

#### 节点关系

childNodes属性：保存着一个nodeList对象，是一个类数组对象，用于保存一组有序的节点。可以使用[]或item()访问

parentNode属性：指向文档树的父节点

previousSibling属性：前一个同辈节点，第一个节点为null

nextSibling属性：下一个同辈节点，最后一个为null

firstChild属性：指向第一个子节点，相当于childNodes[0]

lastChild属性：指向最后一个子节点，相当于childNodes[childNodes.length - 1]

hasChildNodeS()方法：在节点包含一个或着多个子节点的情况下返回true。

ownerDocument属性：指向文档节点。

#### 操作节点

* appendChild()：用于向childNodes结尾添加一个节点
* insertBefore()：接受两个参数，一是插入的节点，二是参照节点
* replaceChild()：接受两个参数，一个是插入的节点，二是被替换节点。返回替换节点
* removeChild()：接受一个参数，即被删除节点
  * 注意，一个文档不能有多个相同的节点，appendChild已存在节点，会变成移动节点
* cloneNode()：用于复制节点，如果参数是true会复制子节点
* normalize()：用于处理文本节点（不含文本和相邻文本节点）

#### Document类型

JS通过Document类型表示文档，document对象是HTMLDocument得一个实例，表示整个HTML页面。而且document对象是window对象得一个属性，因此可以将其作为全局对象来访问

* nodeType:9
* nodeName:#document
* nodeValue:null
* parentNode:null
* ownerDocument:null

子节点可能是一个DocumentType（最多一个）、Element（最多一个）、ProcessingInstruction或Comment

##### 文档子节点

document属性

* documentElment属性：代表文档元素
* body属性：指向body元素
* doctype属性：代表DocumentType类型，即`<!DOCTYPE>`（注意兼容性）

##### 文档信息

document属性

* title属性：代表title元素之间的文本
* URL属性：包含页面完整的URL链接
* domain属性：包含页面的域名
* referrer属性：保存着链接到当前页面的那个页面的URL，没有的话为null

以上属性只有domain可以重置，但是不能随意设置，如p2p.wrox.com只能重置为wrow.com，而且不能逆向。当domain相同，框架间就可以通信

##### 查找元素

* getElementById()方法：接受一个参数即要取得元素的id（区分大小写），但是ie8及以下不区分大小写。而且ie7及以下有一个怪癖，即name特性与给定id匹配的表单元素，也会被返回。

* getElementsByTagName()方法：接受一个参数，即要取得元素的标签名，返回的是包含0个或多个元素的NodeList。要想取得文档所有元素，可以传入"*"。
  * item()方法可以访问nodelist中的项。当然，使用[]也可以访问
  * namedItem()方法可以通过name特性取得nodeList集合中的项同样可以使用[]传入字符串访问name特性项
* getElementsByName()方法：可以获取所有name特性的元素。

##### 特殊集合

* document.anchors:包含文档中所有带name特性的a元素
* document.forms:包含文档中所有的form元素
* document.images:包含文档中所有的img元素
* document.link:包含文档中所有带href特性的a元素

###### 文档写入

* write():接受一个字符串参数，即要写入到输出流中的文本。
* writeln():接受一个字符串参数，即将要写入到输出流中的文本，在字符串末尾添加一个换行符(\n)。
* open()和close()可以用于打开和关闭网页的输出流

#### Element类型

* nodeType:1
* nodeName:标签名
* nodeValue:null
* parentNode:Document或者Element
* 子节点可能是Element,Text,Comment,ProcessingInstruction,CDATASection或EntityReference

也可以使用tagName属性获取标签名，返回的可能是一个大写的标签名字符串

##### HTML元素

所有HTML元素都由HTMLElement类型表示，不是直接通过这个类型也是通过这个类型更具体的子类型来表示。HTMLElement直接继承自Element并添加了一些属性，每个HTML元素都包含以下特性：

* id
* title
* lang
* dir
* className

##### 操作特性

* getAttribute():接受一个字符串参数即特性名，返回特性值（包括自定义特性），形式为字符串
* setAttribute():接受两个参数，一个是特性名，二是特性值（IE7兼容性有问题，尽量使用属性来设置特性）
* removeAttribute():接受一个参数即要删除的特性（IE8之前不支持）

要注意的是，对于自定义的属性要使用上述三种方法，属性形式无法获取自定义，对于非自定义属性，尽量用属性形式，因为两种方式表现所有不同
##### attributes特性

Element类型是使用attributes属性的唯一一个DOM节点类型，attributes属性中包含一个NamedNodeMap与NodeList类似。

* getNamedItem('name')：返回nodeName属性等于name的节点
* removeNamedItem('name')：从列表中移除nodeName等于name的节点
* setNamedItem(node)：向列表中添加节点，以节点的nodeName属性作为索引
* item(pos)：返回位于数字pos位置处的节点

这种方法最大的用处适用于遍历元素的特性

```js
function outputAtt(element){
  var pairs = new Array(),
      attrName,
      attrValue,
      i,
      len;

  for(i=0,len=element.atrributes.length;i<len;i++){
    attrName = element.attributes[i].nodeName;
    attrValue = element.atrributes[i].nodeValue;
    if(element.attributes[i].specified){
      //判断是否指定了相应特性，IE中有bug回返回所有特性
      pairs.push(attrName+':'+attrValue);
    }
  }
  return pairs.join("");
}
```

##### 创建元素

document.createElement(name):name为元素名

###### 元素的子节点

childNodes属性包含的它的所有子节点，为了弥补浏览器对于标签间文本节点的不同，可以使用nodeType来判断是否为1（即元素节点），再执行某些操作。

#### Text类型

* nodeType:3
* nodeName:'#text'
* nodeValue:所包含的文本
* parentNode:Element
* 无子节点

可以使用nodeValue或data属性来访问Text节点包含的文本

操作文本节点的方法

* appendData(text)：将text添加到节点的末尾
* deleteData(offset,count)：从offset指定的位置开始删除count个字符
* insertData(offset,text)：再offset指定的位置插入text
* replaceData(offset,count,text)：用text替换从offset指定位置到offset+count为止处的文本
* splitText(offset)：从offset指定的位置将当前文本节点分成两个文本节点
* substringData(offset,count)：提取从offset指定的位置开始到offset+count为止处的字符串

此外还有一个length属性，保存着节点中字符的数目。

##### 创建文本节点

document.createTextNode()：参数为插入节点的文本

##### 规范文本节点

可以在多个文本节点的父元素上调用normalize()方法，则会将所有文本节点合并成一个节点

### DOM操作技术

#### 动态脚本

第一种是url方式

```js
var script = document.createElement("script");
script.type = "text/javascript";
script.src = "x.js";
document.body.appendChild(script);
```

另一种方式是行内方式(由于IE不允许DOM访问其子节点等原因，所以代码如下)

```js
function loadScriptString(code){
  var script = document.createElement("script");
  script.type = "text/javascript";
  try{
    script.appendChild(document.createTextNode(code));
  } catch(ex){
    script.text = code;
  }
  document.body.appendChild(script)
}
```

但是这种方法没有什么意义，因为代码可以在js中直接执行，不需要写在script标签中

#### 动态样式

同动态脚本，但是行内样式用的是cssText而不是text

#### 操作表格

除了传统的方法，可以使用js提供的一些方法

* caption：保存着caption元素的指针
* tBodies：是一个tbody元素的HTMLCollection
* tFoot：保存着对tfoot元素的指针
* tHead：保存着对thead元素的指针
* rows：是一个表格中所有行的HTMLCollection
* createTHead()：创建thead元素将其放在表格中，返回引用
* createTFoot()：创建tfoot元素将其放在表格中，返回引用
* createCaption()：创建caption元素将其放在表格中，返回引用
* deleteTHead()：删除thead元素
* deleteTFoot()：删除tfoot元素
* deleteCaption()：删除caption元素
* rows：保存着tbody元素中的HTMLCollection
* deleteRow(pos)：删除指定位置的行
* insertRow(pos)：向rows集合中的指定位置插入一行
* cells：保存着tr元素中单元格HTMLCollection
* deleteCell(pos)：删除指定位置的单元格
* insertCell(pos)：向cells集合中的指定位置插入一个单元格，返回对插入单元格的引用

#### 使用NodeList

使用NodeList时应避免无限循环，应在for循环初始化变量的时候定义长度变量

## DOM扩展

### 选择符API

querySelector():接收一个CSS选择符，返回与该模式匹配的第一个元素，没有找到匹配元素，返回null
querySelectorAll():与querySelector方法类似，不同的是此方法返回的是一个NodeList实例

以上IE 8+

### 元素遍历

* childElementCount：返回子元素的个数
* firstElementChild：指向第一个子元素
* lastElementChild：指向最后一个子元素
* previousElementSibling：指向前一个同辈元素
* nextElementSibling：指向后一个同辈元素

以上方法会忽略空白文本节点，IE9+支持

### HTML5

#### 与类相关的扩充

1. getElementsByClassName()：返回带有指定类的所有元素的NodeList，传入多个类名的顺序不重要（ie9+支持）
2. classList：表示class的集合，可以使用item()方法或方括号语法(ie10+)
    * add(value)：将给定字符串值添加到列表中
    * contains(value)：表示列表是否存在给定值
    * remove(value)：从列表中删除给定的字符串
    * toggle(value)：如果已存在则删除，不存在则添加（ie不支持）

#### 焦点管理

* document.activeElement：会引用DOM中当前获得了焦点的元素，默认情况下，在文档刚刚加载完成时，document.activeElement中保存的是document.body元素的引用
* document.hasFocus()：用于确定文档是否获取了焦点，获取了返回true

#### HTMLDocument的变化

* readyState属性(ie4+)：判断文档加载状态，loading为正在加载文档，complete为已经加载完文档
* compatMode属性(ie6+)：混杂模式下值为BackCompat
* head属性(ie9+)：直接获取head引用，如果不支持，仍然使用getElementByTagName()方法
* 字符集：document.charset

#### 插入标记

相比创建dom操作，插入标记更简单，速度更快

* innerHTML(ie6-9不同标签的赋值会存在问题)
    * 读模式下：返回与调用元素的所有子节点对应的HTML标记（要注意的是，每种浏览器返回的值会不同）
    * 写模式下：会根据值创建新的DOM树，然后用这个DOM树完全替换调用元素原先的所有子节点
* outerHTML(ie4+)
    * 读模式下：返回调用它的元素及所有子节点的HTML标签
    * 写模式下：会根据指定的HTML字符串创建新的DOM子树，然后用这个DOM子树完全替换调用元素
* insertAdjacentHTML()：接收两个参数，插入位置和要插入的HTML文本
    * 第一个参数必须是下列值之一
        * beforebegin：在当前元素之前插入一个紧邻的同辈元素
        * afterbegin：在当前元素之下插入一个新的子元素或在第一个子元素之前再插入新的子元素
        * beforeend：在当前元素下插入一个新的子元素，或在最后一个子元素之后再插入新的子元素
        * afterend：再当前元素之后再插入一个紧邻的同辈元素

#### scrollIntoView()方法

使滚动条滚动到调用元素的顶部与视口顶部平齐的地方(为某个元素设置焦点也会产生相同的效果)

## DOM2和DOM3

### 样式

####访问元素样式

可以直接用style属性来访问样式（带-的样式属性需要转换成驼峰形式）


style对象有以下属性和方法

* cssText：能够访问到style特性中的CSS代码
* length：应用给元素的CSS属性的数量
* parentRule：表示CSS信息的CSSRule对象
* getPropertyCSSValue(propertyName)：返回给定属性值的CSSValue对象
* getPropertyValue(propertyName)：返回给定属性的字符串值
* item(index)：返回给定位置的CSS属性的名称
* removeProperty(propertyName)：从样式中删除给定属性
* setProperty(peropertyName,value,priority)：将给定属性设置为响应的值，并加上优先权标志

#### 操作样式表

通过document.styleSheets可以访问到文档全部的样式表，是list的形式，属性和方法如下

* disabled：表示样式表是否被禁用的布尔值，设置为true则禁用样式表
* href：如果样式表是通过link包含的，则是样式表的URL，否则，是null
* ownerNode：如果样式表是通过@import导入的，则属性值为null
* parentStyleSheet：在当前样式表是通过@import导入的情况下，这个属性是一个指向导入它的样式表的指针
* title：ownerNode中title属性的值
* type：表示样式表类型的字符串。就CSS样式表而言，这个字符串是type/css
* cssRules：样式表中包含的样式规则的集合，IE有一个rules属性
* ownerRule：如果样式表是通过@import导入的，这个属性就是一个指针，指向表示导入的规则，否则值为null
* deleteRule(index)：删除cssRules集合中指定的规则，IE用removeRule()方法
* insertRule(rule,index)：向cssRules集合中指定的位置插入rule字符串，ie用addRule()方法

CSS规则

* cssText：返回整条规则对应的文本（safari全部转换成小写，ie不支持）
* parentRule：如果当前规则是导入规则，这个属性引用的就是导入规则，否则为null（ie不支持）
* parentStyleSheet：当前规则所属的样式表（ie不支持）
* selectorText：返回当前规则的选择符文本
* style：一个CSSStyleDeclaration对象，可以通过它设置和取得规则中指定的样式值

### 元素大小

* 偏移量
    * offsetHeight：元素在垂直方向上占用的空间大小。包括元素的高度、水平滚动条的高度、上边框高度和下边框高度
    * offsetWidth：元素在水平方向上占用的空间大小。包括元素的高度、垂直滚动条的高度、左边框宽度和右边框宽度
    * offsetLeft：元素的左外边框至包含元素的左内边框之间的像素距离
    * offsetTop：元素的上外边框至包含元素的上内边框之间的像素距离

包含元素的引用保存在offsetParent属性中

* 客户区大小(确定视口大小ie7不支持document.documentElement所以要用document.body)
    * clientWidth：是元素内容区宽度加上左右内边距宽度
    * clientHeight：是内容区高度加上上下内边距高度
* 滚动大小
    * scrollHeight：在没有滚动条下，内容高度
    * scrollWidth：在没有滚动条下，内容宽度
    * scrollLeft：被隐藏在内容区左侧的像素数，可改变
    * scrollTop：被隐藏在内容区域上方的像素数，可改变

### 范围

* 创建范围：document.createRange()
* setStart：接受两个参数一个是参照节点，一个是偏移量值
* setEnd:同上
* deleteContents()：删除范围所包含的内容
* extractContents()：也会移除范围选区，但是会返回文档片段
* cloneContents()：创建范围对象的一个副本
* insertNode()：向范围选区的开始处插入一个节点

## ^_^未完待续，敬请期待！