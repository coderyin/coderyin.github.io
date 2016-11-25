# Swift 服务端
Swfit既可以作为客户端编写语言，又可以编写服务端语言，对于使用Swift语言开发iOS应用的程序猿们，尝试一下自己为App写个后台，乐趣无穷。我相信Swift语言替换掉Objectvie-c是未来的趋势，并且Swift端开源框架也比较成熟，也可以让OC程序猿熟悉Swift语法。本文主要以Vapor框架为例（Perfect框架的性能要优于Vapor，但是部署到云端没有Vapor容易，折腾了半天，最终选择Vapor）本文主要以介绍以下点

- Vapor的配置
- Vapor本地部署
- 部署代码到云（heroku）

## 一、配置Vapor框架
### 1.检查当前环境 
**Vapor目前只支持Swift3.0，Xcode8以上，请自行检查版本（昨天搞了半天Perfect框架，一直报错原因就是Perfect只支持8.1以上Xcode和Swift3.0.1**
### 2.配置环境
在命令行中输入命令，检测是否可以运行

    curl -sL check.vapor.sh | bash
没有返回任何消息，就是好消息，下一步下载Vapor toolbox（toolbox可以帮助用户简化一些操作）

    curl -sL toolbox.vapor.sh | bash

![image](https://cloud.githubusercontent.com/assets/1342803/17454691/97e549e2-5b6d-11e6-979a-f0cd6b6f1b0a.png)

安装toolbox

    curl -sL toolbox.vapor.sh | bash

## 二、hello world 本地部署
终端输入

    vapor new Hello

接下来，进入Hello工程目录

    cd Hello
    
进入目录你发现没有可运行的xcode工程有点懵逼是不是，继续输入
    
    vapor xcode -y

等待时间略长，去抽两根烟吧。


终于完成了，**左上角运行选择App->My Mac**

找到main.swift文件，写入如下代码

```
import Vapor

let drop = Droplet()

drop.get("hello") { request in
    return "Hello, world!"
}

drop.resource("posts", PostController())

drop.run()

```

运行工程
在浏览器中访问

    http://localhost:8080/hello
    
或者  

    0.0.0.0:8080/hello
或者  

    http://127.0.0.1:8080/hello

**注意，一定要有端口号如8080，且get的hello区分大小写，仔细看清你的get方法中的参数哦**

hello, world 对于第一次写服务端的人是否有点小激动呢。我们通过get方法获取到数据，其他请求方式同理，只要你的代码没有问题，服务端就会给你返回你请求的数据。自此，本地部署完成。

## 三、部署云端
作为一个程序员，你一定不满足于服务端代码只在自己服务器或局域网内运行，一般服务器也是一笔不小的费用。如果你和我一样只是为了学习，这笔费用有一些大财小用。but，国外一家网站提供免费的代码部署服务，那就是heroku，接下来我们要将我们的hello工程部署到heroku。

#### 注册heroku
进入
[heroku](http://note.youdao.com/)注册，注册完并不需要你去创建应用，因为创建应用我们可以直接在终端完成。

#### 登录并提交应用
继续我们的终端之旅
    
    heroku login

之后按照提示输入账户名和密码

    cd <Your Project>

进入你工程目录

    git init
    
对于使用git的程序猿们，这行代码很熟悉吧

    git add .
    git commit -am "Description"

提交

到了这里 vapor toolbx登场了

    vapor heroku init

第一个输入y，自定义一个应用名，比如hello
第二个很重要，问你是否要自定义buildpack，**一定选yes，一定选yes，一定选yes**

输入buildpack的地址

    https://github.com/kylef/heroku-buildpack-swift.git

第三个y/n，是否立即上传到heroku，没问题的话，选择y.

**如果buildpack写错了，也可以到heroku控制台中选择对应的app，选择setting，找到其中的buildpack，去修改**


一切顺利的话，打开
https://~~yourappname~~.herokuapp.com/hello 试试吧