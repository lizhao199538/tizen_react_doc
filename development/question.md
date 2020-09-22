<!-- 项目大标题 -->
<h1 align="center">开发常见问题</h1>
<!-- 文档说明 -->
开发中常见问题大家随时记录下来，以避免后期再次遇到同样的问题<br/>
😋💕🦄

### 开发准备
+ 三星官方API
	* API文档地址：[tizen-web-device-api](https://developer.samsung.com/smarttv/develop/api-references/tizen-web-device-api-references.html)
	- API引入方式：
		- 在项目的index.html中 引入 `<script type="text/javascript" src="$WEBAPIS/webapis/webapis.js"></script>`
		- 在项目的config.xml中 引入 `<tizen:privilege name="http://developer.samsung.com/privilege/productinfo"/>`
	- 使用方式：	`var duid = webapis.productinfo.getDuid();`（模拟器拿不到值，需要值可以在真机获取后使用）

## Tizen浏览器版本兼容问题
主要说明由于Tizen浏览器4.0和5.0之间的兼容性问题造成的不兼容问题
#### 1、getBoundingClientRect问题
div.getBoundingClientRect()可以获取
* Tizen4.0：width、height、top、right、bottom、left
* Tizen5.0：width、height、top、right、bottom、left、x、y

```javascript
let clientRect = currentRect.getBoundingClientRect();
if (clientRect.x === undefined || clientRect.x === '') {
    // 检测到没有x的情况下，将left、top赋值给x、y
    clientRect = {
        x: clientRect.left ? clientRect.left : currentRect.offsetLeft,
        y: clientRect.top ? clientRect.top : currentRect.offsetTop,
        width: clientRect.width ? clientRect.width : currentRect.clientWidth,
        height: clientRect.height ? clientRect.height : currentRect.clientHeight,
        top: clientRect.top ? clientRect.top : currentRect.offsetTop,
        left: clientRect.left ? clientRect.left : currentRect.offsetLeft,
        right: clientRect.right ? clientRect.right : currentRect.offsetLeft + currentRect.clientWidth,
        bottom: clientRect.bottom ? clientRect.bottom : currentRect.offsetTop + currentRect.clientHeight
    };
}
```
### 开发问题
+ 变量赋值
	- 全局变量赋值请使用**var**，let赋值后在个别机型会出现undefined。
+ config.xml文件
	- 新建项目之后，请将旧项目的引入都复制过来，否则将使个别机型的播放器不能播放视频。
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