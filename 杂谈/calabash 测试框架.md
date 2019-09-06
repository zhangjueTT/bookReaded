## calabash介绍

Calabash is an automated testing technology for Android and iOS native and hybrid applications.

While Calabash is completely free, Xamarin provides a number of commercial services centered around Calabash and quality assurance for mobile, namely Xamarin Test Cloud consisting of hosted test-execution environments which let you execute Calabash tests on a large number of Android and iOS devices.

cucumber是BDD(Behavior-driven development，行为驱动开发)的测试架构。它使用自然语言来描述测试，使得非程序员可以理解他们。

![](https://upload-images.jianshu.io/upload_images/13390267-29faf46e6a2ff2ba.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

calabash 框架是基于Ruby脚本语言进行测试的，因此熟悉Ruby脚本的使用很重要。

calabash框架通过HTTP请求在浏览器以及移动设备之间进行通信，当然你也可以调用Shell和本机通信。

gem是Ruby中的包管理工具。

rbenv is a system for managing ruby versions.

We recommend that you always use a Gemfile and bundler.

Bundler provides a consistent environment for Ruby projects by tracking and installing the exact gems and versions that are needed.

Gemfile是bundler中提供的工具

[calabash github 主页](https://github.com/calabash)

[calabash-ios wiki页](https://github.com/calabash/calabash-ios/wiki)

[cucumber 使用文档](https://cucumber.io/docs/cucumber/api/)

[Ruby 语法简易教程](https://www.runoob.com/ruby/ruby-tutorial.html)

## 生命周期 
1. env.rb
2. launch.rb：目前不清楚这个文件是否真的被启动了，因为调不起来
3. user_define.rb
4. user_hook.rb：里面用到了cucumber的生命周期函数

## 使用工具与脚本
init_env.sh: 安装各种需要的工具，以及配置环境的脚本
build.sh：安装demo APP

**debug_tools**: 文件夹中包含python脚本，用于Log中关键字的验证，主要分为：key_log,sip_log,state_log

sox：声音相关的工具
tshark：wireshark抓包命令
rvi: 虚拟IOS链接设备

ios-deploy：是一个终端安装和调试iPhone应用的是第三方开源库

其他工具望名知意，可以用到时再看。

## log相关
目前存在的问题： 

**sip_log** 和 **key_log** 是通过json文件配置的，而**state_log**是通过手动输入的，方式应该统一，并且形成一个列表，方面后面的输入。并且写case的位置在主模块，json文件所处的位置在submodule，这不利于通过更新，应该修改。

account state 和 call state 的测试case应该区分，目前主要测试的事account的state transision，Event是否测试也需要进一步确定。

Sip协议中挖掘出来参数没有一个统一的记录，不利于非开发人员的使用，并且长时间开发人员也需要花大量的时间去熟悉。因此应该减少硬编码，加入统一的配置文件，有利于扩展和熟悉。

硬编码问题，应该引入配置文件，第一步可以替代所有的正则表达式。

state:

"['accountidle-accountinprogress', 'accountnewprov', 'accountinprogress-accountready', 'accountregsuccess', 'accountincomingcall', 'callidle-callreplying', 'callstartreplywithmsg', 'callcreatesdpsuccess', 'callcandidatecompleted', 'callreplying-terminate', 'callreject', 'callreject', 'accountincomingcall', 'callidle-callreplying', 'callstartreplywithmsg', 'callcreatesdpsuccess', 'callcandidatecompleted', 'callreplying-terminate', 'callsendtovoicemail', 'callsendtovoicemail']"

















