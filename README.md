<!-- 项目大标题 -->
<h1 align="center">艾数达 前端框架说明</h1>
<!-- 项目说明 -->
艾数达前端项目此后用react进行开发，在react基础上封装了一个脚手架，后期所有的项目可以在此技术上进行快速开发

此说明主要讲解二次封装的脚手架使用说明

## eslint+prettier规范与检查代码
公司前端代码规范，主要结束Eslint（默认设置）进行代码检查，配合使用prettier进行代码风格格式化

* Eslint规范说明：http://note.youdao.com/noteshare?id=6aa1e423122367e08c6db5328d586695

* vscode settings.json中设置prettier：[settings.json](./eslint/settings.json)

## 项目基础配置说明

#### 自定义环境配置
自定义环境配置，有助于开发者在本地开发、测试已经上线的时候，不必要关心环境参数是否配置错误，只要编译的命令写对就不会出现问题
###### 1. 根据教程安装相关插件以及建好本地配置，[点击查看](https://blog.csdn.net/ximenxiafeng/article/details/107309344)
###### 2. 根据配置进行本地或线上打包操作
* 本地开发：npm run start
* 测试环境：npm run test
* 正式环境：npm run prod

#### 本地代理以及web线上跨域设置
由于项目是在本地浏览器上开发，避免不了跨域问题，在本地开发和测试环境（web网站）上可以通过以下方式解决接口跨域问题
* 开发环境：package.json中配置"proxy": "https://api.xxx.cn/api/"
* 测试环境：如果项目要放到网站中用浏览器远程查看的话，需要服务端配置相关的跨域允许操作，不多说，看[相关文章](https://www.cnblogs.com/lixihuan/p/9620036.html)

#### 配置Tizen项目的结构说明
1. 用Tizen开发器新建一个空项目，将项目生成的配置文件copy到此脚手架的public文件夹下，以便编译的时候，自动将文件打包到build中
2. 在Tizen中导入项目，要将编译好的build文件夹导入
3. 项目的ID问题：每次要创建一个最新的空项目，将其中的id复制到当前项目中，不然三星平台会检测出软件重复

#### 项目接口说明????????

## 封装的项目内部架构说明
一下介绍的是基于react进行开发进行的相关封装的说明，主要包含路由跳转、路由传参、焦点转移等相关模块的说明

#### 路由配置说明
由于项目要做到翻页后，记住上一页的信息，所以这里的路由没有用react提供的路由，而是在App.js中，通过配合redux将所有的页面信息存储起来，然后通过App.js将页面一个个的分层级进行渲染展示出来
###### 1. 页面跳转，回退相关的路由操作方法
* 跳转到其他页面：
  目标页面获取传参的方法，直接通过 this.props.params
```javascript
this.props.pushRouter({
    name: 'singSong',         // 页面名称，在App.js中使用
    pageId: this.getRandom(), // 页面的随机数，路由站记录页面标识
    params: {
        artist: '123',        // 传入下一页的参数
    }
});
```

* 从其他页面回退：
  目标页面获取传参的方法，直接通过 this.props.backParams
```javascript
// 两个方法必须同时使用，返回上上页
this.props.deleteRouter(2, {artist: '123'});
this.props.deletePageDom(2);
// ---- 返回上页简写如下
this.props.deleteRouter();
this.props.deletePageDom();
```
###### 2. 增加一页新页面的方法
* 在App.js的render中，通过redux中的router获取所有路由站，循环渲染出来
* 每次渲染所带的参数如下：display、pageId、key、params、backParams
```javascript
{this.props.router.map((item, index) => {
    // 只有最后一层是显示的，以上所有层都是隐藏的
    let display = index < this.props.router.length - 1 ? 'none' : '';
    if (item.name === 'home') {
        <SelectSong
            display={display}            // 如果不是最顶层，是否要隐藏掉
            pageId={item.pageId}         // 页面的随机标识，一定不能少
            key={index}                  // map循环的key值
            params={item.params}         // 跳转到下一页面参数
            backParams={item.backParams} // 从下一页返回到上一页的参数
        ></SelectSong>
    }
})}
```

#### 页面焦点配置
每个页面属于一层路由页，此页面中的所有可点击的焦点都会收集放到一个数组（redux）中，根据数组中的焦点位置，我们可以操作页面中的焦点进行转移；
等进入下一个页面的时候，我们会立马收集最新页面的所有焦点，焦点转移逻辑只会移动最顶层的页面焦点。

###### 1. 页面创建以及焦点设置
* 在state中设置焦点数据(可以是数组或者对象，但必须有cursor项)
```javascript
//设置分类组件的随机标识
this.compId = this.getRandom();
this.state = {
    //数字键盘的数组
    numberKeyList: [
        {
            keyValue: 1,
            cursor: this.setCursorObj(this.props.pageId, this.compId, 'b', true)
        }
    ],
    getCodeKey: {
        // 获取验证码按钮
        getCodeButton: {
            cursor: this.setCursorObj(this.props.pageId, this.compId, 'b')
        }
    },
}
```
| cursor参数 | cursor说明 |
|--------|-----------|
| pageRandom | page页面的随机标识 |
| compRandom | 单个组件的随机标识 |
| level | 当前焦点的层级，一般以横行为一个层级，左右一定的时候不会串层级移动 |
| curr | 初始化的时候，是否设置为焦点，也可以等页面渲染出来再单独设置 |
| direction | 页面可以移动的方向，默认{ left: 'yes', right: 'yes', top: 'yes', bottom: 'yes' }<br/>每个参数：left = ['right','yes','no']、right = ['left','yes','no']、top = ['yes','no']、bottom = ['yes','no']<br/>yes：可以通行；no：不能移动；right：左移的时候，没有就会挑选当前焦点上一层的最右边的一个焦点；left：同right解释 |

* render中的div焦点渲染
```javascript
<div
    className={'button-box flex-ac' + (this.state.buttonList.searchButton.cursor.curr ? ' curr' : '')}
    ref={this.state.buttonList.searchButton.cursor.refs}
>
    ...
</div>
```
* componentDidMount完成之后，将焦点收集起来
```javascript
this.props.editeDomList([this.state.numberKeyList,this.state.getCodeKey]);
```
* componentDidMount完成之后，将焦点收集起来，收集起来后，再设置某个焦点
```javascript
this.props.setCursorDom(this.state.songClassList[0].cursor.random);
```

###### 2. 数据变更后的焦点重置
由于react中路由以及焦点是全局共用的，涉及到焦点切换等全局数据操作，几乎所有的组件都会受到更改通知，我们在componentDidUpdate周期中，将过滤掉没用的更改，将涉及到当前组件的更改捕获到，并重新删除当前组件的焦点重新收集

```javascript
// 更新完毕
componentDidUpdate(prevProps, prevState) {
    if (this.props.routerDomList[this.props.routerDomList.length - 1].pageId !== this.props.pageId) return;
    // 如果更新后的节点个数，和之前的不一样，就证明整个组件结构发生了变化
    if (prevState.songList.length !== this.state.songList.length) {
        //更新完毕后，删除dom节点
        this.props.deleteCompDom(this.compId);
        if (this.state.totalPage > 1) {
            // 增加dom节点
            this.props.editeDomList([this.state.songList, this.state.pageKeyBttton]);
        } else {
            // 没有分页的情况
            this.props.editeDomList([this.state.songList]);
        }
    } else {
        let boo = this.state.songList.some((item, index) => {
            if (item.songid !== prevState.songList[index].songid) {
                return true;
            } else {
                return false;
            }
        });
        if (boo) {
            //证明这是翻页了，开始重新设置焦点dom
            //更新完毕后，删除dom节点
            this.props.deleteCompDom(this.compId);
            if (this.state.totalPage > 1) {
                // 增加dom节点
                this.props.editeDomList([this.state.songList, this.state.pageKeyBttton]);
            } else {
                // 没有分页的情况
                this.props.editeDomList([this.state.songList]);
            }
        }
    }
}
```

###### 3. 焦点移动规则以及方法
一个页面的焦点收集完毕后（一个页面包含很多组件，所有组件的焦点都收集完），我们点击方向键来移动焦点，其实就是获取到当前焦点的位置后，通过计算来得知下一个焦点应该是哪个元素，具体使用方法如下
```javascript
// 判断是否要移动焦点的时候，因为焦点通过props传入，改变redux中的焦点数据，当前组件首先通过此周期得知，我们在此周期内通过shouldComponentCurrUpdate方法来判断是否要移动当前焦点的样式
componentWillReceiveProps(nextProps) {
    // 过滤掉当前组件不是最顶层的页面的组件的情况（有时候不能过滤，看具体需求）
    if (this.props.routerDomList[this.props.routerDomList.length - 1].pageId !== this.props.pageId) return;
    // 根据新的props判断，是否移动焦点，第三个参数是state中所有有焦点的元素
    shouldComponentCurrUpdate(nextProps, this, ['songList', 'songClassList', 'artistList']);
}
```
#### 监听相关的规格以及方法
页面中有不少监听事件，包括组件级的局部监听功能，页面整体的焦点移动、反野功能等
###### 1. 键盘移动的焦点监听方法
事件监听的添加与删除应当配套使用，因为有些组件会频繁的增加或者移除，如果不删除设置的监听事件，会造成额外的负担
```javascript
constructor(props) {
    super(props);
    // 一定要在这里将this绑定到事件监听方法上
    this.handleKeyDown = this.handleKeyDown.bind(this);
}
// 页面渲染完成之后
componentDidMount() {
    // 增加事件监听
    document.addEventListener('keydown', this.handleKeyDown);
}
// 组件将要卸载
componentWillUnmount() {
    // 卸载的时候，删除掉事件监听
    document.removeEventListener('keydown', this.handleKeyDown);
}
//监听键盘事件
handleKeyDown(e) {
    //判断如果当前页面不再第一个的时候，忽略点击事件
    if (this.props.routerDomList[this.props.routerDomList.length - 1].pageId !== this.props.pageId) return;
    //开始判断键盘逻辑
    if (e.keyCode === TvKeyCode.KEY_ENTER) {
        // ...
    }
}
```

## 封装的插件，以及Ui组件等说明
项目中封装了一些插件、以及样式组件等常用的功能模块，在这里统一说明一下

#### localStorage 方法封装以及使用
由于原生的localStorage在存取的时候，无法将object数据类型保存起来，所以我们封装了一个插件，整个项目中统一用封装的storage，由于storage已经设置成全局方法了，可以在组件里只用通过this.localStore直接使用

```javascript
// 在localStorage中获取某个key/value值
this.localStore.get(key);
// 网localStorage中设置某个key/value值
this.localStore.set(key,value);
// 判断localStorage中某个数据是否存在，返回true、false
this.localStore.has(key);
// 删除localStorage中的某个数据
this.localStore.remove(key);
// 清除存储对象中所有的键
this.localStore.clear();
/*
 * 添加监听set添加操作的事件(在构造函数中给handle添加bind(this))
 * window.addEventListener('setLocalStoreEvent', this.handle, false);
 * 移除监听set添加操作的事件
 * window.removeEventListener('setLocalStoreEvent', this.handle, false);
 * ------------------------------------
 * 添加监听remove删除操作的事件(在构造函数中给handle添加bind(this))
 * window.addEventListener('removeLocalStoreEvent', this.handle, false);
 * 移除监听remove删除操作的事件
 * window.removeEventListener('removeLocalStoreEvent', this.handle, false);
 * ------------------------------------
 * 添加监听clear清空操作的事件(在构造函数中给handle添加bind(this))
 * window.addEventListener('clearLocalStoreEvent', this.handle, false);
 * 移除监听clear清空操作的事件
 * window.removeEventListener('clearLocalStoreEvent', this.handle, false);
 */
```

#### toast UI组件封装以及使用
toast主要是弹出的弹窗类UI效果（没有焦点交互），每个项目中都会有此类样式出现。此组件一旦出现，会阻碍页面中焦点的转移和逻辑执行，切记不要用错。如果想暂时阻碍逻辑执行，但是不让页面有UI变化，可以使用Toast.empty()完成

###### 1. toast的引入
```javascript
import Toast from '../toast/Index';
```
###### 2. toast的使用
```javascript
// 普通的提示，有个叹号的图标，默认只有描述的时候不消失 （后2个参数可以不传，时间不传默认不消失）
Toast.info('友情提示', 2000, ()=>{ /* 关闭后的回调函数 */})
// 普通的提示，有个对号的图标，默认只有描述的时候不消失 （后2个参数可以不传，时间不传默认不消失）
Toast.success('操作成功', 2000, ()=>{ /* 关闭后的回调函数 */})
// 普通的提示，有个叉号的图标，默认只有描述的时候不消失 （后2个参数可以不传，时间不传默认不消失）
Toast.error('操作失败', 2000, ()=>{ /* 关闭后的回调函数 */})
// 普通的提示，有个转圈的图标，默认只有描述的时候不消失 （后2个参数可以不传，时间不传默认不消失）
Toast.loading('加载中', 2000, ()=>{ /* 关闭后的回调函数 */})
// 没有样式提示，只会将点击事件给封死，调用destory还可以放开
Toast.empty()
// 判断是否有弹窗，true就证明有toast，false就证明没有
Toast.have()
// 销毁页面中的toast提示窗口【常用】
Toast.destroy()
```

注意：在App.js初始化的时候，要先销毁一次toast，以防止toast存在localstorage中，阻碍各种逻辑执行
```javascript
componentWillMount() {
    //放置出错，先销毁toast，以防止焦点无法转移
    Toast.destroy();
}
```

## 项目结构以及其他附属功能说明
主要说明整个项目的目录结构以及使用方法的相关说明，每个都相对简单

#### 项目结构说明

| 目录结构 | 结构说明 |
|--------|-----------|
| .vscode | 放置vscode的相关配置，例如：settings.json代码检查配置；sftp.json远程ftp配置 |
| config | 设置less的时候，不许放开此隐藏文件夹，不用管，也不要随意配置 |
| public | 放置项目的原始文件，包括从Tizen创建好项目的相关文件等 |
| scripts | 配置运行环境放开的，不用管，也不要随意配置 |
| src | 逻辑代码部分 |
| ./src/assets | 资源文件夹 |
| ./src/components | 公共组件文件夹 |
| ./src/mock | 假数据临时文件夹，最后上线的时候，肯定用不到 |
| ./src/redux | 公共数据存放处 |
| ./src/server | 接口文件夹，所有的网络接口都写在这里 |
| ./src/style | 公共样式文件夹，main.less当前项目的公共样式；public.less所有项目的公共样式；reset.less重置浏览器的样式 |
| ./src/utils | 各种自己封装的方法都放在这里 |
| ./src/utils/pageDom.js | 跟路由以及焦点相关的方法 |
| ./src/utils/request.js | 封装的axios的数据获取的方法 |
| ./src/utils/storage.js | 封装的localStorage |
| ./src/utils/token.js | 封装的token的增删改查方法，所有项目基本都会用到 |
| ./src/utils/utils.js | 公共的常用的方法 |
| ./src/views | 单个页面可以放在这里，他们的样式放在了style里 |

#### 样式写法说明
所有的样式都必须统一用less来写，格式按照以下规则处理
- 每个组件单独配置一个对应的less样式文件
- 样式less文件里都用层级结构来写，并且最顶层的class名字必须组件唯一
- 尽量不要用单个常用单词进行命名，例如某个按钮叫做，class='button'
- 能够用到公共样式里的样式的就尽量用，不要在组件中重新写，例如：盒子模型一定要用公共的

```javascript
// Setting.js
<div className={'setting-page flex-ac flex-col ' + this.props.display}>
    {/* 首页顶部的状态栏 */}
    <div className={'set-row-box flex-1 wi'}>
        <div className={'set-row flex-ac'}>
            <div className={'flex-1'}>联系客服</div>
            <div className={'flex-1 tr'}>关注“唱吧智能点歌台”公众号反馈问题</div>
        </div>
    </div>
</div>
```
```less
// Setting.less
.setting-page {
	.set-row-box {
		margin-top: 82px;
	}
	.set-row {
		height: 80px;
		margin: 0 64px 32px;
		border-radius: 10px;
		font-size: 50px;
		padding: 0 16px;
		&.curr {
			background-color: #bb3dc2;
		}
	}
}
```