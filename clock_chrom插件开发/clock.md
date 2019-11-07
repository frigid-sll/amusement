首先新建一个名为my_clock的文件夹，在此文件夹中新建一个名为manifest.json的文件，内容如下：

```json
{
    "manifest_version": 2,
    "name": "我的时钟",
    "version": "1.0",
    "description": "我的第一个Chrome扩展",
    "icons": {
        "16": "images/icon16.png",
        "48": "images/icon48.png",
        "128": "images/icon128.png"
    },
    "browser_action": {
        "default_icon": {
            "19": "images/icon19.png",
            "38": "images/icon38.png"
        },
        "default_title": "我的时钟",
        "default_popup": "popup.html"
    }
}
```

上面的字段有些我们可以一眼看出在定义什么，比如name定义了扩展的名称，version定义了扩展的版本，description定义了扩展的描述，icons定义了扩展相关图标文件的位置。version的值最多可以是由三个圆点分为四段的版本号，每段只能是数字，每段数字不能大于65535且不能以0开头（可以是0，但不可以是0123），版本号段左侧为高位，比如1.0.2.0版本比1.0.0.1版本更高。每次更新扩展时，新的版本号必须比之前的版本号高。

browser_action指定扩展的图标放在Chrome的工具栏中，browser_action中的default_icon属性定义了相应图标文件的位置，default_title定义了当用户鼠标悬停于扩展图标上所显示的文字，default_popup则定义了当用户单击扩展图标时所显示页面的文件位置。

接下来我们开始编写popup.html。

```xml
<html>
<head>
<style>
* {
    margin: 0;
    padding: 0;
}

body {
    width: 200px;
    height: 100px;
}

div {
    line-height: 100px;
    font-size: 42px;
    text-align: center;
}
</style>
</head>
<body>
<div id="clock_div"></div>
<script src="js/my_clock.js"></script>
</body>
</html>
```

如果你曾经编写过网页，会发现上面这个页面省略了很多内容，比如<title>标签。因为对于Chrome扩展来说，很多对网页有意义的内容是无意义的，所以我们可以只挑需要的写，当然你全写出来也不会有什么问题。

上面的这个页面首先定义了全局元素的margin和padding为0，这样我们可以更加自由地控制元素的外观。在编写网页时，body的尺寸往往不会专门给定，但对于Chrome扩展有时这是必要的，比如此例中我们需要告诉Chrome当用户单击扩展图标后展示一个多大的界面。

之后我们在body标签中定义了一个id为clock_div的div容器，用这个容器来显示当前的时间，这样我们就把HTML的布局写好了。接下来我们就需要引入JavaScript处理数据并动态显示了。值得注意的是Chrome不允许将JavaScript代码段直接内嵌入HTML文档，所以我们需要通过外部引入的方式引用JS文件。当然inline-script也是被禁止的，所以所有元素的事件都需要使用JavaScript代码进行绑定，如果你没有使用一个拥有强大选择器的库（如jQuery），最好给需要绑定事件的元素分配一个id以便进行操作。

下面来编写my_clock.js文件。

```jsx
function my_clock(el){
    var today=new Date();
    var h=today.getHours();
    var m=today.getMinutes();
    var s=today.getSeconds();
    m=m>=10?m:('0'+m);
    s=s>=10?s:('0'+s);
    el.innerHTML = h+":"+m+":"+s;
    setTimeout(function(){my_clock(el)}, 1000);
}

var clock_div = document.getElementById('clock_div');
my_clock(clock_div);
```

在my_clock.js文件中我们定义了一个my_clock函数用于显示时间，这个函数包含了一个el参数，这个参数为显示时间的容器，由于在HTML文档中我们设计在id为clock_div的div容器中显示时间，所以调用my_clock函数时我们传入了这个容器，在js文件中用变量clock_div表示。之后my_clock函数1000毫秒之后又会再次调用自身，这样clock_div中显示的时间就会被更新。

至此这个扩展就编写完毕了，当然别忘了将图标文件也放入相应的文件夹中。

![img](https:////upload-images.jianshu.io/upload_images/2926249-ec299230f1552acc.small?imageMogr2/auto-orient/strip|imageView2/2/w/700/format/webp)


*扩展的文件结构*

下面我们就需要将这个扩展载入Chrome中运行了。可以直接在地址栏中输入[chrome://extensions](https://link.jianshu.com?t=chrome://extensions)进入，勾选右上角的“开发者模式”，点击“加载已开发的扩展程序”，选择扩展所在的文件夹，就可以在浏览器工具栏中看到我们的扩展了。
 

![img](https:////upload-images.jianshu.io/upload_images/2926249-b22cd6b8f6e14675.png?imageMogr2/auto-orient/strip|imageView2/2/w/791/format/webp)


 当鼠标点击扩展图标后，一个显示时钟的界面就出现了。

![img](https:////upload-images.jianshu.io/upload_images/2926249-6125e7ab46570030.png?imageMogr2/auto-orient/strip|imageView2/2/w/316/format/webp)