---
title: SSM框架实现支付宝支付功能
date: 2019-12-16 14:20:50
categories: 移动支付
tags: Java
---

> 移动互联时代的来临，快捷的移动支付使得生活变得越来越多姿多彩。那么移动支付到底是怎么实现的呢？今天就让我们来揭开它的神秘面纱！

### 支付宝测试环境代码测试
**源代码**
```
 Github地址：https://github.com/OUYANGSIHAI/sihai-maven-ssm-alipay
```
**当然可以下载官方的demo**
```
https://docs.open.alipay.com/270/106291/
```
![](https://img-blog.csdnimg.cn/2019121614310813.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEwMDA2NA==,size_16,color_FFFFFF,t_70#pic_center)

### 配置AlipayConfig

**(1) 注册蚂蚁金服开发者账号（免费，不像苹果会收取费用）**

**注册地址：https://open.alipay.com **，用你的支付宝账号扫码登录，完善个人信息，选择服务类型（我选的是自研）。
![](https://img-blog.csdnimg.cn/20191216143438154.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEwMDA2NA==,size_16,color_FFFFFF,t_70#pic_center)

**(2)设置app_id和gatewayUrl**
![](https://img-blog.csdnimg.cn/20191216143747263.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEwMDA2NA==,size_16,color_FFFFFF,t_70#pic_center)
其中密钥需要自己生成，appID和支付宝网关是已经给好的，网关有dev字样，表明是用于开发测试。
**(3) 设置密钥**
![](https://img-blog.csdnimg.cn/20191216144028707.png#pic_center)
点击“生成方法”，打开界面如下：
![](https://img-blog.csdnimg.cn/20191216144111780.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEwMDA2NA==,size_16,color_FFFFFF,t_70#pic_center)
下载密钥生成工具，解压打开后，选择2048位生成密钥：
![](https://img-blog.csdnimg.cn/20191216144331953.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEwMDA2NA==,size_16,color_FFFFFF,t_70#pic_center)
设置方法,"打开密钥文件路径"：
![](https://img-blog.csdnimg.cn/20191216144510845.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEwMDA2NA==,size_16,color_FFFFFF,t_70#pic_center)
![](https://img-blog.csdnimg.cn/20191216144605254.png#pic_center)
复制应用公钥2048.txt中的内容到点击"设置应用公钥"的弹出框中，保存：

### 商户私钥（merchant_private_key）
复制 应用私钥2048.txt 中的内容到merchant_private_key中。
### 支付宝公钥（alipay_public_key）
![](https://img-blog.csdnimg.cn/20191216144847151.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEwMDA2NA==,size_16,color_FFFFFF,t_70#pic_center)
点击如上图链接，复制弹出框里面的内容到alipay_public_key。
如果这个设置不对，结果是：支付成功，但是验签失败。
如果是正式环境，需要上传到对应的应用中：
![](https://img-blog.csdnimg.cn/20191216145134149.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEwMDA2NA==,size_16,color_FFFFFF,t_70#pic_center)

**(4) 服务器异步通知页面路径（notify_url）**

如果没有改名，修改IP和端口号就可以了，我自己的如下:
```
http://localhost:8080/alipay.trade.page.pay-JAVA-UTF-8/notify_url.jsp
```
**(5) 页面跳转同步通知页面的路径（return_url）**
```
http://localhost:8080/alipay.trade.page.pay-JAVA-UTF-8/return_url.jsp
```

### 测试运行
![](https://img-blog.csdnimg.cn/20191216145430728.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEwMDA2NA==,size_16,color_FFFFFF,t_70#pic_center)

测试用的支付宝买家账户可以在"沙箱账"这个页面可以找到：
![](https://img-blog.csdnimg.cn/20191216145557853.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjEwMDA2NA==,size_16,color_FFFFFF,t_70#pic_center)
支付成功后，验签结果