### 一. webpack命令行方式打包
    1. 没有webpack.config.js时，默认会到当前目录下的src文件夹的index.js作为入口，打包文件输出到src同级的dist目录下，默认名是main；
        通过命令行可以设置入口文件、输出文件、输出文件夹：
        npx webpack --entry ./src/main.js --output-filename bundle.js --output-path ./bundle 
    2. 有webpack.config.js时，直接输入npx webpack就可以根据配置文件打包；
        如果不是这个配置文件名字，则会报错，也可以用一下命令指定配置文件打包：
        npx webpack --config wk.config.js
    3. webpack.config.js的output.path，必须是一个绝对路径

### 二. 通过package.json脚本执行命令
    "scripts":{
        build:"webpack"，
        // build:"webpack --config wk.config.js"，不是用webpack.config.js时可以指定文件打包
    }

### 三. 常用loader：
    1. css-loader：识别js文件中引入的css文件；
    2. style-loader：把引入的文件插入到html文件的style样式表中；
    3. less-loader：处理less文件；
    4. postcss：处理css兼容性、前缀、新特性等，跟babel专门处理js一样。
        1. postcss对应的插件：
            postcss-loader：识别后处理css、less文件，所以在配置时css、less都要加这个插件；
            autoprefixer：给css样式加前缀，但是使用了postcss-preset-env时就不用这个了，因为它里面有这个功能了；
            postcss-preset-env：预设了各种处理css的功能，如处理css兼容性、前缀、新特性等功能，所以用了这个插件后不要用其他重复功能的插件；
        2. 插件可以在postcss-loader的options里面配置，也可以在单独的postcss.config.js里面配置, 在plugins数组里面使用。

    5. 文件资源处理：
        webpack5之前都是有特定的loader处理文件资源，webpack5后通过资源模块类型配置就行了。
        {
            test:/.(png|jpe?g|svg|gif)$/,
            // 1. asset/resource, 代替file-loader, 把所有文件资源都单独打包成一个文件，缺点是不管文件大小，都需要发送网络请求请求资源
            // type: 'asset/resource',
            // 2. asset/inline, 代替url-loader, 所有文件都通过base64编码的方式添加到引用文件里，缺点是都会被打包在js文件中，增加了js文件的体积
            // type: 'asset/inline',
            // 3. asset/source，代替raw-loader, 把文件的源码导出，一般很少用。
            // type: 'asset/source'，
            4. asset, 代替url-loader，可以在单独的文件或者base64之间选择, 一般是用这个配置属性
            type: 'asset',
            parser：{
                dataUrlCondition：{
                    maxSize: 10 * 1024 // 默认为8kb， 超过这个文件大小的就单独打包成一个文件，否则在js中通过base64编码访问。
                }
            }，
            generator：{
                filename：'img/[name][hash:8][ext]'，// 文件打包后输出的目录和文件名，也可以在output属性的assetModuleFilename配置
            }
        }

    6. babel：把ES6以上的代码转换为ES5的代码
        babel-loader：识别处理jsx?文件；
        @babel/preset-env：预设了对ES6进行处理的各种功能，如把箭头函数转为普通函数，把const、let转为var等 
        babel的preset是放在presets属性里的，跟postcss放在plugins不一样。  

    7. vue-loader: 识别处理vue文件，配置这个loader同时，还要添加VueLoaderPlugin插件来处理style标签的样式，这个插件在vue-loader/dist/index中可以引入。
    8. ts-loader：处理ts文件。需要配置tsconfig.json文件，tsc --init 生成配置文件。但是实际开发中不使用ts-loader来处理ts文件，直接用babel的preset：@babel/preset-ts

### 四. resolve解析文件路径：
    extension：默认值['.js', '.json', '.wasm'], // 文件没有写扩展名时，会查找这个属性
    alias:{ '@' : path.resolve(__dirname,'./src') }, // 文件路径设置别名

### 五. 常用插件：
    html-webpack-plugin：生成指定的html模板，template选项可以指定某个目录下的html模板；
    DefinePlugin：定义全局变量，整个项目都可以访问。这是webpack内置的插件，默认有一个process.env.NODE_ENV用来判断开发环境还是生产环境。

### 六. mode：
    development：DefinePlugin会将process.env.NODE_ENV设置为development；
    production：DefinePlugin会将process.env.NODE_ENV设置为production；

### 七. 启动本地开发服务器：
     webpack-dev-server：安装这个插件，在package.json配置脚本命令 webpack serve即可启动本地开发服务器。启动时服务器会把打包好的文件都放到内存里

### 八. 开发配置devServer：
    1. hot：默认为true：
            模块热替换HMR(hot module replace)：
                默认是开启了HMR，如果要设置，可以在devServer中设置hot：true开启HMR，但是这样只会刷新浏览器。然后
                想要局部更新文件，需要通过module.hot.accept(需要HMR的文件路径，callback)设置要热替换的文件才行。
                vue-loader中自带有热替换功能，不过只能针对.vue文件替换，其他文件不会。
                style-loader会有HMR功能。
                vuex也可以配置对应的HMR功能。
       hot：only，当编译失败时，修复错误后一般会重新刷新页面，如果不想重新刷新，可以使用这个值。
    2. host：设置域名，0.0.0.0可以让同一个局域网的其他人连到自己的项目；
    3. port：项目端口；
    4. compress：对静态文件开启gzip压缩，默认为false；
    5. open：启动项目后是否打开浏览器，默认为false；
    6. static：index.html引用静态文件资源时，从这里配置的文件夹读取资源，默认是'public'文件夹。例如把favicon.ico放在public文件夹就是因为这个，然后在index.html引入。
    7. proxy：做跨域处理。
        proxy：{
           '/api'：{
               target：'http://192.168.0.26:8080',
               pathRewrite：{
                   '^/api'：''     // 把请求路径的/api去掉，如果想保留可以不用设置
               },
               changeOrigin：true     // 改变请求头host(前端不能直接设置，但是可以通过服务器设置，这个属性就是告诉本地服务器改变host)，
                                      // 如果不设置为true，默认host是本地服务http://localhost:8083，如果服务器对请求的host校验，会不通过。
           }
        }
    8. historyApiFallback：true。 
        开发时，使用history模式时，在url地址栏访问不存在的路径会报404错误，设置为true时可以返回index.html而不用报404。
        如果是部署的话，就直接在nginx配置location / {  try_files $uri $uri/ /index.html; } 就可以了，vue-router官网有多种解决方法。


### 九. context：
    基础目录，绝对路径，用于从配置中解析入口点(entry point)和 加载器(loader)。
    默认是启动webpack命令的路径，通常是在package.json中执行命令，而package.json是在项目根目录下的，所以entry配置 './src/main.js' 是可以找到文件的。

### 十. devtool：设置source map的方式
    false：不使用source map；
    none：不生成source map，production模式默认使用，而且不能直接设置这个值;
    eval：development模式默认使用，在bundle.js文件中使用eval函数执行源代码，并且添加有//#sourceURL=; 会在调试面板生成source map文件，但是不会跟源文件一样，显示错误的位置也可能不一致。
        构建和重新构建速度除了false或者none之外最快的；
    source-map：生成的source map和源文件一样，但是构建和重新构建速度会很慢；
    eval-source-map：把source map以dataURL的方式添加到eval函数里；
    inline-source-map：把source map以dataURL的方式添加到bundle文件后面；
    cheap-source-map：比source-map更低开销，错误只显示行，不显示列；
    cheap-module-source-map：用loader处理过的源文件，生成source map时会跟源文件一样；
    hidden-source-map：生成source map，但是不会使用；
    nosources-source-map：生成source map，只显示对应错误的提示，但是不能查看源文件；
    最佳实践：
        开发：source-map或者cheap-module-source-map；
        生产：false或者不写；

### 十一. 浏览器兼容性browserslist：
        1. browserslist：是一个在不同前端工具(babel、postcss等工具)之间，共享目标浏览器和nodejs版本的配置。         
        2. .browserslistrc文件配置：
            没有配置这个文件时，默认配置：
              >0.5%，last 2 versions，Firefox ESR，not dead。
            常用配置：
              > 1% // 大于1%使用率的浏览器；
              last 2 versions：浏览器最近的2个版本；
              not dead：dead是指24个月内没有更新或者官方支持的浏览器；
        3. 可以使用or、and、not来设置条件。
        4. 还可以在babel、postcss中的presets中设置targets属性指定browserlist，但是一般不在里面设置

### 十二. stage-x的preset：babel7后不再使用
       js新特性产生的阶段：
       stage-0：稻草人(图纸),
       stage-1：提案，
       stage-2：草稿，
       stage-3：候补，
       stage-4：完成，正式发布。

### 十三. polyfill：
        1. 某些浏览器没有的API或者构造器，例如，Promise、Generator、Symbol等，babel转换后还是一样，所以在这些浏览器会报错，
            这时候就需要通过polyfill实现一个Promise，然后一起打包。
        2. babel 7.4.0以前使用@babel/polyfill，之后就使用core-js和regenerator-runtime来使用，这个是开发和生成都需要的依赖       
        3. 配置polyfill：
            在babel.config.js的presets数组里，@babel/preset-env数组第二个参数对象里配置
            module.exports = {
                presets:[
                    ['@babel/preset-env',{
                        corejs:3, // corejs插件的版本
                        useBuiltIns: 'usage', // 3个参数，false：不使用polyfill，usage：按需使用，entry：针对第三方依赖需要使用的，在入口文件使用import  'corejs/stable';import 'regenerator-runtime/runtime';
                    }]
                ]
            }

### 十四.react和typescript：
       1.处理react的jsx文件和ts文件都可以用babel-loader，而且它们都有对应的babel预设：@babel/preset-react、@babel/preset-typescript；
         @babel/preset-typescript同样的也可以设置polyfill。
       2.用babel-loader处理ts文件时，不会做类型检查，所以处理ts最佳方法是：
         继续使用babel-loader处理ts文件，但是在打包前先使用tsc --noEmit --watch对ts文件做检查，没问题了才打包。（noEmit：不产生编译后的文件）

## 性能优化：
### 一. 代码分离：
    1. 入口entry：
        1.1 entry可以配置单个入口，也可以配置多个入口，也可以以数组把多个文件作为一个入口。
        2.1 多入口重复的依赖共享：
            通过入口配置的dependOn和shared关联，这样可以把多入口文件重复的依赖只打包一份。

    2. 动态导入import()：
        1.1 webapck会把动态导入的模块单独打包，实现代码分离。
        2.1 分包的名字通过output.chunkFilename设置，而且可以通过import(/* webpackChunkName: about*/, 模块路径)设置[name]

    3. splitChunks:
        3.1 默认对异步导入的模块分包，静态导入的本地模块或者第三方依赖不会分包。
        3.2 设置optimization.splitChunks.chunks为all，就可以对异步模块、第三方依赖分包，静态导入的本地模块仍然不会分包的。
            splitChunks的其他属性：
                maxSize：分包出来的文件大小不能大于这个值，否则不会分包；
                minSize：分包出来的文件大小不能小于这个值，否则不会分包；
                cacheGroups：test匹配对应文件夹下的模块，对他们进行分包，只有匹配上，所有模块都能进行分包，前提是chunks=all
                cacheGroups：{
                    vendors：{
                        test：/node_modules/，
                        filename：'[name]_vendors.js'
                    },
                    utils：{
                        test：/utils/，
                        filename：'[name]_utils.js'
                    }
                }

    4. chunkIds：
        1. 可以设置分离出来的模块的文件名，有几个常用值：
            named：对调试更友好的id，也就是文件夹加原来文件名组成的名字，开发环境默认使用；
            natural：按模块使用顺序的数字id，根据对模块的使用改变，数字也会改变。不利于缓存；
            deterministic：只有模块不改变，多次编译也不会改变的id。webpack5才有的属性，有利于缓存，生产环境默认使用。

    5. prefetch和preload：
        1. 配置：import(/*webpackPrefetch:true*/, 模块路径)
            prefetch会在主包下载完之后才会下载，preload会和主包并行下载，一般使用prefetch好一点。

    6. css代码分离：
        使用mini-css-extract-plugin在plugins配置filename，可以设置css文件名和文件夹位置，这样就可以把css从js中分离出来，
        同时在处理css的rule里面使用mini-css-extract-plugin的loader，会把css文件通过link引入html

### 二. CDN服务器：
    CDN，content delivery(distribution) network，内容分发网络。通过相互连接的网络系统，选择使用最靠近用户的服务器把内容返回。
    1. 第一种优化，把自己的项目部署到CDN服务器，需要在output.publicPath配置CDN服务器地址，打包出来的index.html引入的script的src属性会自动加上该地址。
    2. 第二种优化，第三方依赖用CDN引入，不参与打包，在生产环境配置：
        externals：{
            axios：'axios' //  key是项目中导入模块的名字，就是from 'axios'中的axios，value是CDN导入的全局名称
        }
        配置完webpack后，还需要在index.html模板中用script导入CDN中的依赖。
        <script src="https://cdn.bootcdn.net/ajax/libs/lodash.js/4.17.21/lodash.min.js"></script>
        *****注意：就算不通过import引入axios，webpack也可以知道不打包axios，打包后通过CDN导入的全局对象使用axios。

### 三. shimming垫片：了解即可，很少用，以下只是其中一种用法。
    第三方的依赖没有引入axios但使用了axios时，会报错，
    使用ProvidePlugin插件，插入全局变量axios，就可以不用引入axios都能使用了

### 四. 代码压缩：
    1. js代码压缩：
       production模式默认使用TerserPlugin压缩，想自定义配置，需要安装terser-webpack-plugin，且需要把optimization.minimize设置为true，
       TerserPlugin有默认值，最好是设置parallel为true，这个配置可以使打包速度更快。
       terserOptions：这个专门用来配置terser专用的选项，可以查看terser官网详细配置。
    2. css代码压缩：
       使用CSSMinimizerWebpackPlugin进行压缩

### 五. tree-shaking：
    1. js代码的tree-shaking
        1. optimization.usedExports
            对导出但是没有使用的内容添加魔法注释unused harmony export xxxxx，用来告诉TerserPlugin删除哪些没用的js代码，而且compress.unused要设置为true，
            在production模式下，usedExports和unused默认为true，所以可以不用设置
        2. package.json的sideEffects：
            1. 通过es module导入的文件，都可以使用tree-shaking，optimization.sideEffects决定是否启用tree-shaking。
            2. package.json的sideEffects设置为false，说明所有的文件都没有副作用。如import './utils/math.js'或者import {sum} from './utils/math.js'但是没有使用sum的，
                都被认为没有副作用而不会参与打包。
            3. sideEffects可以设置为数组，表示哪些文件是有副作用的，到时也会参与打包。
            4. 如果一个文件没有副作用而被删除，这个文件里正常的导入、使用的其他文件也不会参与打包
            5. 如果一个文件有副作用，但是导入的文件没有副作用，那么这个导入的文件不会参与打包。
    2. css代码的tree-shaking
        purgecss-webpack-plugin：删除没有使用上的样式，例如.title{color:red}，html没有元素有title类名的话，这个.title样式会被删除

### 六. scope hoisting作用域提升(默认production模式已开启)：
     在plugins设置 new webpack.optimize.ModuleConcatenationPlugin()

### 七. 对css和js文件进行gzip压缩：
     使用compression-webpack-plugin插件进行压缩

### 八. 压缩html：
     使用html-webpack-plugin的minify属性压缩

### 九. 打包时间分析：
     speed-measure-webpack-plugin

### 十. 打包后文件分析：
     webpack-bundle-analyzer

## webpack源码：
### 一. 调用webpack()函数，创建compiler对象
    1. 在createCompiler时，创建完compiler就会开始注册插件(插件可以是对象，对象要有apply方法，也可以是函数，都会接收compiler为参数)
### 二. compiler调用run()方法，开始进行编译和打包，传入回调函数查看结果

## 自定义loader：
### 一. loader本质上是一个导出为函数的模块，且必须有string或者buffer类型的返回值；

### 二. webpack是通过loader runner库调用这个函数，并且传入3个参数content、map、meta；

### 三.同步loader和异步loader：
    1. 同步loader只需要用return返回值或者调用this.callback(err,content)，区别是this.callback方法可以传递错误信息；
    2. 异步loader调用this.async()方法获取callback，然后在异步的地方调用callback(err, content)；

### 四. 给loader传参：
    通过options配置参数，在loader里面调用this.getOptions()即可。
