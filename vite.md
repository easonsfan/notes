### vite是一个构建工具，和webpack、rollup差不多，而不是一个脚手架

### 2个脚手架：
    create-vite：可以创建各种框架的项目
        npm create vite
    create-vue：创建vue项目
        在创建的过程中，某个选项customize with create-vue是使用create-vue创建vue项目
    使用命令过程中会安装这些脚手架

### vite的特点：
    浏览器支持原生js的优点：
        1. 现在一般的浏览器本身可以支持原生es6或者更高级的代码；
    浏览器支持原生js的缺点：
        1. 其他的jsx、vue等是不支持的；
        2. 路径要写后缀；
        3. 如果一个模块里引入了很多模块，例如一个第三方库lodash，这个lodash的其他所有模块文件也会被加载(多达几百个模块)。
    vite可以解决以上缺点的同时，利用优点来提高开发速度：
        1. 在请求这些vue、ts文件时，开发服务器经过esbuild编译后，返回js内容给浏览器；
        2. 路径可以不用写后缀
        3. 经过esbuild预构建后，多个模块会合并到一个文件中
        预构建依赖：
            1. 对于commonjs或者umd方式导入，都会通过esbuild预构建成es module导入；
            2. 如果直接用import { someMethod } from 'my-dep'导入模块的，浏览器不会直接找node_modules下的模块，所以需要改变导入路径，把'my-dep'改成
                如 /node_modules/.vite/deps/my-dep.js?v=f3sf2ebd，这样的路径浏览器就可以识别。
            3. 有一些使用es module的依赖，它又依赖特别多的内部模块，例如lodash-es，这时使用import { debounce } from 'lodash-es'导入，因为debounce也会依赖很多内部模块，所以浏览器会加载特别多的文件，
                通过预构建，只需要加载一个文件即可
            注意：这种预构建只在开发模式使用，生产模式通过插件@rollup/plugin-commonjs把commonjs转换为es module的

### vite开发服务器底层原理：
    启动的服务器是使用connect库，ts文件等非原生js文件通过esbuild转换为js

### vite开发时编译速度快的原因：
    1. vite尽可能少的编译文件，使得编译速度变快，而且不会打包，不像webpack一样把es6全部转成es5，还要打包
    2. 主要是使用esbuild进行编译，esbuild是一个编译工具

    esbuild的特点：
        1. 超快的构建速度，并且不需要缓存；
        2. 支持ES6和CommonJS的模块化；
        3. 支持ES6的Tree shaking；
        4. 支持Go、JavaScript的API；
        5. 支持typescript、JSX等语法编译；
        6. 支持sourcemap；
        7. 支持代码压缩；
        8. 支持扩展其他插件；

### esbuild为什么快：
    1. esbuild是用go语言编写的，可以直接转换成机器代码，而无需经过字节码；
    2. esbuild可以充分利用CPU的多内核；
    3. esbuild所有内容都是从零开始编写的，没有使用第三方，所有一开始就考虑各种性能问题；

### 生产环境打包为什么不用esbuild，而用rollup：
    因为esbuild针对代码分割和css处理等方面不是很成熟，仍在开发当中，以后可能会在生产构建中使用esbuild

### vue2要使用volar的话，需要在项目根目录.vscode的extensions.json文件配置：
    {
        "recommendations"：["Vue.volar"]
    }

### 配置
    1.共享配置（开发和生产都可以用）
        base 等于webpack的 output.publicPath：设置访问的URL路径
        define 等于webpack的 definePlugin插件功能：设置一些全局变量
        pulicDir 等于webpack的 devServer.static.directory：用来配置访问静态资源的位置，例如访问favicon.ico，默认都是public文件夹
