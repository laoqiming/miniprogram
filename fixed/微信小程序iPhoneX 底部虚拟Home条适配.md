# 微信小程序iPhoneX 底部虚拟Home键区域适配方案

## 为什么要适配？

先看下iPhoneX和之前的iPhone的区别，如下图

![](https://img-blog.csdnimg.cn/20201010143043548.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xhb3FpbWluZw==,size_16,color_FFFFFF,t_70#pic_center)

可以看出主要是顶部和底部多了一些区域，如果页面内容进入该区域，有可能会导致：

1. 内容被遮挡；
2. 内容和虚拟Home键重叠而引起误操作；

因此，我们需要对iPhoneX进行一些必要的适配。

## 怎么适配？

因为我们要做的是微信小程序的适配，而我们开发的页面运行在微信里面，顶部空间微信已经做了适配，所以我们仅需对底部虚拟Home键区域进行适配即可（自定义导航例外）。

这里有两种情况：

1. 底部无悬浮物件的情况
2. 底部有悬浮物件的情况

### 底部无悬浮物件的情况

只要把页面内容容器的下内边距设置为虚拟Home键区域的高度即可。

### 底部有悬浮物件的情况

需把悬浮物件的下内边距设置为虚拟Home键区域的高度，并且页面内容容器的下内边距设置为悬浮物件的高度（原悬浮物件高度+虚拟Home键区域高度）。

### 具体实现

- 虚拟Home键区域的高度：

  iOS11 的Webkit 的新增了一个 CSS env()，用于获取安全区域与边界的距离，有四个预定义的变量：

  - safe-area-inset-left：安全区域距离左边边界距离
  - safe-area-inset-right：安全区域距离右边边界距离
  - safe-area-inset-top：安全区域距离顶部边界距离
  - safe-area-inset-bottom：安全区域距离底部边界距离

- 定义css变量

  ```css
  page{
    /* 使用css变量定义安全边距 */
    --iphonex-fix-bottom:0;
    --iphonex-fix-bottom: constant(safe-area-inset-bottom); /* iOS11.2 beta 开始被弃用 */
    --iphonex-fix-bottom: env(safe-area-inset-bottom);
  }
  ```

- 应用安全边距

  ```css
  .container{
    /* 应用安全边距 */
    padding-bottom:var(--iphonex-fix-bottom);
  }
  ```

  

具体实现代码见以下代码片段：

https://developers.weixin.qq.com/s/cgxGsZmj7ukG



