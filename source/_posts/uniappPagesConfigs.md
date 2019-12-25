---
title: uniapp的路由配置
date: 2019-12-25 14:36:18
categorise:
- notes
tags:
- uniappPages
---
`uniapp`通过根目录中的`pages.json`文件决定页面文件的路径、窗口样式、原生导航栏、底部原生tarBar等

## 配置像列表

|属性|类型|是否必填|功能|兼容|
|:-:|:-:|:-:|:-:|:-:|
|[globalStyle](https://uniapp.dcloud.io/collocation/pages?id=globalstyle)|Object|否|设置默认页面的窗口表现,可设置应用的状态栏、导航条、标题、窗口背景色等。|
|[pages](https://uniapp.dcloud.io/collocation/pages?id=pages)|Object,Array|是|设置页面的路径及窗口表现|
|[easycom](https://uniapp.dcloud.io/collocation/pages?id=easycom)|Object|否|组件自动引入规则|2.5.0+|
|[tabBar](https://uniapp.dcloud.io/collocation/pages?id=tabBar)|Object|否|设置底部tab的表现|
|[condition](https://uniapp.dcloud.io/collocation/pages?id=condition)|Object|否|启动模式配置|
|[subPackages](https://uniapp.dcloud.io/collocation/pages?id=subPackages)|Object,Array|否|分包加载配置|
|[preloadRule](https://uniapp.dcloud.io/collocation/pages?id=preloadRule)|Object|否|分包预下载规则|微信小程序|
|[workers](https://uniapp.dcloud.io/collocation/pages?id=workers)|Scring|否|Worker 代码放置目录|微信小程序|

```js
{
    "pages": [{ // pages节点的第一项为应用入口页（即首页）,应用中新增/减少页面，都需要对 pages 数组进行修改,文件名不需要写后缀，框架会自动寻找路径下的页面资源
        "path": "pages/component/index", // 配置页面路径
        "style": { // 配置页面窗口表现,用于设置对应页面的状态栏、导航条、标题、窗口背景色等。 其配置项与 globalStyle 一致，此配置会覆盖 globalStyle
            "disableScroll": false,//禁用滚动 全局中无此设置 //仅支持微信小程序（iOS）、百度小程序（iOS）
            "navigationBarShadow":{
                //导航栏阴影
                "colorType":"grey" // 支持颜色：grey、blue、green、orange、red、yellow
            },
        }
    }, {
        "path": "pages/API/index",
        "style": {
            "navigationBarTitleText": "接口"
        }
    }, {
        "path": "pages/component/view/index",
        "style": {
            "navigationBarTitleText": "view"
        }
    }],
    "condition": { //模式配置，仅开发期间生效
        "current": 0, //当前激活的模式（list 的索引项）
        "list": [{
            "name": "test", //模式名称
            "path": "pages/component/view/index" //启动页面，必选
        }]
    },
    "globalStyle": {
        "navigationBarTextStyle": "black", // 导航栏标题颜色及状态栏前景颜色，仅支持 black/white

        "navigationBarTitleText": "演示", // 导航栏标题文字内容

        "navigationBarBackgroundColor": "#F8F8F8", // 导航栏背景色

        "backgroundColor": "#F8F8F8",
        
        "navigationStyle":"default", // 导航栏样式，仅支持 default/custom。custom即取消默认的原生导航栏 //仅支持 微信小程序 7.0+、百度小程序、H5、App（2.0.3+）

        "backgroundColor":"#ffffff", //窗口背景色 //仅支持微信小程序

        "backgroundTextStyle":"dark", // 下拉loading样式，仅支持dark/light //仅支持微信小程序

        "enablePullDownRefresh":false,// 是否开启下拉刷新

        "onReachBottomDistance":50,// 上拉触底事件触发时距离页面底部距离，单位仅支持px

        "backgroundColorTop":"#ffffff",// 顶部窗口的背景色（bounce回弹区域）//仅支持iOS

        "backgroundColorBottom":"#ffffff",// 底部部窗口的背景色（bounce回弹区域）//仅支持iOS

        "titleImage":"", // 导航栏图片地址（替换当前文字标题），支付宝小程序内必须使用https的图片链接地址 //仅支持支付宝小程序、H5、APP

        "transparentTitle":"none", // 导航栏透明设置。支持 always 一直透明 / auto 滑动自适应 / none 不透明//仅支持支付宝小程序、H5、APP

        "titlePenetrate":"NO",// 导航栏点击穿透

        "pageOrientation":"portrait", // 横屏配置，屏幕旋转设置，仅支持 auto/portrait/landscape // 仅支持App 2.4.7+、微信小程序

        "animationType":"pop-in", // 窗口显示的动画效果 // 仅支持App

        "animationDuration":300, // 窗口动画的持续时间 // 仅支持App

        "app-plus":{//编译到APP 平台的特定样式 详情见uniapp官网 https://uniapp.dcloud.io/collocation/pages?id=app-plus // 仅支持App
        },

        "h5":{//编译到h5 平台的特定样式 // 仅支持h5
        },

        "mp-alipay":{//编译到mp-alipay 平台的特定样式 // 仅支持mp-alipay 支付包
        },

        "mp-weixin":{//编译到mp-weixin平台的特定样式 // 仅支持微信小程序
        },

        "mp-baidu":{//编译到"mp-baidu 平台的特定样式 // 仅支持百度小程序
        },

        "mp-toutiao":{//编译到mp-toutiao 平台的特定样式 // 仅支持字节跳动小程序
        },

        "mp-qq":{//编译到mp-qq 平台的特定样式 // 仅支持QQ小程序
        },

        "usingComponents":{ // 引用小程序组件
            "collapse-tree-item":"/components/collapse-tree-item"
        },
    "pageOrientation": "portrait"//横屏配置，全局屏幕旋转设置(仅 APP/微信/QQ小程序)，支持 auto / portrait / landscape
    },
    "tabBar": {
        "color": "#7A7E83",
        "selectedColor": "#3cc51f",
        "borderStyle": "black",
        "backgroundColor": "#ffffff",
        "height": "50px",
        "fontSize": "10px",
        "iconWidth": "24px",
        "spacing": "3px",
        "list": [{
            "pagePath": "pages/component/index",
            "iconPath": "static/image/icon_component.png",
            "selectedIconPath": "static/image/icon_component_HL.png",
            "text": "组件"
        }, {
            "pagePath": "pages/API/index",
            "iconPath": "static/image/icon_API.png",
            "selectedIconPath": "static/image/icon_API_HL.png",
            "text": "接口"
        }],
        "midButton": {
            "width": "80px",
            "height": "50px",
            "text": "文字",
            "iconPath": "static/image/midButton_iconPath.png",
            "iconWidth": "24px",
            "backgroundImage": "static/image/midButton_backgroundImage.png"
        }
    },
  "easycom": {
    "uni-(.*)": "@/components/uni-$1/uni-$1.vue"
  }
}

```
### 自定义导航栏使用注意事项
当 `navigationStyle":custom`或`titleNView:false`时，原生导航栏不显示同时需注意：
- 非H5段，手机顶部状态栏区域会被页面内容覆盖。`uniapp`提供状态栏css变量，可使用view占用其位置
- 如果原生导航不满足要求，可使用自定义导航栏 [NavBar](https://ext.dcloud.net.cn/plugin?id=52)
- 导航栏搭配原生下拉刷新问题：微信小程序ios端需要拉更长才能看到下来刷新，Android则从屏幕底部下拉，无法从状态栏下拉，如需，请前端模拟， [推荐插件](https://ext.dcloud.net.cn/plugin?id=343)
- 非H5段，前端导航盖不住原生组件，如页面有viedo,map等原生组件，滚动时会覆盖导航栏，小程序可使用`cover-view`来解决，APP可使用titleNView 或subNVue
- 前端组件渲染不如原生导航，原生导航可保证动画期间不白屏
- 页面禁用原生导航后想，改变状态栏前景字体，可设置`navigationBarTextStyle`,部分低端手机不支持

鉴于以上问题，原生导航等满足业务需求，使用原生导航，甚至可牺牲一些不重要的需求。[更多详情](https://ask.dcloud.net.cn/article/34921)

### easycom 配置说明
- 通过正则匹配来自动引入组件
- 此方式引入的组件无需在页面内`import`,也无需在`components`中声明，即可在页面中使用
- 组件名完全一致的情况下，优先级低于手动
- 修改easycom 不会重新编译，须手动改动页面触发

```js
"easycom": {
  "uni-(.*)": "@/components/uni-$1/uni-$1.vue"
}
```
### tabBar 配置说明
- position为top时 不显示ICON, top值仅支持微信小程序
- tabBar中list是数组最少配置2个，最多5个tab,按数组顺序排列
- 切换第一次加载时可能渲染不及时，可在每个Tabbar页面的onLoad生命周期中先弹出一个loading
- 页面出现过后，保存在内存中，切换tabbar,之后触发onShow,不在触发onLoad
  
|属性|是否必选|值类型|默认值|描述|平台差异|
|:-:|:-:|:-:|:-:|:-:|:-:|
|color|是|HexColor||tab字体默认颜色||
|selectedColor|是|HexColor||选中颜色||
|backgroundColor|是|HexColor||背景色||
|borderStyle|否|String|black|边框颜色仅支持black/white| App最新版支持其它颜色|
|list|是|Array||tab列表最少2，最多5||
|position|否|String|bottom|位置 bottom\top|仅微信支持top|
|fontSize|否|String|10px|字体大小|App2.3.4+|
|iconWidth|否|String|24px|icon默认宽度|App2.3.4+|
|spacing|否|String|3px|图标与文字间间距|App2.3.4+|
|height|否|String|50px|默认高度|App2.3.4+|
|mindButton|否|Object||中间按钮仅在list为偶数时生效|App2.3.4+|

#### list 属性说明
list数组中的子项都为对象，其属性为：
|属性|值类型|是否必选|说明
|:-:|:-:|:-:|:-:|
|pagePath|String|是|页面路径，须在pages中选定义|
|text|String|是|tab是按钮文字，H5、5+APP平台非必填|
|iconPath|String|否|图片路径，大小40kB,尺寸81*81，只支持静态图片，不支持字体图|
|selectedIconPath|String|否|选中时的图片|

#### midButton

