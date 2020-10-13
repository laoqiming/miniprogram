# 使用自定义组件覆盖原生image组件



## 问题的产生

1. 在微信小程序中使用image组件显示图片，但是在使用过程中发现，有时候图片会因为各种原因导致加载失败而不显示；
2. 在某些特殊情况下（暂未知原因，错误代码是404，但图片地址确实能访问），图片加载失败而不显示后，微信会有缓存，即使下次重新打开，因为是同样的地址，所以还是不显示；
3. 而如果更换了地址，则又能正常显示；
4. 虽然image组件提供了 binderror 事件可以对加载失败进行处理，但是如果每个页面，每个标签都去加上处理事件，那是很麻烦的事情；



## 解决过程

### 第一阶段：

1. 写一个自定义组件 my-image，在该自定义组件中对加载错误进行处理（url加随机数重新加载）；

   my-image.wxml

   ```html
   <image src="{{src}}" mode="{{mode}}" webp="{{webp}}" lazy-load="{{lazyLoad}}" show-menu-by-longpress="{{showMenuByLongpress}}" binderror="onError" bindload="onLoaded" class="{{cssClass}}" style="{{cssText}}"></image>
   ```

   my-image.js

   ```javascript
   Component({
     /**
      * 组件的属性列表
      */
     properties: {
       src: {
         type: String,
         value: '',
       },
       cssClass: {
         type: String,
         value: ''
       },
       cssText: {
         type: String,
         value: ''
       },
       mode: {
         type: String,
         value: 'scaleToFill'
       },
       webp: {
         type: Boolean,
         value: false,
       },
       lazyLoad: {
         type: Boolean,
         value: false,
       },
       showMenuByLongpress: {
         type: Boolean,
         value: false,
       },
       retry: {
         type: Number,
         value: 1
       },
       errorImage: {
         type: String,
         value: 'error.png'
       }
     },
   
     /**
      * 组件的初始数据
      */
     data: {
       loadCount: 0,
       errorMessage: ''
     },
   
     /**
      * 组件的方法列表
      */
     methods: {
       onError(e) {
         let imagePath = this.data.src.replace(/[&\?][\.\d]+$/g, '')
         if (!/^https?:\/\/[^\/]+\/?$/i.test(imagePath)) {
           // 正常路径图片
           this.data.errorMessage = e.detail.errMsg
           if (this.data.retry > 0) {
             if (this.data.loadCount < this.data.retry) {
               this.data.loadCount++
               let path = imagePath + (imagePath.indexOf('?') == -1 ? '?' : '&') + Math.random()
               console.log('重试',path)
               this.setData({
                 src: path
               })
             } else {
               this.setData({
                 src: this.data.errorImage
               })
               this.triggerEvent('error', e)
             }
           } else {
             this.triggerEvent('error', e)
           }
         } else {
           // 非正常路径图片，直接设置为错误图片
           this.data.loadCount++
           this.setData({
             src: this.data.errorImage
           })
           this.triggerEvent('error', e)
         }
       },
       onLoaded(e) {
         this.triggerEvent('load', e)
       }
     }
   })
   ```

   

2. 把原生image组件替换为my-image组件；

   ```html
   <!-- <image src="{{imagePath}}" mode="aspectFit" style="width:200rpx;height:200rpx;"></image> -->
   <my-image src="{{imagePath}}" retry="2" mode="aspectFit" css-text="width:200rpx;height:200rpx;"></my-image>
   ```

经过上面的操作，目的基本可以达到了，加载失败的图片都被加载出来了，但问题没完。

原有页面经过替换标签后大部分图片都能正常显示，但是也导致了另外的问题：

**原来写的级联样式不能应用到自定义组件的图片上**

如果每个样式都要去做处理，这工作量可不小，于是有了第二阶段。



### 第二阶段：

解决级联样式不能应用到图片标签的问题。

1. 为了解决第一阶段的问题，马上找官方文档仔细阅读，希望能从中找到解决办法。

2. 最后从下面文档找到解决办法：https://developers.weixin.qq.com/miniprogram/dev/framework/custom-component/wxml-wxss.html

3. 设置样式隔离为page-apply-shared

4. 设置virtualHost为true

   ```javascript
   Component({
     options: {
       virtualHost: true,
       styleIsolation: 'apply-shared'
     },
   })
   ```

至此以上问题解决。



### 第三阶段：

虽然经过上面两个阶段，这个自定义图片组件已经能达到目的了，但是，对于一个少则几十，多则上百个页面的小程序来说，要一个一个页面去修改image标签，并且修改class为css-class，那工作量也不少（当然可以用正则批量替换）。

最好就是不用修改原来的代码，在某个地方直接操作就最好了。

于是产生以下想法：

1. 能不能像html dom 那样，使用js对image进行遍历绑定错误处理事件？
2. 能不能在某个地方改写原生image控件？

第一个想法很快被否定掉，除非在渲染前对wxml模板动手。

第二个想法貌似也找不到入手的地方，搜索了网上的资料也找不到相关的资料。

折腾了好久，最后突发奇想，如果引入自定义组件的时候，定义自定义组件的标签名是image，那会不会替换掉原生的image控件呢？

马上动手！

```javascript
"usingComponents": {
    "image": "/components/myImage/myImage"
}
```

惊喜！！！真的被替换掉了！

那接着接续解决两个小问题，class属性和style属性。

my-image.wxml

```
<image src="{{src}}" mode="{{mode}}" webp="{{webp}}" lazy-load="{{lazyLoad}}" show-menu-by-longpress="{{showMenuByLongpress}}" binderror="onError" bindload="onLoaded" class="class" style="{{style}}"></image>
```

my-image.js

```javascript
Component({
  options: {
    virtualHost: true, // 设置组件为虚拟节点，让组件第一级子节点直接插入DOM中
    styleIsolation: 'apply-shared', // 让组件可以应用外部级联样式
  },
  externalClasses: ['class'], // 让组件可以接受class属性
  /**
   * 组件的属性列表
   */
  properties: {
    src: {
      type: String,
      value: '',
    },
    style: {
      type: String,
      value: '',
    },
    mode: {
      type: String,
      value: 'scaleToFill'
    },
    webp: {
      type: Boolean,
      value: false,
    },
    lazyLoad: {
      type: Boolean,
      value: false,
    },
    showMenuByLongpress: {
      type: Boolean,
      value: false,
    },
    retry: {
      type: Number,
      value: 1
    },
    errorImage: {
      type: String,
      value: 'error.png'
    }
  },

  /**
   * 组件的初始数据
   */
  data: {
    loadCount: 0,
    errorMessage: ''
  },

  /**
   * 组件的方法列表
   */
  methods: {
    onError(e) {
      let imagePath = this.data.src.replace(/[&\?][\.\d]+$/g, '')
      if (!/^https?:\/\/[^\/]+\/?$/i.test(imagePath)) {
        // 正常路径图片
        this.data.errorMessage = e.detail.errMsg
        if (this.data.retry > 0) {
          if (this.data.loadCount < this.data.retry) {
            this.data.loadCount++
            let path = imagePath + (imagePath.indexOf('?') == -1 ? '?' : '&') + Math.random()
            console.log('重试', path)
            this.setData({
              src: path
            })
          } else {
            this.setData({
              src: this.data.errorImage
            })
            this.triggerEvent('error', e)
          }
        } else {
          this.triggerEvent('error', e)
        }
      } else {
        // 非正常路径图片，直接设置为错误图片
        this.data.loadCount++
        this.setData({
          src: this.data.errorImage
        })
        this.triggerEvent('error', e)
      }
    },
    onLoaded(e) {
      this.triggerEvent('load', e)
    }
  }
})
```

自定义组件经过上面的改造后，只要在每个页面的.json文件增加自定义组件引入就可以了。

```javascript
"usingComponents": {
    "image": "/components/myImage/myImage"
 }
```

小程序代码片段链接：https://developers.weixin.qq.com/s/99vhg4mi7OlT