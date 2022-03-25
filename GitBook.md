#  1. 背景

## 1.1 GitBook 是什么？

GitBook 是一个基于 Node.js 的命令行工具，支持 Markdown 和 AsciiDoc 两种语法格式，可以输出 HTML、PDF、eBook、mobi 等格式的电子书，很多开源书籍都是使用GitBook 编辑，然后发布到 GitHub 上进行在线阅读。具有丰富的格式功能、友好的编辑特性及易于维护、方便版本跟踪的特性。

## 1.2 为什么需要 GitBook

已经有了 Word、WPS、Tapd、有道云笔记等等众多的本地、在线编辑工具，为什么还需要 GitBook？主要原因在于现有的编辑方式存在以下不足：

- 不具备协同编辑功能，传递繁琐，获取最新版本不友好
- 在线编辑不具备长期性，位置众多难于寻找

相比之下 GitBook 使用markdown 语法编辑，内容可以上传到Gitlab 管理，具备项目工程的概念，能以源码的形式纳入到版本控制中，而不是二进制形式，从而天生支持协同写作，同时部署发布在私有 GitLab 中，针对敏感的技术资料起到隐私保护作用。

## 1.3 用来做什么？

- 开发团队编写实现文档、对外API 文档
- 测试团队编写功能点说明、配置管理文档、发布记录文档
- 团队内部编写技术文档，打造学习型团队进行技术分享

## 1.4 组成部分

- Node.js：GitBook 运行依赖环境
- GitBook命令行：管理文档，预览、制作电子书，安装在Jenkins 服务器用于生成在线预览地址；本地也需要安装一个用于导出pdf格式和本地预览
- Typora：MarkDown 文档编辑工具，用于编写电子书
- PicGo：图片上传工具，用于在 Typora 编辑过程中上传文件
- Jenkins：书籍在线生成，自动打包
- GitLab：作为 GitBook 书籍内容的托管工具
- SourceTree（非必须）：可视化管理 GitLab 仓库，如果对Git 命令行操作

## 1.5 使用流程

1. GitLab 上建立电子书项目工程
2. 通过SourceTree 或 Git 命令行将电子书项目拉到本地
3. 安装Node.js，并通过Node.js 安装本地命令行工具，用于初始化GitBook 工程，及本地预览和pdf 导出
4. 通过 Typora 工具编辑电子书，编辑过程中如果涉及到图片上传，可以通过PicGo 上传
5. Jenkins 中建立电子书编译工程，并关联hook GitLab 中的电子书项目，用于电子书更新后自动触发编译并部署到在线地址
6. 编辑完后本地预览电子书，确认没有问题后将电子书提交到GitLab
7. Jenkins 编译完成，在线预览电子书

# 2. GitBook使用

## 2.1 本机环境搭建

### 2.1 Node.js 安装

访问 [Node.js 官网](https://nodejs.org/en/) 下载对应操作系统版本，安装成功后在 cmd窗体中输入node -v 查看是否安装成功

Windows:

```shell
C:\pc>node -v
v8.11.1

C:\pc>npm -v
5.6.0
```

Mac:

```shell
butelmac@MZFdeMacBook-Pro picgo % node -v
v12.22.3
butelmac@MZFdeMacBook-Pro picgo % npm -v
6.14.13
butelmac@MZFdeMacBook-Pro picgo %
```

### 2.1.2 Gitbook 命令行安装

注：[GitBook 官网](https://www.gitbook.com/) 上本身有客户端编辑器工具，但在国内使用存在翻墙问题，所以只通过 Node.js 安装命令行，用于进行本地预览

Windows：

```shell
C:\pc>npm install gitbook-cli -g
npm WARN notice [SECURITY] lodash has the following vulnerability: 1 low. Go here for more details: https://nodesecurity.io/advisories?search=lodash&version=4.17.4 - Run `npm i npm@latest -g` to upgrade your npm version, and then `npm audit` to get more info.
C:\pc\AppData\Roaming\npm\gitbook -> C:\pc\AppData\Roaming\npm\node_modules\gitbook-cli\bin\gitbook.js
+ gitbook-cli@2.3.2
added 578 packages in 134.873s

C:\pc>gitbook -V
CLI version: 2.3.2
GitBook version: 3.2.3
```

Mac:

```shell
sudo npm install ebook-convert -g
sudo npm install calibre -g

进入https://calibre-ebook.com/download 下载mac版calibre

sudo ln -s /Applications/calibre.app/Contents/MacOS/ebook-convert /usr/local/bin

npm config set registry=http://registry.npm.taobao.org -g

gitbook uninstall 3.2.3 #卸载
gitbook fetch 3.0.0 #安装,3.2.3 版本存在报错问题

butelmac@MZFdeMacBook-Pro picgo % gitbook -V
CLI version: 2.3.2
GitBook version: 3.0.0
```

### 2.1.3 编辑器 Typora 下载安装

访问 [Typora官网](https://www.typora.io/) 下载安装

![image-20210318174934879](http://112.2.0.206:8096/GitBook/uploadfiles/uploads/279eff87e1e13ce9ccd30ee1f374ea14/image-20210318174934879.png)

### 2.1.4 图片上传工具下载安装

目前使用的是PicGo工具，这也是Typora推荐的，可以直接配合使用的工具: [PicGo github上下载地址](https://github.com/Molunerfinn/PicGo/releases)

### 2.1.5 PicGo 配置

- 安装gitlab插件：启动应用后，进入“插件设置”，输入框中输入gitlab进行搜索，安装gitlab插件

![image-20210318183151733](http://10.134.101.126:8090//yeshuo/gitbook/uploads/ae154cf0be19cffb806bc5de07905869/image-20210318183151733.png)

- 选择图床：插件安装完成后，进入“PicGo设置”，“选择显示的图床”中，取消勾选其它选项，仅勾选“GitLab图床”

![image-20210318183345436](http://10.134.101.126:8090//yeshuo/gitbook/uploads/5fc9ed310c980ccbc78cca79becaacc5/image-20210318183345436.png)

- 配置图床：展开“图床设置”，选择“GitLab图床”，设置基本选项（可根据图中进行设置，图中提供为内网地址）

    URL:http://112.2.0.206:8096/

    Group:GitBook（gitlab上的group）

    Project:uploadfiles（gitlab上的项目名）

    Token:-SsYiqoiepeY9wJZvm77（每个用户自己对应的token，在gitlab主页点击右上角头像进入user settings-account，其中的Private token即为此处token）

    ![image-20210826113830942](http://112.2.0.206:8096//GitBook/uploadfiles/uploads/d7410e867235921072d8a1ea9c7e1954/image-20210826113830942.png)

![image-20210409142643451](http://112.2.0.206:8096/GitBook/uploadfiles/uploads/56cf48880979f063aec97f181c76c50f/image-20210409142643451.png)

- 设置PicGo服务端口：进入“PicGo设置”，设置Server，“是否开启Server”选择开，设置监听端口（不修改即为默认端口）

    ![image-20210318191702194](http://10.134.101.126:8090//yeshuo/gitbook/uploads/a2d372ea231c40b395ee0b1fc206fdce/image-20210318191702194.png)

    ![image-20210318191715814](http://10.134.101.126:8090//yeshuo/gitbook/uploads/eafeef012b453212394f8780d8416587/image-20210318191715814.png)

- 重启PicGo，使配置生效（PicGo需要一直在后台运行，不能退出）

### 2.1.6 Typora 配置

工具安装完成后，即可正常进行编辑使用，但无法进行图片上传.目前采用的是PicGo工具进行图片上传，将图片上传至gitlab服务器，具体方法如下：

进入“文件”->“偏好设置”，选择“图像”，“插入图片时...”的下拉框选择“上传图片”，并勾选下方“对本地位置的图片应用上述规则”和“对网络位置的图片应用上述规则”

"上传服务设定“中，”上传服务“选择”PicGo(app)“，下方选择PigGo路径，根据步骤1中安装路径进行选择，默认在”C:\Users\用户名\AppData\Local\Programs\PicGo\“目录下，选择程序文件PigGo.exe

![image-20210318180138383](http://10.134.101.126:8090//yeshuo/gitbook/uploads/060751dd556d07c437621d56496022b5/image-20210318180138383.png)

配置完成后，点击“验证图片上传选项”，进行图片上传验证，出现下图中所示内容，表示配置成功

![image-20210319093650555](http://10.134.101.126:8090//yeshuo/gitbook/uploads/b573b1493a5a667ed8f24917a64d85ca/image-20210319093650555.png)

## 2.2 Gitbook 结合 GitLab的使用

电子书和 GitLab 结合并不是必须，引入Gitlab 是为了以项目工程的概念管理电子书内容，同时方便Jenkins 集成自动部署电子书进行在线浏览。

### 2.2.1 GitLab 上创建电子书工程

1. 目前GitBook 电子书工程统一放在 GitBook 组中：http://112.2.0.206:8096/gitbook

![image-20210826115329791](http://112.2.0.206:8096//GitBook/uploadfiles/uploads/c184a642f0a7c02a89b2b9961e00a109/image-20210826115329791.png)

1. 创建完成后点击项目，获取新建工程的 git 地址：

![image-20210826115518392](http://112.2.0.206:8096//GitBook/uploadfiles/uploads/947114ee2c0d3a190db5883afc0eea95/image-20210826115518392.png)

1. 通过git 命令行或 SourceTree 将刚刚创建的电子书工程项目拉到本地。[SourceTree使用参考](https://www.tapd.cn/53842400/markdown_wikis/show/#1153842400001000441)

### 2.2.2 GitBook 编辑

1. 到工程目录，通过命令 gitbook init 将工程初始化为 GitBook 电子书, 初始化成功会自动生成 SUMMARY.md 和 README.md 两个文件，该文件手动生成也是可以的。

    其中README.md可以编辑简介内容，SUMMARY.md用来编辑目录章节

```shell
butelmac@MZFdeMacBook-Pro BCS-API-Doc % pwd
/Users/butelmac/GitLab/BCS-API-Doc
butelmac@MZFdeMacBook-Pro BCS-API-Doc % gitbook init
info: create SUMMARY.md 
info: initialization is finished
```

1. 完成以上步骤，即可打开 Typora 编辑器开始愉快的电子编辑之旅了
2. 针对编辑完的电子书，想本地预览查看在线效果可以执行 gitbook serve 命令

```shell
butelmac@MZFdeMacBook-Pro BCS-API-Doc % gitbook serve
Live reload server started on port: 35729
Press CTRL+C to quit ...

info: 14 plugins are installed 
info: loading plugin "hide-element"... OK 
info: loading plugin "search-pro"... OK 
info: loading plugin "anchor-navigation-ex"... OK 
info: loading plugin "insert-logo"... OK 
info: loading plugin "highlight"... OK 
info: loading plugin "popup"... OK 
info: loading plugin "toc"... OK 
info: loading plugin "chapter-fold"... OK 
info: loading plugin "code"... OK 
info: loading plugin "splitter"... OK 
info: loading plugin "tbfed-pagefooter"... OK 
info: loading plugin "livereload"... OK 
info: loading plugin "fontsettings"... OK 
info: loading plugin "theme-default"... OK 
info: found 7 pages 
info: found 2 asset files 
warn: "options" property is deprecated, use config.get(key) instead 
INFO:当前正在处理:README.md
INFO:当前正在处理:红云ConferenceSDK接入说明.md
INFO:当前正在处理:Android-SDK/UI_SDK.md
INFO:当前正在处理:Android-SDK/Native_SDK.md
INFO:当前正在处理:iOS-SDK/UI-SDK.md
INFO:当前正在处理:iOS-SDK/Native-SDK.md
INFO:当前正在处理:SCHEMA应用调起.md
info: >> generation finished with success in 1.3s ! 

Starting server ...
Serving book on http://localhost:4000
```

预览效果如下：

![image-20210826150418840](http://112.2.0.206:8096//GitBook/uploadfiles/uploads/3753afbe1d50f4e2dc5ab177a7755ace/image-20210826150418840.png)

## 2.3 GitBook结合jenkins的使用

### 2.3.1 创建 jenkins 工程

在jenkins上选择 GitBook 组新建工程item，输入任务名称，选择复制，从已有的项目配置中拷贝配置，输入 ConferenceSDKDoc

![image-20210826155535159](http://112.2.0.206:8096//GitBook/uploadfiles/uploads/07cbb13e3730aec7aef446928c49406a/image-20210826155535159.png)

![image-20210826155341293](http://112.2.0.206:8096//GitBook/uploadfiles/uploads/2398e62760b0e86314718f34b9c03c01/image-20210826155341293.png)

### 2.3.2 构建配置

1. 修改源码管理地址为项目GitLab 源码

![image-20210826160616443](http://112.2.0.206:8096//GitBook/uploadfiles/uploads/af8187cfa7053f5fe167f7411e03ad1e/image-20210826160616443.png)

![image-20210408145507625](http://10.134.101.126:8090//yeshuo/gitbook/uploads/71f1662edd7f694d592f1482f845deff/image-20210408145507625.png)

1. 创建项目成功后，自动跳转到配置页，在源码管理中，选择Git，在Repository URL中输入gitlab上的项目地址，在Credentials中选择账号

    ![image-20210408150029172](http://10.134.101.126:8090//yeshuo/gitbook/uploads/d7a613c4c5b30cd20d51e2a99bd426df/image-20210408150029172.png)

2. 在构建触发器中勾选Build when a change is pushed to GitLab. GitLab webhook URL，记住该webhook URL 地址，需要添加到GitLab项目中

    ![image-20210826162155351](http://112.2.0.206:8096//GitBook/uploadfiles/uploads/78e2fe247526e7bf06a381d69e0c922d/image-20210826162155351.png)

3. 在构建中添加构建步骤，选择Execute shell，输入需要执行的命令，将目录调整为自己的项目名称 BCS-API-DOC

    ```shell
    gitbook build  #gitbook的编译命令，可编译成静态网页的形式输出，需要保证目录正确
    rm -rf /usr/local/nginx/html/book/BCS-API-DOC #删除原构建内容
    cp -R /home/master/gitbook/_book /usr/local/nginx/html/book/BCS-API-DOC    #将新编译生成的静态页面拷贝到nginx的目录下
    ```

    ![image-20210826162303706](http://112.2.0.206:8096//GitBook/uploadfiles/uploads/6bc1f38b9c9128c4f1235cafc9a35ad8/image-20210826162303706.png)

4. 保存jenkins设置

5. 进入gitlab的项目主页，进入Settings-Integrations页面，在URL中输入步骤3所示的url地址，在Secret Token中输入步骤3中点击高级按钮时生成的secret token（非必须），然后点击下方的Add webhook

    ![image-20210408220830776](http://10.134.101.126:8090//yeshuo/gitbook/uploads/a308f72af740fddcd3709429b87e613d/image-20210408220830776.png)

![image-20210826162857856](http://112.2.0.206:8096//GitBook/uploadfiles/uploads/f94ee906063b5e95865f3ceb2dff50e7/image-20210826162857856.png)

### 2.3.3 提交 & 编译

1. 代码提交

    通过git 命令行 或 SourceTree 将电子书代码进行提交，除了 _book 目录不用提交，其他目录都可以提交。（node_modules 为下载的插件，占用文件数比较多，原则上可以不用提交，但为了避免jenkins 环境编译报错，或者其他同事下载下来没有插件无法进行本地预览，所以也一并提交上去）

    ![image-20210826164005779](http://112.2.0.206:8096//GitBook/uploadfiles/uploads/b714fd4c38509bce58e5b1b3bd81bf73/image-20210826164005779.png)

2. 提交完后，会看到 Jenkins 自动触发编译

![image-20210826163407947](http://112.2.0.206:8096//GitBook/uploadfiles/uploads/59971291db4c474c59193b09ab0747b3/image-20210826163407947.png)

1. 如果编译报错，想看控制台日志可以在 Jenkins 上点击编译记录 #1 查看

![image-20210826164340445](http://112.2.0.206:8096//GitBook/uploadfiles/uploads/ed7e9ba0c2fc6c272e7ef039ffc76068/image-20210826164340445.png)

### 2.3.4 在线浏览

在线浏览地址：http://112.2.0.206:20088/book/BCS-API-DOC/

其中固定部分：http://112.2.0.206:20088/book/

项目部分，为在jenkins 构建配置中最终填的目录名称BCS-API-DOC

![image-20210826163519806](http://112.2.0.206:8096//GitBook/uploadfiles/uploads/726bd466b9627c7f3ccd4eb1cf38ad24/image-20210826163519806.png)

# 3. GitBook 文件结构

![image-20210826165122711](http://112.2.0.206:8096//GitBook/uploadfiles/uploads/1fc2ce3a71d8f56fe9ac5d34ea3b4596/image-20210826165122711.png)

```shell
README.md #必须，电子书简介，同时上传到GitLab 上后，项目主页中也会显示该内容
SUMMARY.md #必须，用于声明目录结构 [名称](链接)
book.json #必须，可以使用现有的配置，用于声明插件，丰富GitBook 布局效果，附录中包含book.json 文件，可下载使用
node_modeles #必须，自动生成，也可以拷贝现有的配置，插件的安装文件，附录中包含压缩包，下载后解压
Android-SDK #自定义，电子书文件夹
SCHEMA应用调起.md #自定义，电子书文本
_book #编译生成产物,本地浏览和在线浏览均需要
```

附录：

1. [book.json 文件下载](http://112.2.0.206:8096//GitBook/uploadfiles/uploads/5c16e919f8bd300488c8977ced65cb66/book.json)
2. [node_models 下载](http://112.2.0.206:8096//GitBook/uploadfiles/uploads/0f848a8d784fba244878206df578daa7/node_modules.zip)
3. SUMMARY 示例

```markdown
# Summary

* [概述](README.md)
* [红云ConferenceSDK接入说明](红云ConferenceSDK接入说明.md)
* [Android-SDK](README.md)
  * [UI SDK](Android-SDK/UI_SDK.md)
  * [Native SDK](Android-SDK/Native_SDK.md)
* [iOS-SDK](README.md)
  * [UI SDK](iOS-SDK/UI-SDK.md)
  * [Native SDK](iOS-SDK/Native-SDK.md)
* [SCHEMA 应用调起](SCHEMA应用调起.md)
```

# 4. GitBook 常用命令

1. 初始化

    ```shell
    gitbook init #将生成 README及 SUMMARY 文件
    ```

2. 编译

    ```shell
    gitbook build #将编译电子书并生成 _book 目录文件
    ```

3. 在线预览

    ```shell
    gitbook serve #也会触发编译，完成后可通过 http://localhost:4000/ 在线浏览
    ```

4. 转换为本地 pdf、电子书

    ```shell
    gitbook pdf . xxx.pdf #在电子书目录中执行，目前生成的pdf 文件对于目录索引的处理有问题，可能和使用的插件有关系，尝试处理中
    gitbook mobi . xxx.mobi #kindle 支持的电子书格式
    gitbook epub . xxx.epub # 可通过在线阅读器或下载其他阅读器查看 https://www.neat-reader.cn/webapp#/
    ```

