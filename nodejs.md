### 一. nodejs全局对象：
    1. global、process、module、exports、require 、__dirname、__filename、Buffer等
    2. 进程对象(整个node程序的进程)process：
         process.argv：是一个数组，索引0的位置是node.exe的文件夹路径，索引1的位置是要执行文件的文件夹路径，后面的元素是执行node命令时传进来的参数。
    3. nodejs的global和浏览器的window，都可以用globalThis访问

### 二. 模块化
    1. nodejs模块化规范CommonJS：
        1. 具体实现：导出通过module.exports，exports; 导入通过require；
        2. 为什么要用2种导出方式，因为规范中没有module.exports, 只有exports, 但是node又实现了Module类来实现模块化，所以通过exports = module.exports,
          既能让模块导出，又实现了规范的exports, 实际开发中用module.exports导出；
        3. 在CommonJS里可以修改导入的内容，但是不建议修改，在ESModule中是不可以修改的；
        4. 模块第一次导入时会执行一次内部代码，之后再导入内部代码不会再执行，因为该模块被缓存了，可以通过module.loaded判断是否缓存了    
    2. 浏览器模块化ESModule：
        1. 如果html要使用ESModule，需要在script标签设置属性type="module"，且必须要启动一个服务才能使用；
        2. ESModule导入导出的不是对象，只是标识符；

### 三. 包管理
    1. package.json文件配置项：(npm init手动生成，npm init -y自动生成)
        name: 项目名称，（必填）
        version: 版本号，（必填）
        main：项目入口，
        description：描述，
        author：作者，
        keywords:数组，用来做搜索，
        license：开源协议，
        private：是否私有项目，
        scripts：npm执行的脚本命令，优先到node_modules的.bin下查找对应的工具执行命令，
        dependencies：生产依赖，对于插件和库，安装它们时会同时安装它们package.json中配置所需要的依赖
        devDependencies：开发依赖，
        peerDependencies：对等依赖，这个项目必须依赖其他的库才能运行，
        engines：用于指定node和npm的版本，安装这个项目前会先检查版本，如果不符合会报错，
        browserslist：打包后的JavaScript要兼容的浏览器版本，webpack打包可能用到，这个属性可以用.browserslistrc文件来代替，
        homepage：项目首页地址，
        repository：仓库地址，值为一个对象，例如：{type: "git", url: "github地址"}

    2. 依赖的版本号管理：
        版本号x.y.z写死：以后只会下载这个版本号；
        版本号用^x.y.z：保持x不变，y和z都可以是最新的；
        版本号用~x.y.z：保持x和y不变，z可以是最新的；
        一般来说都是用^x.y.z；
        虽然使用^x.y.z, 在npm install时有可能会安装新的版本，但是实际项目会用package-lock.json来锁定每个依赖的版本

    3. npx：用来执行命令的。优先到node_modules的.bin文件夹下找到对应的工具执行命令，找不到就通过远程的npm包来执行。
        如果不使用npx，直接用工具执行命令，windows操作系统会先在当前文件夹查找是否有可以执行命令的工具，如果没有就查找全局的环境变量，还没找到就报错

    4. 使用npm发布自己的包：（以下命令都要在对应目录下输入）
        4.1 在npm官网注册账号；
        4.2 在当前要发布的包的目录下登录，npm login；
        4.3 设置好package.json;
        4.4 发布到npm registry上，npm publish；
            如果更新了包，需要修改完版本号后再重新发布，npm publish；
            删除包： npm unpublish；
            让发布的包过期：npm deprecate；

### 四. pnpm：
    1. pnpm(performant npm): 速度快、节省磁盘空间的软件包管理器
        1. 原来的npm/yarn/cnpm的问题：在磁盘有多个项目时，每个项目的node_modules会占磁盘容量，想节省容量需要把node_modules文件夹删除，pnpm可以解决这个问题
        2. 硬链接：文件与底层的文件数据的链接，叫做硬链接。
          电脑上的复制，实际上会在文件系统创建一个新的文件数据，与之前的文件数据无关，它们有各自的硬链接。
          mklink /H 新的硬链接(一个文件)  源文件， windows使用这个命令可以创建新的硬链接；如果源文件被删除，新的硬链接不会有影响。
        3. 软链接或者叫符号链接：引用硬链接的路径名。其实就是快捷方式。
          mklink  新的软链接 源文件，windows使用这个命令可以创建新的软链接；如果源文件被删除，新的软链接会找不到硬链接。
        4. pnpm原理：每个项目用到相同的包时，把包通过新建硬链接的方式，放到对应的项目里，这样相同的包通过硬链接就只存在一份，从而节省磁盘空间
            使用pnpm下载依赖的node_modules, 硬链接和软链接都有使用。

    2. 扁平化包管理(npm、yarn):
        node_modules中各个插件用到相同的依赖时，会把依赖放到node_modules顶层，即跟插件同一层。产生的问题是这些依赖也可以被项目使用了
       非扁平化包管理pnpm：
        node_modules下有.pnpm文件夹存放硬链接(就是插件本身)，除此之外就是各种插件的软链接，链接到.pnpm中的硬链接；
        这些硬链接下又存在node_modules, 里面存放插件，以及这些插件的依赖，而这些依赖又是软链接，链接到.pnpm的硬链接
        幽灵依赖：
            在使用npm时，某些插件会引用一些不在package.json记录的其他依赖(这些依赖通常是跟插件一起安装的)，这些依赖被称为幽灵依赖，造成的问题就是没有安装但是却引用了这些依赖，会让人产生困惑。
            因为npm使用的是扁平化管理，幽灵依赖在顶层可以被引入，但是pnpm是非扁平化管理，不存在幽灵依赖情况，因为依赖不在node_modules顶层，没有办法引入，但是有些插件确实需要这些幽灵依赖，
            所以一般使用pnpm时，就要把幽灵依赖当成项目依赖安装进来。
    3. 命令：
        pnpm install：安装全部依赖；
        pnpm add：安装依赖；
        pnpm remove：删除依赖；
        pnpm update：更新依赖，加--latest把依赖升级到最新版本
        pnpm <cmd>：执行脚本命令，pnpm dev、pnpm build
        pnpm store path：获取store的文件夹路径；
        pnpm store prune：清除依赖中没有用的文件

### 五. path模块：专门用来处理文件路径的模块
    path.dirname()：根据路径获取文件的所在目录；
    path.basename(): 获取文件名；
    path.extname(): 获取文件扩展名；
    path.join(): 单纯拼接传入的路径；
    path.resolve(): 拼接传入的路径，并且一定返回绝对路径；
    不管是join还是resolve，都是从右到左开始拼接，而且会根据文件路径的逻辑拼接，不是单纯的字符串拼接