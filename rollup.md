### 一. rollup是什么：
    rollup是一个JavaScript的模块化打包工具，可以帮助我们编译小的代码到一个大的、复杂的代码中，比如一个库或者一个应用程序。

### 二. rollup和webpack的区别：
    1.rollup默认只能打包ES module，想要打包commonjs，需要额外使用插件；
    2.webpack可以通过loader处理各种非js文件，而rollup主要针对js文件打包，例如主要用rollup打包一个vue库，但是rollup也可以处理其他文件如css、vue等，
       所以也是可以打包项目程序，例如vite就是用rollup打包项目；
    3.rollup的配置比webpack简单，容易理解；
    4.webpack早期不支持tree shaking，而rollup是支持的；

### 三.rollup对各个平台的输出格式：
    1.iife：立即执行函数，针对浏览器，给window添加对应的库；
    2.amd：使用amd的环境，很少用；
    3.cjs：commonjs环境使用；
    4.umd：通用环境，以上3个环境都能使用；

### 四.配置文件：
    module.exports = {
        // 入口文件，也可以使用多入口；
        input: "./src/index.js", 
        // 出口文件, 也可以输出多个，设置为不同的format，把output设置为数组
        output: {
            file: './dist/bundle.js', // 输入路径和文件名；
            name: 'vue', // 库的名字；
            format: 'umd', // 输入文件支持的环境，有cjs、es、iife、umd、amd等；
            plugins:[ commonjs() ], 
            externals:[ 'lodash' ], // 不打包这里的第三方库
            globals:{
                lodash: '_'，// 不打包lodash，就告诉rollup外部依赖使用cdn导入的'_'
            }
        }
    }

### 五.插件的使用：
    1. rollup只支持es module打包，为了使用commonjs，需要用到以下插件：
        @rollup/plugin-commonjs
    2. 想要使用node_modules的插件，需要用以下插件：
        @rollup/plugin-node-resolve
    3.es6转es5，babel代码转换，跟webpack的配置文件一样，同时需要在babel里添加选项{babelHelpers: 'bundled'}：
        @rollup/plugin-babel
    4.对js进行压缩：
        @rollup/plugin-terser
    ******只要使用以上的插件就可以对一个库打包了，对项目打包后续还需要其他插件。*******

    5.对css进行打包，这里不仅有postcss的功能，还有分离css、preset等功能：
       rollup-plugin-postcss
    6.对vue打包：
       rollup-plugin-vue
      打包vue后会报错，是因为使用了process.env.NODE_ENV，但是浏览器没有process.env.NODE_ENV，所以要配合以下插件使用：
       @rollup/plugin-replace，这个跟webpack的DefinePlugin一样，可以定义全局的变量，直接定义process.env.NODE_ENV: `'production'`
    7.启动本地服务器：
      rollup-plugin-serve
      修改文件后重新刷新：
      rollup-plugin-livereload