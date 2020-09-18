<h1 align="center">Tiezn Studio 前端开发注意事项</h1>

一些开发过程中踩过的坑和Tizen的使用建议

## 环境

Tizen Studio 是三星自己的开发工具，我们可以不使用他的IDE来编辑代码，但是要用他来将项目打包，才能使得三星认同

### 安装

* 安装包下载地址： [下载Tizen Studio](https://developer.tizen.org/development/tizen-studio/download)

建议安装到C盘你找得到的地方（该程序不生成桌面快捷方式）

安装完成后运行 tizen-studio/ide/TizenStudio.exe (为了方便后续使用，建议生成桌面快捷方式)

### 插件

我们开发的电视APP需要对应的开发环境，下面的插件就是电视的开发环境了

第一步：打开插件安装器

![](http://note.youdao.com/s/Cu5anJdf)

第二步：下载本地测试机插件

![](../image/插件安装_2.jpg)

第三步：下载扩展插件

![](../image/插件安装_3.jpg)

插件下载完之后就可以运行测试环境了(右键点击你的项目)

![](../image/测试机环境运行.jpg)

### 数字证书

真机测试就需要将你的项目用Tizen Studio打包了，打包需要对应的数字证书，下面就是数字证书的安装了

第一步：打开数字证书安装

![](../image/数字证书_1.jpg)

第二步：新建数字证书

![](../image/数字证书_2.jpg)

第三步：命名

![](../image/数字证书_3.jpg)

第四步：选择已有数字证书

![](../image/数字证书_4.jpg)

第五步：上传数字证书

![](../image/数字证书_5.jpg)

第六步：完成

![](../image/数字证书_6.jpg)

数字证书安装完成之后，就可以对项目进行打包了，打包成功之后会在项目根目录下生成 .wgt 格式的文件。

![](../image/项目打包.jpg)

### 测试机安装

1. 将打包生成的 .wgt 文件拷贝到测试机电脑的 **D:\TC\webapps\youshu** 文件夹下
2. 运行 **D:\TC\bin** 下的 shutdown.bat 然后 shutuo.bat 重新读取本地文件
3. 打开测试机环境(一般已处于打开状态下)
![](../image/测试机环境.jpg)
4. 传入安装包
	+ 停止日志: Ctrl+P P (如果日志刷屏的话)
	+ 启动命令: 20089999 再输入 98 (如果wascmd 命令失效的话)
	+ 进入文件夹: cd /opt/home
	+ 查询所有文件: ls (查看所有传入的安装包)
	+ 删除安装包: rm xxx.wgt (如果你之前有传入当前项目的安装包一定要先删除掉)
	+ 查询测试机所有软件信息: wascmd -l (是小写L,不是大写I) 找到你要安装的项目的ID(app_id)
	+ 卸载软件: wascmd -u xxx(ID)
	+ 传入安装包: `wget http://192.168.0.102:8089/youshu/xxx.wgt`
	+ 安装软件: wascmd -i xxx(ID) -p /opt/home/xxx.wgt
	+ 安装完就可以再电视上运行你的项目了
	+ 输入 **`logger-mgr --filter ConsoleMessage && dlogutil ConsoleMessage:D &`** 可以查看你的项目打印的日志

### 真机安装

将打包生成的 .wgt 文件给到后端技术员同志，让他帮忙进行二次打包，将二次打包后的 .license 和 .tmg 俩个文件拷贝到U盘下的 userwidget 文件夹下（拷贝之前清空该文件夹），打开电视停留在APPS界面，然后插入U盘，项目会自动安装。