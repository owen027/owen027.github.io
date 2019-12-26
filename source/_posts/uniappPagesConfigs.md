---
title: uniapp的路由配置
date: 2019-12-25 14:36:18
categories:
- notes
tags:
- uniappPages
---
`uniapp`通过根目录中的`pages.json`文件决定页面文件的路径、窗口样式、原生导航栏、底部原生tarBar等

## 配置项列表

|属性|值类型|是否必选|描述|兼容|
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

|属性|值类型|是否必选|默认值|说明
|:-:|:-:|:-:|:-:|:-:|
|width|String|否|中间按钮的宽度，tabBar中其它项为减去此宽度后平分，默认值为与其它项平分|
|height|String|否|中间按钮的高度|
|text|String|否|中间按钮的文字|
|iconPath|String|否|中间按钮的图片路径|
|iconWidth|String|否|图片宽度|
|backgroundImage|String|否|背景图|

mindButton 没有pagePath,需监听点击事件，自行处理点击后的逻辑，监听事件：`uni.onTabBarMidBottonTap`[详情](https://uniapp.dcloud.io/api/ui/tabbar?id=ontabbarmidbuttontap)，此事件仅支持 App

#### tabBar常见问题 [详情](tabbar常见问题)
- tabBar在各个平台的默认高度不一，App在HBuilderX 2.3.4起从56px调整为50px,
- 通过`JS API`实现动态修改[tabBar](https://uniapp.dcloud.io/api/ui/tabbar)
- JS代码跳转至tabBar页面，API 只能使用`uni.switchTab`,不能使用`uni.navigateTo`、`uni,rediretTo`,使用 `navigator`组件跳转时须设置属性`open-type="switchTab"`
- 原生tabBar有且只有一个在首页，二级页如需Tab,须自行实现
- 如需先登录，后进入tab页面，无须将登录页设为首页，可参考HBuilderX新建项目时的登录模板

 ```js
"tabBar": {
    "color": "#7A7E83",
    "selectedColor": "#3cc51f",
    "borderStyle": "black",
    "backgroundColor": "#ffffff",
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
    }]
}
 ```

### condition

启动模式配置，仅开放环境有效，模拟直达页面的场景，（如：小程序转发后，用户点击所打开的页面）

|属性|值类型|是否必选|描述|
|:-:|:-:|:-:|:-:|
|current|Number|是|当前激活的模式，list节点的索引值|
|list|Array|是|启动模式列表|

#### list

|属性|值类型|是否必选|描述|
|:-:|:-:|:-:|:-:|
|name|String|是|启动模式名|
|path|String|是|启动页面路径|
|query|String|否|启动参数，可在onLoad中获取|

```js
"condition": { //模式配置，仅开发期间生效
    "current": 0, //当前激活的模式（list 的索引项）
    "list": [{
            "name": "swiper", //模式名称
            "path": "pages/component/swiper/swiper", //启动页面，必选
            "query": "interval=4000&autoplay=false" //启动参数，在页面的onLoad函数里面得到。
        },
        {
            "name": "test",
            "path": "pages/component/switch/switch"
        }
    ]
}
```

### subPackages
分包加载配置**此配置为小程序的分包加载机制**
- 微信、百度每个分包大概2M，总共不能超过8M
- 支付宝每个分包大概2M，不能超过4M
- **subPackages 的pages路径为 `root`下的相对路径**
- [分包优化详情](https://uniapp.dcloud.io/collocation/manifest?id=%e5%85%b3%e4%ba%8e%e5%88%86%e5%8c%85%e4%bc%98%e5%8c%96%e7%9a%84%e8%af%b4%e6%98%8e)
- 对于`vendor.js`过大情况可使用运行是压缩代码：HBuilderX创建的项目勾选 运行->运行到小程序模拟器->运行时是否压缩代码；cli项目可在 `packge.json`中添加参数 `--minimize`

|属性|值类型|是否必选|描述|
|:-:|:-:|:-:|:-:|
|root|String|是|子包根目录|
|pages|Array|是|子包由哪些页面组成参数同 `pages`|

```js
{
    "pages": [{
        "path": "pages/index/index",
        "style": { ...}
        }, {
        "path": "pages/login/login",
        "style": { ...}
        }
    ],
    "subPackages": [{
        "root": "pagesA",
        "pages": [{
            "path": "list/list",
            "style": { ...}
            }]
        },

        {
        "root": "pagesB",
        "pages": [{
            "path": "detail/detail",
            "style": { ...}
            }]
        }
    ],
    "preloadRule": {
        "pagesA/list/list": {
            "network": "all",
            "packages": ["__APP__"]
        },
        "pagesB/detail/detail": {
            "network": "all",
            "packages": ["pagesA"]
        }
    }
}

```

### preloadRule

分包预加载配置。

|属性|值类型|是否必选|描述|
|:-:|:-:|:-:|:-:|
|packages|Array<String>|是|进入页面后预下载分包的root或name|
|network|String|否|在指定网络下预下载，all(不限网络)、wifi(默认仅WiFi下预下载)|



