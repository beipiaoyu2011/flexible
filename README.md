# flexible
(以下有些图和说明摘自一些地方，只是为了说明一些东西，如有冒犯十分抱歉)

曾几何时为了兼容IE低版本浏览器而头痛，以为到Mobile时代可以跟这些麻烦说拜拜。可没想到到了移动时代，为了处理各终端的适配而乱了手脚。对于混迹各社区的偶，时常发现大家拿手机淘宝的H5页面做讨论——手淘的H5页面是如何实现多终端的适配？

那么趁此Amfe阿里无线前端团队双11技术连载之际，用一个实战案例来告诉大家，手淘的H5页面是如何实现多终端适配的，希望这篇文章对大家在Mobile的世界中能过得更轻松。

**# 目标 #**

拿一个双11的Mobile页面来做案例，比如你实现一个类似下图的一个H5页面：

![](https://camo.githubusercontent.com/5ac077a46fc12456b71725f70cab904abcffb6e7/687474703a2f2f7777772e773363706c75732e636f6d2f73697465732f64656661756c742f66696c65732f626c6f67732f323031352f313531312f72656d2d312e6a7067)

**# 痛点 #**

虽然H5的页面与PC的Web页面相比简单了不少，但让我们头痛的事情是要想尽办法让页面能适配众多不同的终端设备。看看下图你就会知道，这是多么痛苦的一件事情：

![](https://camo.githubusercontent.com/9598a107e7f7029717f52192c90dcaf7008e49c1/687474703a2f2f7777772e773363706c75732e636f6d2f73697465732f64656661756c742f66696c65732f626c6f67732f323031352f313531312f72656d2d342e706e67)

再来看看手淘H5要适配的终端设备数据：

![](https://camo.githubusercontent.com/ab4450a21060ca291fc6b7ddc9592c94467d6bd6/687474703a2f2f7777772e773363706c75732e636f6d2f73697465732f64656661756c742f66696c65732f626c6f67732f323031352f313531312f72656d2d372e706e67)

手淘团队适配协作模式

早期移动端开发，对于终端设备适配问题只属于Android系列，只不过很多设计师常常忽略Android适配问题，只出一套iOS平台设计稿。但随着iPhone6，iPhone6+的出现，从此终端适配问题不再是Android系列了，也从这个时候让移动端适配全面进入到“杂屏”时代。

![](https://camo.githubusercontent.com/0a4ba5a67f639a004a85b371d51d33a737e0f5c8/687474703a2f2f7777772e773363706c75732e636f6d2f73697465732f64656661756c742f66696c65732f626c6f67732f323031352f313531312f72656d2d31312e706e67)

为了应对这多么的终端设备，设计师和前端开发之间又应该采用什么协作模式？或许大家对此也非常感兴趣。

而整个手淘设计师和前端开发的适配协作基本思路是：

选择一种尺寸作为设计和开发基准

定义一套适配规则，自动适配剩下的两种尺寸(其实不仅这两种，你懂的)

特殊适配效果给出设计效果

还是上一张图吧，因为一图胜过千言万语：

![](https://camo.githubusercontent.com/8e69ed933a0eff873d4a2b3667461d1e3ec2d790/687474703a2f2f7777772e773363706c75732e636f6d2f73697465732f64656661756c742f66696c65732f626c6f67732f323031352f313531312f72656d2d362e6a7067)

在此也不做更多的阐述。在手淘的设计师和前端开发协作过程中：手淘设计师常选择iPhone6作为基准设计尺寸，交付给前端的设计尺寸是按750px * 1334px为准(高度会随着内容多少而改变)。前端开发人员通过一套适配规则自动适配到其他的尺寸。

根据上面所说的，设计师给我们的设计图是一个750px * 1600px的页面：

![](https://camo.githubusercontent.com/9285fef8a588c2b233f99edacef5c9b3652c2a6e/687474703a2f2f7777772e773363706c75732e636f6d2f73697465732f64656661756c742f66696c65732f626c6f67732f323031352f313531312f72656d2d332e6a7067)

前端开发完成终端适配方案

拿到设计师给的设计图之后，剩下的事情是前端开发人员的事了。而手淘经过多年的摸索和实战，总结了一套移动端适配的方案——flexible方案。

这种方案具体在实际开发中如何使用，暂时先卖个关子，在继续详细的开发实施之前，我们要先了解一些基本概念。

一些基本概念

在进行具体实战之前，首先得了解下面这些基本概念(术语)：

视窗 viewport

简单的理解，viewport是严格等于浏览器的窗口。在桌面浏览器中，viewport就是浏览器窗口的宽度高度。但在移动端设备上就有点复杂。

移动端的viewport太窄，为了能更好为CSS布局服务，所以提供了两个viewport:虚拟的viewportvisualviewport和布局的viewportlayoutviewport。

George Cummins在Stack Overflow上对这两个基本概念做了详细的解释。

而事实上viewport是一个很复杂的知识点，上面的简单描述可能无法帮助你更好的理解viewport，而你又想对此做更深的了解，可以阅读PPK写的相关教程。

物理像素(physical pixel)

物理像素又被称为设备像素，他是显示设备中一个最微小的物理部件。每个像素可以根据操作系统设置自己的颜色和亮度。正是这些设备像素的微小距离欺骗了我们肉眼看到的图像效果。

设备独立像素(density-independent pixel)

设备独立像素也称为密度无关像素，可以认为是计算机坐标系统中的一个点，这个点代表一个可以由程序使用的虚拟像素(比如说CSS像素)，然后由相关系统转换为物理像素。

CSS像素

CSS像素是一个抽像的单位，主要使用在浏览器上，用来精确度量Web页面上的内容。一般情况之下，CSS像素称为与设备无关的像素(device-independent pixel)，简称DIPs。

屏幕密度

屏幕密度是指一个设备表面上存在的像素数量，它通常以每英寸有多少像素来计算(PPI)。

设备像素比(device pixel ratio)

设备像素比简称为dpr，其定义了物理像素和设备独立像素的对应关系。它的值可以按下面的公式计算得到：

设备像素比 ＝ 物理像素 / 设备独立像素
在JavaScript中，可以通过window.devicePixelRatio获取到当前设备的dpr。而在CSS中，可以通过-webkit-device-pixel-ratio，-webkit-min-device-pixel-ratio和 -webkit-max-device-pixel-ratio进行媒体查询，对不同dpr的设备，做一些样式适配(这里只针对webkit内核的浏览器和webview)。

dip或dp,（device independent pixels，设备独立像素）与屏幕密度有关。dip可以用来辅助区分视网膜设备还是非视网膜设备。

meta标签

<meta>标签有很多种，而这里要着重说的是viewport的meta标签，其主要用来告诉浏览器如何规范的渲染Web页面，而你则需要告诉它视窗有多大。在开发移动端页面，我们需要设置meta标签如下：

<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">

代码以显示网页的屏幕宽度定义了视窗宽度。网页的比例和最大比例被设置为100%。

留个悬念，因为后面的解决方案中需要重度依赖meta标签。

CSS单位rem

在W3C规范中是这样描述rem的:

font size of the root element.

简单的理解，rem就是相对于根元素<html>的font-size来做计算。而我们的方案中使用rem单位，是能轻易的根据<html>的font-size计算出元素的盒模型大小。而这个特色对我们来说是特别的有益处。

前端实现方案

了解了前面一些相关概念之后，接下来我们来看实际解决方案。在整个手淘团队，我们有一个名叫lib-flexible的库，而这个库就是用来解决H5页面终端适配的。

lib-flexible是什么？

lib-flexible是一个制作H5适配的开源库，可以点击这里下载相关文件，获取需要的JavaScript和CSS文件。

当然你可以直接使用阿里CDN：

<script src="http://g.tbcdn.cn/mtb/lib-flexible/{{version}}/??flexible_css.js,flexible.js"></script>

将代码中的{{version}}换成对应的版本号0.3.4。

使用方法

lib-flexible库的使用方法非常的简单，只需要在Web页面的<head></head>中添加对应的flexible_css.js,flexible.js文件：

第一种方法是将文件下载到你的项目中，然后通过相对路径添加:

<script src="build/flexible_css.debug.js"></script>

<script src="build/flexible.debug.js"></script>
或者直接加载阿里CDN的文件：

<script src="http://g.tbcdn.cn/mtb/lib-flexible/0.3.4/??flexible_css.js,flexible.js"></script>

另外强烈建议对JS做内联处理，在所有资源加载之前执行这个JS。执行这个JS后，会在<html>元素上增加一个data-dpr属性，以及一个font-size样式。JS会根据不同的设备添加不同的data-dpr值，比如说2或者3，同时会给html加上对应的font-size的值，比如说75px。

如此一来，页面中的元素，都可以通过rem单位来设置。他们会根据html元素的font-size值做相应的计算，从而实现屏幕的适配效果。

除此之外，在引入lib-flexible需要执行的JS之前，可以手动设置meta来控制dpr值，如：

<meta name="flexible" content="initial-dpr=2" />

其中initial-dpr会把dpr强制设置为给定的值。如果手动设置了dpr之后，不管设备是多少的dpr，都会强制认为其dpr是你设置的值。在此不建议手动强制设置dpr，因为在Flexible中，只对iOS设备进行dpr的判断，对于Android系列，始终认为其dpr为1。

	 
	 if (!dpr && !scale) {
	
	    var isAndroid = win.navigator.appVersion.match(/android/gi);
	
	    var isIPhone = win.navigator.appVersion.match(/iphone/gi);
	
	    var devicePixelRatio = win.devicePixelRatio;
	
	    if (isIPhone) {
	
	        // iOS下，对于2和3的屏，用2倍的方案，其余的用1倍方案
	
	        if (devicePixelRatio >= 3 && (!dpr || dpr >= 3)) { 
	               
	            dpr = 3;
	
	        } else if (devicePixelRatio >= 2 && (!dpr || dpr >= 2)){
	
	            dpr = 2;
	
	        } else {
	
	            dpr = 1;
	
	        }
	
	    } else {
	
	        // 其他设备下，仍旧使用1倍的方案
	
	        dpr = 1;
	    }
	
	    scale = 1 / dpr;
	}

flexible的实质

flexible实际上就是能过JS来动态改写meta标签，代码类似这样：
	
	var metaEl = doc.createElement('meta');
	
	var scale = isRetina ? 0.5:1;
	
	metaEl.setAttribute('name', 'viewport');
	
	metaEl.setAttribute('content', 'initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');
	
	if (docEl.firstElementChild) {
	
	    document.documentElement.firstElementChild.appendChild(metaEl);
	
	} else {
	    var wrap = doc.createElement('div');
	
	    wrap.appendChild(metaEl);
	
	    documen.write(wrap.innerHTML);
	
	}
事实上他做了这几样事情：

动态改写<meta>标签

给<html>元素添加data-dpr属性，并且动态改写data-dpr的值

给<html>元素添加font-size属性，并且动态改写font-size的值

案例实战

了解Flexible相关的知识之后，咱们回到文章开头。我们的目标是制作一个适配各终端的H5页面。别的不多说，动手才能丰衣足食。

把视觉稿中的px转换成rem

读到这里，大家应该都知道，我们接下来要做的事情，就是如何把视觉稿中的px转换成rem。在此花点时间解释一下。

首先，目前日常工作当中，视觉设计师给到前端开发人员手中的视觉稿尺寸一般是基于640px、750px以及1125px宽度为准。甚至为什么？大家应该懂的（考虑Retina屏）。

正如文章开头显示的示例设计稿，他就是一张以750px为基础设计的。那么问题来了，我们如何将设计稿中的各元素的px转换成rem。

目前Flexible会将视觉稿分成100份（主要为了以后能更好的兼容vh和vw），而每一份被称为一个单位a。同时1rem单位被认定为10a。针对我们这份视觉稿可以计算出：

1a   = 7.5px

1rem = 75px 

那么我们这个示例的稿子就分成了10a，也就是整个宽度为10rem，<html>对应的font-size为75px：


![](https://camo.githubusercontent.com/d55922d1d351bd879e258c00abde7d05f0b72c76/687474703a2f2f7777772e773363706c75732e636f6d2f73697465732f64656661756c742f66696c65732f626c6f67732f323031352f313531312f72656d2d322e6a7067)

这样一来，对于视觉稿上的元素尺寸换算，只需要原始的px值除以rem基准值即可。例如此例视觉稿中的图片，其尺寸是176px * 176px,转换成为2.346667rem * 2.346667rem。

PostCSS(px2rem)

工具px2rem。安装好px2rem之后，可以在项目中直接使用。也可以使用PostCSS。使用PostCSS插件postcss-px2rem：

	var gulp = require('gulp');
	
	var postcss = require('gulp-postcss');
	
	var px2rem = require('postcss-px2rem');
	
	gulp.task('default', function() {
	
	    var processors = [px2rem({remUnit: 75})];
	
	    return gulp.src('./src/*.css')
	
	        .pipe(postcss(processors))
	
	        .pipe(gulp.dest('./dest'));
	});

除了在Gulp中配置外，还可以使用其他的配置方式，详细的介绍可以点击这里进行了解。

配置完成之后，在实际使用时，你只要像下面这样使用：

	.selector {
	
	    width: 150px;
	
	    height: 64px; /*px*/
	
	    font-size: 28px; /*px*/
	
	    border: 1px solid #ddd; /*no*/
	}
上面的/*no*/和/*px*/ 标识 不需要变成rem的一定要加上 这样最终不会把px变成rem
px2rem处理之后将会变成：
	
	.selector {
	
	    width: 2rem;
	
	    border: 1px solid #ddd;
	
	}
	
	[data-dpr="1"] .selector {
	
	    height: 32px;
	
	    font-size: 14px;
	
	}
	
	[data-dpr="2"] .selector {
	
	    height: 64px;
	
	    font-size: 28px;
	
	}
	
	[data-dpr="3"] .selector {
	
	    height: 96px;
	
	    font-size: 42px;
	
	}

在整个开发中有了这些工具之后，完全不用担心px值转rem值影响开发效率。


----------

**最后**
我说一下我自己写的这个demo

一般 git clone 下来以后 npm install 安装一下组件

然后gulp运行一下

然后就可以在localhost:8000/html/demo.html 查看h5适配效果

下面是我的手机iphone 6s效果图

![](http://i.imgur.com/xbyLJn5.jpg)

----------

贴一下写的gulp任务
	
	var gulp = require('gulp');
	
	var postcss = require('gulp-postcss');
	
	var px2rem = require('postcss-px2rem');
	
	var connect = require('gulp-connect');
	
	//创建watch任务去检测html文件,其定义了当html改动之后，去调用一个Gulp的Task
	
	gulp.task('watch', function () {
	
	  gulp.watch(['./public/html/*.html'], ['html']);
	
	});
	
	//使用postcss px2rem 得到rem
	
	gulp.task('css', function() {
	
	    var processors = [px2rem({remUnit: 75})];
	
	    return gulp.src('./public/css/*.css')
	
	        .pipe(postcss(processors))
	
	        .pipe(gulp.dest('./public/dest'));
	
	});
	
	//使用connect启动一个Web服务器
	
	gulp.task('connect',function(){
	
		connect.server({
	
			root:'public',
	
			port:'8000',
	
			livereload: true
	
		})
	
	});
	
	//html任务
	
	gulp.task('html',function(){
	
		gulp.src('./public/html/*.html')
	
		.pipe(connect.reload());
	
	});
	
	//运行gulp 默认的Task
	
	gulp.task('default',['css','connect','watch'])

----------

然后贴一下package.json

	{
	  "name": "ss",
	
	  "version": "1.0.0",
	
	  "main": "gulpfile.js",
	
	  "scripts": {
	
	     "test": "echo \"Error: no test specified\" && exit 1"
	
	  },
	
	  "repository": {
	
		"type": "git",
	
		"url": "git@gitlab.gofund.cn:terminal_new/ggblog.git"
	
	  },
	  "author": "",
	
	  "license": "ISC",
	
	  "devDependencies": {
	
		"gulp": "^3.9.1",
	
		"gulp-connect": "^5.0.0",
	
		"gulp-postcss": "^6.1.1",
	
		"postcss-px2rem": "^0.3.0"
	
	  },
	
	  "description": ""
	
	}