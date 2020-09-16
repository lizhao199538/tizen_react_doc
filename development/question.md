<!-- 项目大标题 -->
<h1 align="center">开发常见问题</h1>
<!-- 文档说明 -->
开发中常见问题大家随时记录下来，以避免后期再次遇到同样的问题<br/>
😋💕🦄

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