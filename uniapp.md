### 一. uniapp运行在真机或者模拟器
    1. 运行在模拟器
        使用adb：hbuilder默认安装了adb，安装路径是hbuilder位置的plugins/launcher/tools/adbs下。在命令行工具使用命令 'adb connect 模拟器的ip地址'，
                       来连接模拟器，然后就可以用hbuilder来运行程序
        注意：雷电模拟器默认开启了adb，只需在hbuilder里刷新列表就可以连接 2023-07-16
    2. 真机调试
        用数据线连接手机，手机打开开发者模式，然后在hbuilder检测手机连接即可

### 二. uniapp项目目录
    unpackage：在运行或者发行时打包好的文件，存放在这个文件夹；
    manifest.json：配置这个程序的全局配置的文件，例如这个app的图标和名字、权限等配置，还有微信小程序开发时填写appid，最后所有配置会生成源码视图这个json；
    pages.json：页面配置，相当于微信小程序的app.json；
    uni.scss：只能写自定义的全局样式，普通样式是不生效的；
    App.vue：编写所有页面的公共样式，style标签不可以加scoped。其他组件加不加scoped也一样，样式默认是只影响当前组件页面；
    static：存放本地图片、字体、视频等文件，不应该放在assets目录里，tabbar里的图片只能放在static目录下，放到assets会报错；

### 三. 常用内置组件
    // div可以使用，但是不推荐，因为会影响编译速度，而且这个div不是跨平台组件
    1. view组件代替div
    2. text组件代替span
    3. button组件，可以设置type="primary"等属性，但是在小程序和浏览器颜色会有差别。可以通过全局设置同一个颜色；或者自己封装一个button组件
    4. image组件
    5. scroll-view组件 滚动区域
    6. swiper组件 轮播图
**uniapp不能使用 ref 获取 dom 元素，使用 uni.createSelectorQuery().select('.class').boundingClientRect(callback),exec() 获取**

### 四. ui组件库 uni-ui
    以上的组件为基础组件，这个uni-ui的组件为扩展组件
    1. uni-ui的特点：
        1. 高性能
        2. 全端支持
        3. 风格扩展：支持用uni.scss自定义样式
    2. 使用uni-ui的方法：
        1. 创建项目时选择uni-app的uni-ui项目，会把所有的uni-ui都导入进来；
        2. 创建的是非uni-ui项目，如果是按需引入组件，就要先到官网安装组件再导入进来项目；
        3. 创建的是非uni-ui项目，导入全部组件，同样也要安装在导入；
        4. 没有网络时，可以直接复制其他项目的组件使用
    3. 定制uni-ui的主题色：
        在uni.scss文件，先导入uni-ui的默认样式，然后在后面重写对应的变量就好，具体可参考官网对组件的介绍
    4. 重写组件样式：
        直接重写样式，但是需要加important才能覆盖

### 五. 微信小程序背景图片
    1. 不能使用本地图片，要么把图片转成base64，要么使用网络图片；
    大于70kb的图片会报错，需要使用网络图片或者转成base64才可以。小于70kb的图片会自动转成base64。

### 六. 页面跳转
    1. navigator组件
        1. url：为跳转的页面地址；
        2. open-type：跳转的方式，navigate、switchTab等，跟下面api的一样
    2. 通过api方式跳转：
        1. uni.navigateTo：保留当前页面，跳转到应用内的某个页面，使用uni.navigateBack可以返回到原页面；
        2. uni.redirectTo：关闭当前页面，跳转到应用内的某个页面；
        3. uni.reLaunch：关闭所有页面，打开到应用内的某个页面；
        4. uni.switchTab：跳转到 tabBar 页面，并关闭其他所有非 tabBar 页面；
        5. uni.navigateBack：关闭当前页面，返回上一页面或多级页面。可通过 getCurrentPages() 获取当前的页面栈，决定需要返回几层；
        6. uni.preloadPage：预加载页面，是一种性能优化技术。被预载的页面，在打开时速度更快。

### 七. 页面之间传递参数
    正向传递：
        1. 通过在url里加上查询字符串?name=zs&age=18，在目标页面生命周期onLoad的options参数，就可以获取到传递的参数；
        2. 在navigateTo的success方法中，使用参数res的eventchannel.emit方法触发自定义事件然后传递参数，在目标页面生命周期onLoad里使用eventchannel.on绑定的自定义事件获取参数，
            在onLoad需要通过getOpenerEventChannel方法获取eventchannel；
    逆向传递：
        1. 使用navigateBack返回上一页时，使用getOpenerEventChannel方法获取eventchannel，然后使用eventchannel.emit方法触发自定义事件然后传递参数，
            在原来的页面的navigateTo方法中，通过events对象定义一个自定义事件，就可以接收eventchannel.emit传过来的参数
        2. 通过事件总线uni.$on绑定事件，uni.$emit触发事件

### 八. 生命周期
    1. 应用生命周期：
        在App.vue里面的3个生命周期：
            onLaunch、onShow、onHide
    2. 页面生命周期：
        onLoad、onShow等，同时也可以使用组件生命周期，跟vue组件是一样的
        vue2用options API方式写周期就可以；
        vue3的导入写法：组件生命周期从vue导入，页面生命周期从@dcloudio/uni-app导入
    3. 组件生命周期：
        跟vue的生命周期是一样的
        组件中使用页面生命周期：
            1. 在options api时，不支持页面生命周期；
            2. 在composition api时，支持页面生命周期，但是不同端会有差异

### 九. 自定义组件
    1. easycom方式：
        在根目录的 components 文件夹或者在uni_modules的components文件夹里创建组件，
        必须符合 components/组件名称/组件名称.vue 或者 uni_modules/插件ID/components/组件名称/组件名称.vue 的结构(其中插件id就是组件名字，例如uni-badge)
        就可以不用引用、注册，直接在页面中使用
    2. 在 components 或者 uni_modules 目录下的组件，只有使用到的组件才会打包。

### 十. 条件编译
    1. 条件编译的作用：为了在不同平台实现各自独有的特性。
    2.  #ifdef：if defined 仅在某平台存在；
        #ifndef：if not defined 除了某平台均存在；
        %PLATFORM%：平台名称，具体的取值如：VUE3、APP-PLUS、H5、MP-WEIXIN、uniVersion等，详情可以看官网。
    3. 可以编写条件编译的文件：.vue/.nvue/.uvue  .js/.uts .css pages.json 各种预编译文件：.less .scss等
    4. static 目录的条件编译：
        在 static 目录下按各个平台的名字命名的目录，会在打包时只打包该平台对应的目录   

### 十一. uniapp内置了pinia，可以直接使用

### 十二. 第三方插件
    可以直接用 npm 安装后使用，不能用的看插件市场有没有相关插件。

### 十三. 分包和分包预加载规则
    跟 webpack 的按需加载差不多，都是在用到时利用预加载去下载对应的模块或者页面，能优化应用的性能。
    
    项目的目录分配：
     tabbar中的页面需要放到主包中（pages目录中）;
     根目录的components和static放置公共的组件和资源；
     其他分包需要创建自己的目录，目录下可以放置对应自己分包的静态资源static和components
     
    pages.json 的 subPackages 属性配置：
    "pages": [{
    	"path": "pages/index/index",
    	"style": { ...}
    }, {
    	"path": "pages/login/login",
    	"style": { ...}
    }],
    // 分包配置
    "subPackages"：[{
    	"root": "pagesA",
        "name":"list",
    	"pages": [{
    		"path": "list/list",
    		"style": { ...}
    	}]
    }, {
    	"root": "pagesB",
        "name":"detail",
    	"pages": [{
    		"path": "detail/detail",
    		"style": { ...}
    	}]
    }]  
    
    // 要启用预加载，还需要以下配置
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
    key 是页面路径，value 是进入此页面的预下载配置。这种配置的意思是，在进入 pagesB/detail/detail 页面时，就去预加载 pagesA 这个包的页面。

