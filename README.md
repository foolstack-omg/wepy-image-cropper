# 基于 image-cropper 开发的wepy版本微信小程序截图插件

## 说明

此插件基于image-cropper微信原生插件开发, 可能会增加部分功能，具体请查阅以下的插件文档

原生插件传送门：
[微信原生插件 image-cropper 地址](https://github.com/wx-plugin/image-cropper)

使用到该插件的案例：
[同游分账小程序](https://github.com/foolstack-omg/bill-split-weapp)

## 一款高性能的小程序图片裁剪插件，支持旋转、设置尺寸
###### `1.功能强大，请看下面demo。`
###### `2.性能超高超流畅，大图毫无卡顿感。`
###### `3.可以设置导出图片尺寸。`
###### `4.组件化，使用非常简单。`
###### `5.点击中间窗口实时查看裁剪结果。`
<h2 align = "center" style="">体验Demo</h2>
<div align=center ><img width="200" height="200" src="https://raw.githubusercontent.com/1977474741/image-cropper/dev/image/code.jpg?v=0"/></div>


## 使用

### 安装组件
```
npm install wepy-image-cropper --save
```

### 引入组件
```javascript
<template>
  <view wx:if="{{show_cropper}}" style="position: fixed; top: 0; left: 0; right: 0; bottom: 0; background: black;">
      <wepy-image-cropper id="image-cropper" :limit_move="limit_move" :disable_rotate="disable_rotate" :disable_ratio="disable_ratio" :borderColor="borderColor" :width="width" :height="height" @load.user="cropperload" @imageload.user="loadimage"></wepy-image-cropper>
      <view style="height: 100rpx; box-shadow:0px -2rpx 0px 0px white; display: flex; flex-direction: row; align-items: center; justify-content: space-around; position: absolute; width: 100%; bottom: 0; z-index: 9999;">
        <view @tap="hide_cropper" style="font-size: 36rpx; color: white;">取消</view>
        <view @tap="finish_cut" style="font-size: 36rpx; color: white;">完成</view>
      </view>
    </view>
</template>

<script>
  import wepy from 'wepy'
  import WepyImageCropper from 'wepy-image-cropper'

  export default class Test extends wepy.page {
    config = {
      navigationBarTitleText: '',
    }
    components = {
      'wepy-image-cropper': WepyImageCropper
    }

    mixins = []

	cropper = null 
    data = {
     	src: '',
	    width: 250,//宽度
	    height: 250,//高度
	    limit_move: true,
	    disable_rotate: true,
	    borderColor: 'white',
	    disable_ratio: true,
	    show_cropper: true,
    }
    onLoad(options) {
    }
    onReady() {
      //开始裁剪
      this.src = 'https://raw.githubusercontent.com/1977474741/image-cropper/dev/image/code.jpg'
      wx.showLoading({
        title: '加载中'
      })
    }

    computed = {
    }

    methods = {
          cropperload(e) {
            console.log("cropper初始化完成:" + e);
            this.cropper = e.cropper
            this.cropper.$wxpage.imgReset()
            this.$apply()
          },
          loadimage(e){
            console.log("图片加载完成",e);
          },
          // 上传收款码
          upload(type) {
            let that = this
            this.type = type
            this.cropper.$wxpage.setWidth(this.width)
            this.cropper.$wxpage.setHeight(this.height)
            this.cropper.$wxpage.imgReset()
            this.cropper.$wxpage.setCutCenter();
    
            wx.chooseImage({
              count: 1,
              sizeType: ['original', 'compressed'],
              sourceType: ['album', 'camera'],
              success: function success(res) {
                var tempFilePaths = res.tempFilePaths[0];
                that.cropper.$wxpage.pushImg(tempFilePaths);
                that.show_cropper = true;
                that.$apply();
                wx.showLoading({
                  title: '加载中...'
                });
              }
            });
          },
          async finish_cut() {
            this.cropper.$wxpage.getImg(async (e) => {
              try {
                // 获取选择的图片
                let image = e.url
    
               	
              } catch (err) {
                console.log(err)
                wepy.showModal({
                  title: '提示',
                  content: '服务器错误，请联系管理员'
                })
              }
    
              this.show_cropper = false
              this.$apply()
            })
          },
        
        }

    events = {
    }
  }
</script>
```

## 参数说明<font color=#C39178 size=2>（高亮属性表示对于上个版本有修改，请注意）</font>
| 属性           | 类型   | 缺省值  | 取值  | 描述  | 必填 |
| ------------- |:------:|:------:|:-----:|:-----:|:-----:|
| imgSrc      	| String | 无	   |无限制|图片地址(如果是网络图片需配置安全域名)|否|
| `borderColor` | String | 'white'	   |无限制| 裁剪框的颜色 |否|
| disable_rotate| Boolean| false    |true/false|禁止用户旋转(为false时建议同时设置limit_move为false)|否|
| limit_move	| Boolean| false    |true/false|限制图片移动范围(裁剪框始终在图片内)(为true时建议同时设置disable_rotate为true)|否|
| width 	| Number | 200      |超过屏幕宽度自动转为屏幕宽度|裁剪框宽度|否|
| height        | Number | 200      |超过屏幕高度自动转为屏幕高度|裁剪框高度|否|
| max_width 	| Number | 300      |裁剪框最大宽度|裁剪框最大宽度|否|
| max_height        | Number | 300      |裁剪框最大高度|裁剪框最大高度|否|
| min_width 	| Number | 100      |裁剪框最小宽度|裁剪框最小宽度|否|
| min_height        | Number | 100      |裁剪框最小高度|裁剪框最小高度|否|
| disable_width 	| Boolean | false      |true/false|锁定裁剪框宽度|否|
| disable_height    | Boolean | false      |true/false|锁定裁剪框高度|否|
| disable_ratio    | Boolean | false      |true/false|锁定裁剪框比例|否|
| export_scale  | Number | 3        |无限制|输出图片的比例(相对于裁剪框尺寸)|否|
| quality 	| Number | 1        |0-1|生成的图片质量|否|
| ~~cut_top~~ 	| Number | 居中     |始终在屏幕内 |裁剪框上边距|否|
| ~~cut_left~~ 	| Number | 居中     |始终在屏幕内 |裁剪框左边距|否|
| `img_width` 	| Number | 宽高都不设置，最小边填满裁剪框 |支持%(不加单位为px)(只设置宽度，高度自适应)|图片宽度|否|
| `img_height` 	| Number | 宽高都不设置，最小边填满裁剪框 |支持%(不加单位为px)(只设置高度，宽度自适应)|图片高度|否|
| scale 	| Number | 1	   |无限制|图片的缩放比|否|
| angle 	| Number | 0	   |(limit_move=true时angle=n*90)|图片的旋转角度|否|
| min_scale 	| Number | 0.5	   |无限制|图片的最小缩放比|否|
| max_scale 	| Number | 2	   |无限制|图片的最大缩放比|否|
| bindload 	| Function | null   |函数名称|cropper初始化完成|否|
| bindimageload | Function | null  |函数名称|图片加载完成,返回值Object{width,height,path,type等}|否|
| bindtapcut 	| Function | null  |函数名称|点击中间裁剪框,返回值Object{src,width,height}|否|
## 函数说明
| 函数名         | 参数   	       | 返回值  |描述|参数必填|
| ------------- |:------:	   |:------:|:------:|:------:|
| upload      	|  无    	  |   无   |调起wx上传图片接口并开始剪裁|否|
| pushImg       |  src   	   |   无   |开始裁剪图片|是|
| getImg        |Function(回调函数) |   `Object{url,width,height}`  |获取裁剪之后的图片(图片尺寸 = 图片宽高 * export_scale)|是|
| ~~setCutXY~~     	|  X、Y  	  |   无    |设置裁剪框位置|是|
| setCutSize    |  width、height   |   无    |设置裁剪框大小|是|
| setCutCenter  |  无   	   	  |   无    |设置裁剪框居中|否|
| setScale      |  scale   	   |   无    |设置图片缩放比例（不受min_scale、max_scale影响）|是|
| setAngle      |  deg   	   |   无    |设置图片旋转角度（带过渡效果）|是|
| setTransform  |{x,y,angle,scale,cutX,cutY}|   无    |图片在原有基础上的变化(scale受min_scale、max_scale影响)|根据需要传参|
| imgReset      |无	          |   无    |重置图片的角度、缩放、位置(可以在onloadImage回调里使用)|否|
| setWidth      | width	          |   无    | 设置图片宽度 |否|
| setHeight      | height	          |   无    | 设置图片高度 |否|

[![996.icu](https://img.shields.io/badge/link-996.icu-red.svg)](https://996.icu)
[![LICENSE](https://img.shields.io/badge/license-Anti%20996-blue.svg)](https://github.com/996icu/996.ICU/blob/master/LICENSE)

## 联系我们

<html>
<img width='200'  src='https://bill-split.ergou.live/images/contact.jpeg'/>
</html>
