<h1 align="center">Tiezn Studio 前端开发注意事项</h1>

由于三星的项目需要在不同机型下运行，为了兼容所有机型，我们会遇到各种各样的问题，如果您遇到了新的问题，并有解决的办法，请在文档中记录，谢谢。

### 开发准备
+ 三星官方API
	* API文档地址：[tizen-web-device-api](https://developer.samsung.com/smarttv/develop/api-references/tizen-web-device-api-references.html)
	- API引入方式：
		- 在项目的index.html中 引入 `<script type="text/javascript" src="$WEBAPIS/webapis/webapis.js"></script>`
		- 在项目的config.xml中 引入 `<tizen:privilege name="http://developer.samsung.com/privilege/productinfo"/>`
	- 使用方式：	`var duid = webapis.productinfo.getDuid();`（模拟器拿不到值，需要值可以在真机获取后使用）

### 开发问题
+ 变量赋值
	- 全局变量赋值请使用**var**，let赋值后在个别机型会出现undefined。
+ config.xml文件
	- 新建项目之后，请将旧项目的引入都复制过来，否则将使个别版本的播放器不能播放视频。
	- `<name>XXX</name>` 标签中的XXX可以使中文，这是APP的名字。
	- `<tizen:application id="XXXX.xxx" package="XXXX" required_version="2.3"/>` 这个标签是新建项目生成的唯一ID，之后上传到三星那边将作为唯一标识，如果出现差异会造成上传失败。
	- `<widget></widget>` 标签的属性**version**是APP的版本号，每次上传到三星之前（包括修改BUG之后的上传）请变更版本号。
+ cookie使用
	- cookie在模拟器不可使用，在个别型号真机也有问题，所以尽量使用localStorage。
+ 播放器
	- 如果将播放器元素赋值到全局变量后，离开页面请置空或清除该变量，否则会有别的页面后台运行该播放器。
	- 播放器绑定JS操作事件 如 onplaying 等，个别机型只有第一次执行。
	- 进度条需要自定义。
	- 播放器的父元素如果使用overflow:hidden,在个别机型会造成播放器黑屏。
+ getBoundingClientRect() 方法
	- 在个别机型中拿不到x,y的值，可以将拿到的left,top 赋值给 x,y 后使用




