#IOS APP 企业发布：in-house方式
Author：飘易 Source：飘易
Categories：移动开发 PostTime：2015-2-7 21:38:55

##正 文：
    首先详细说明一下我们的目标，我们需要发布一个ipa放到网上，所有人（包括越狱及非越狱设备）可以直接通过链接下载安装，不需要通过AppStore，也不需要安装任何证书。要达到这个目标，就需要企业级开发账号（299刀）。企业级开发账号的申请流程在这里就不细说，主要说一下In House ipa的发布流程。
    
    由于网上流传的大多是老版本的xcode的发布方式，因此到了xcode 6以后版本，发布 in-house 的app方式有了不少变化
    。
    网上可以参考的英文文章：
    
Provision iOS IPA App for In-House Enterprise

Distribution：http://johannesluderschmidt.de/provision-ios-ipa-app-for-in-house-enterprise-distribution/2993/

    ##基本上这篇文章介绍的发布流程比较符合新版本，如果你英文不太好，可以看下这篇机器翻译的中文文章：
    
在企业内部分发 iOS 应用程序：http://www.cocoachina.com/industry/20140818/9401.html

    ##另外，还有一篇过时的资料也可以参考，可以帮助你理解一些技巧，比如钥匙串的证书管理：
    
iOS企业开发In House ipa发布流程：http://blog.csdn.net/zhaoxy_thu/article/details/17277527

    ##企业发布 in-house 的核心步骤：
    
一、先登录 https://developer.apple.com/membercenter/index.action 开发者会员中心，打开 “Certificates, Identifiers & Profiles”：

<img src="http://www.piaoyi.org/upimg/file071127_08/02/201551711491888.jpg" />

先创建一个app Identifiers:
<img src="http://www.piaoyi.org/upimg/file071127_08/02/201551711544333.jpg" />


注意：Bundle ID 就是我们xcode本地创建的app id ，必须保证唯一，而且不能有特殊符号，比如 通配符* 和下划线 _ 等。
二、创建对应上面app id的 Provisioning Profiles
<img src="http://www.piaoyi.org/upimg/file071127_08/02/201551711591978.jpg" />

下一步：Select App ID 选择对应的id

下一步：Select certificates 选择发布者证书 (iOS Distribution)，不能选择开发者证书！
下一步：生成证书后下载到本地双击安装即可。
<img src="http://www.piaoyi.org/upimg/file071127_08/02/20155171224891.jpg" />


三、XCODE 里 工程属性设置
1、Identidy里 Team 里选择 发布者账号
2、Deployment Target 选择希望支持的最低ios版本号，一般为7.0
<img src="http://www.piaoyi.org/upimg/file071127_08/02/20155171211258.jpg"/>

3、Build Settings 里 Code Signing 签名设置 Code Signing Identity 里全部设置为发布者证书
4、Provisioning Profile 里设置我们在第二步里下载并安装好的证书文件。
<img src="http://www.piaoyi.org/upimg/file071127_08/02/201551712141858.jpg"/>

四、导出 ipa文件
product - Archive 选择对应的app，然后 Export 出来。
<img src="http://www.piaoyi.org/upimg/file071127_08/02/201551712191619.jpg"/>


导出：

<img src="http://www.piaoyi.org/upimg/file071127_08/02/201551712204866.jpg"/>
发布的注意点：
1、打包的时候不能选择模拟设备，可以选择iOS Device或连接真机，否则 product - Archive 选项是灰色的不可打包。
<img src="http://www.piaoyi.org/upimg/file071127_08/02/201551721333993.jpg"/>

2、由于XCODE6在生成 .ipa 文件的时候，不再自动生成 .plist 文件，因此需要我们手工创建 ipa 同名的 plist文件。可以直接从之前的老版本生成的plist文件复制过来修改。（via）
飘易直接提供demo.plist文件格式：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
        <key>items</key>
        <array>
                <dict>
                        <key>assets</key>
                        <array>
                                <dict>
                                        <key>kind</key>
                                        <string>software-package</string>
                                        <key>url</key>
                                        <string>[INSERT URL HERE]</string>
                                </dict>
                        </array>
                        <key>metadata</key>
                        <dict>
                                <key>bundle-identifier</key>
                                <string>[INSERT BUNDLE ID HERE]</string>
                                <key>bundle-version</key>
                                <string>[INSERT VERSION HERE]</string>
                                <key>kind</key>
                                <string>software</string>
                                <key>title</key>
                                <string>[INSERT APP TITLE HERE]</string>
                        </dict>
                </dict>
        </array>
</dict>
</plist>
```
红色部分需要修改为你的工程实际内容。

然后把 .ipa 和 .plist 文件都上传到支持HTTPS协议的服务器，在网页源码里写入：
```html
<a href="itms-services://?action=download-manifest&url=https://raw.githubusercontent.com/tanzhihang/weihuobao/master/com.buyigang.weihuobao.plist" id="text">Install the In-House App</a> 
```
 
在IOS设备iPhone 或 ipad上使用Safari浏览器打开https网址，点击上面的链接即可安装app了。
