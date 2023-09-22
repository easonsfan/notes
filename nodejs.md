### nodejs是什么：
    nodejs是一个内核使用chrome的v8引擎的JavaScript运行时环境。
    nodejs和浏览器的区别：
        浏览器提供了window、DOM、BOM等API，而nodejs则提供了网络、buffer、fs等模块，对于ECMAscript的规范，2个的实现基本是一样的
    
    nodejs更适合用于I/O密集型的场景(I/O密集型意思是大量进行网页crud、文件读写等)，不适合CPU运算密集型的场景(CPU运算密集型意思是需要大量计算而用到多个线程来处理)，
    因为nodejs是单线程，一般计算是要同步进行，用到多个线程来提升计算效率，耗时的计算会阻塞单个线程。

### 一. nodejs全局对象：
    1. global、process、module、exports、require 、__dirname、__filename、Buffer等
    2. 进程对象(整个node程序的进程)process：
        process.title：进程名称；
        process.pid：进程的pid；
        process.argv：是一个数组，索引0的位置是node.exe的文件夹路径，索引1的位置是要执行文件的文件夹路径，后面的元素是执行node命令时传进来的参数；
        process.stdin：标准输入，程序启动后在命令行输入的内容通过这个api输入；
        process.stdout：标准输出，程序启动后在命令行输入的内容通过这个api输出；
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

### 六. events模块：事件监听模块
    引入events内置模块，创建events实例对象，然后通过这个实例对象进行自定义事件绑定和触发
        const EventEmitter = require('events');
        const myEmitter = new EventEmitter();
        eventEmitter.on('event1',(arg,1,arg2,...)=>{console.log('event1触发')});
        eventEmitter.emit('event1',arg1,arg2,...);
    API:
        on(eventName, listener)：绑定事件，可以为同一个事件添加多个listener；
        prependListener(eventName, listener))：把listener添加到事件对应的listener数组最前面；
        once(eventName,listener)：绑定事件只触发一次；
        emit(eventName, arg1,arg2,...)：触发事件；
        off/removeListener(eventName, listener)：移除单个事件，必须指定事件名称和事件回调名称；
        removeAllListeners(eventName)：有指定事件的，就移除所有指定的事件，没有指定事件的，就移除所有已经绑定的事件；

### 七. event loop：事件循环
![event loop](/assets/event_loop.png "event loop")    
以上是node事件循环的每个阶段队列以及执行顺序。

    1.timers：执行setTimeout和setInterval的回调；
    2.pending callbacks：执行延迟到下一个循环的I/O(文件、网络)回调；
    3.idle，prepare：系统内部使用；
    4.poll：获取新的I/O事件；执行I/O的回调(除了其他阶段的回调以及微任务)，其余情况 node 将在适当的时候在此阻塞；
    说明: 1. 适当的时候：已经没有其它可执行的任务的时候，此时还有异步的I/O没有返回；此时程序会在此阻塞，等待I/O的回调；
          1. 当队列用尽或达到回调限制，事件循环将移动到下一阶段执行，也就是到待定回调pending callbacks阶段执行
    
    5.check：存放setImmediate的回调；
    6.close callbacks：存放sokcet等的close事件回调；

>对于微任务promise和process.nextTick:      
>每个阶段的***每一个***回调执行完，都会去微任务队列执行promise（注意是每一个回调，不是全部回调，因为要跟浏览器的实现同步，每执行一个宏任务，就去清空微任务队列）；      
>process.nextTick是在event loop所有阶段之前就第一个执行的。

### 八. fs模块：文件系统模块
#### 1.文件系统相关知识：     
#### **权限位 mode**：     
文件权限表：    
![权限位](/assets/%E6%9D%83%E9%99%90%E4%BD%8D.awebp "权限位")  

在上面表格中，我们可以看出系统中针对三种类型进行权限分配，即文件所有者（自己）、文件所属组（家人）和其他用户（陌生人），     
文件操作权限又分为三种，读、写和执行，     
文件操作权限用字符表示分别为，r 表示读，w 表示写，x 表示执行，-表示没有对应的权限，    
数字表示为八进制数，具备文件操作权限的八进制数分别为 4 、2、1，不具备权限为0。       

打开一个 git bash，输入命令 ls -al 查询如下文件和文件夹信息：     
**drwxr-xr-x** 1 koala 197121 0 Jun 28 14:41 core          
**-rw-r--r--** 1 koala 197121 293 Jun 23 17:44 index.md       

可以看到最开头的一串字符，一共11位，第一位表示是文件还是文件夹，d表示文件夹，-表示文件，         
第二位开始每3位表示一个权限组，每个权限组里的3个字符分别表示读、写、执行的权限。         

>权限位 mode 主要针对 Linux 和 Unix 操作系统，Window 的权限默认是**可读、可写、不可执行**，所以权限位数字表示为 0o666，转换十进制表示为 438。        

--------------------------------------------------------------------

#### **标识位 flag**：       
| flag   | desc   |
|------- |--------|
| r      |读取文件，如果文件不存在则抛出异常。|
| r+     |读取并写入文件，如果文件不存在则抛出异常。|
| rs     |读取并写入文件，指示操作系统绕开本地文件系统缓存。|
| w      |写入文件，文件不存在会被创建，存在则清空后写入。|
| wx     |写入文件，文件不存在会被创建，如果文件存在则报错，用来避免修改旧文件。|
| w+     |读取并写入文件，文件不存在则创建文件，存在则清空后写入。|
| wx+    |和 w+ 类似，文件不存在会被创建，如果文件存在则报错，用来避免修改旧文件。|
| a      |追加写入，文件不存在则创建文件。|
| ax     |与 a 类似，文件不存在会被创建，如果文件存在则报错，用来避免修改旧文件。|
| a+     |读取并追加写入，不存在则创建。|
| ax+    |与 a+ 类似，文件不存在会被创建，如果文件存在则报错，用来避免修改旧文件。|

r：读；      
w：写；         
a：在原来的内容后追加；         
x：创建一个新的文件，如果文件已经存在，则会报错。如果跟 w 结合，会跟 w 原来功能 '存在则清空后写入' 冲突，并且优先使用 x 的功能，也就是说 wx 在文件存在时会报错，而不是直接清空后写入；             
s: 同步操作；       
+：功能增强，就是如果r只能读，但是r+就能读并且写入，同样w只能写，但是w+就能读并且写。对于操作整个文件的API来说效果可能不明显，但是对于能指定位置操作文件内容的API来说就能看到效果。           

--------------------------------------------------------------------

#### **文件描述符 fd**： 
操作系统会为每个打开的文件分配一个名为文件描述符的数值标识，文件操作使用这些文件描述符来识别与追踪每个特定的文件，             
Window 系统使用了一个不同但概念类似的机制来追踪资源，为方便用户，NodeJS 抽象了不同操作系统间的差异，为所有打开的文件分配了数值的文件描述符。           
通过 fs.open 打开文件就能获取文件描述符 fd       

--------------------------------------------------------------------

#### 2. API 4种风格：以 fs.stat 为例       
    1.callback
        const fs = require('fs')
        fs.stat('.',(err,stats)=>{
            if(err){
                // 处理错误
            }else{
                // 使用 stats
            }
        })
    2.promise
        // const fs = require('fs').promises
        const fs = require('fs/promises')
        fs.stat('.').then(stats=>{
            // 使用 stats
        }).catch(error=>{
            // 处理错误
        })
    3.promisify
        const fs = require('fs')
        const util = require('util')
        const stat = util.promisify(fs.stat);
        stat('.').then(stats => {
            // 使用 stats
        }).catch(error => {
            // 处理错误
        });
    4.同步方法
        const fs = require('fs');
        try {
            const stats = fs.statSync('.');
            // 使用 stats
        } catch(error) {
            // 处理错误
        }

#### 3. fs 常用 API     
    1.获取文件信息
        fs.stat：获取 stats 对象，包含文件的基本信息；
        stats 的一些属性和方法：
            stats.isDirectory()：判断是否为文件夹；
            stats.isFile()：判断是否为文件；
            stats.isSymbolicLink()：判断是否为符号链接（软链接）;
            stats.size：获取文件字节数
        
    2.完整地读写文件的API   
    options一般就是包含encoding，mode，flag的对象。
        1. fs.readFile(filename,[options],[callback(error,data)]
        2. fs.writeFile(filename,data,[options],callback)
        3. fs.appendFile(filename, data, [options], callback)
        4. fs.copyFile(filenameA, filenameB，callback)
        5. fs.unlink(filename, callback)：只能删除文件
    
    3.指定位置读写文件内容的API
       1. fs.open(path,flags,[mode],callback)
       2. fs.read(fd, buffer, offset, length, position, callback);
           1. fd：文件描述符，需要先使用 open 打开，使用fs.open打开成功后返回的文件描述符；
           2. buffer：一个 Buffer 对象，v8引擎分配的一段内存，要将内容读取到的 Buffer；
           3. offset：整数，向 Buffer 缓存区写入的初始位置，以字节为单位；
           4. length：整数，读取文件的长度；
           5. position：整数，读取文件初始位置；文件大小以字节为单位
           6. callback：回调函数，有三个参数 err（错误），bytesRead（实际读取的字节数），buffer（被写入的缓存区对象），读取执行完成后执行。
       3. fs.write(fd, buffer, offset, length, position, callback)
       4. fs.close(fd,callback)    
      
       5. fs.createReadStream(path[, options])
       6. fs.createWriteStream(path[, options])

    4.文件夹操作  
        fs.Dir & fs.Dirent
            fs.Dir 是可迭代的目录流的类，fs.Dirent 的实例对象是遍历 fs.Dir 实例对象获得的当前目录里的内容，可以是文件或子目录
        fs.Dir 实例对象的一些属性和方法：
            dir.path：目录的只读路径；
            dir.read()：按顺序读取迭代器目录里的一个文件或子目录（是一个 Dirent 的实例对象）读完时会返回null。直接for循环遍历dir也是能获取到 Dirent 的实例对象的；
            dir.close()：关闭目录的底层资源句柄；
        fs.Dirent 实例对象的一些属性和方法：
            dirent.name：文件或者子目录的名字；
            dirent.isDirectory()：判断是否为文件夹；
            dirent.isFile()：判断是否为文件；
            dirent.isSymbolicLink()：判断是否为符号链接（软链接）

        1. fs.opendir(path[, options], callback)：打开文件夹，返回一个 Dir 的实例对象；
        2. fs.readdir(path[, options], callback)：读取文件夹，回调的 files 参数是文件夹里文件名的数组，如果 options 里的 withFileTypes 设置为 true，数组里的是 Dirent 的实例对象；
        3. fs.mkdir(path, [options], callback)：创建文件夹，recursive属性为true，表示在创建/a/b/c文件夹是，如果/a/b不存在，就会先递归创建/a/b，再创建c；设置为false的话，不存在就会报错；
        
        4. fs.rmdir(path,callback)：只能删除空目录，不是空目录会报错；
           fs.rm(path,callback)：recursive 设置为 true 时可以递归地删除目录及里面的内容，当 recursive 设置为 false 时，可以单独删除文件，如果 recursive 设置为 false 而且指定删除目录，会报错；
           fs.rm 比 fs.rmdir 更加灵活，不仅可以删除文件，还可以删除目录，也可以递归地删除文件和目录
   
    5.监听文件变化
        6. fs.watch(filename[, options][, listener])
            options：  
                recursive：默认值 false，应该监视所有子目录，还是仅监视当前目录，仅在 macOS 和 Windows 上支持；
                persistent：默认值 true，指示如果文件已正被监视，进程是否应继续运行；
            listener(eventType, filename)：文件变化回调函数
        7. fs.watchFile(filename[, options], listener)
            options：
                biginit：默认值 false，指定回调 stat 中的数值是否为 biginit 类型；
                persistent：默认值 true，当文件正在被监视时，进程是否应该继续运行；
                interval：默认值 5007，用来指定轮询频率（ms）
            listener(currentStats, previousStats)：listener 有两个参数，当前的 stat 对象和之前的 stat 对象
        8. fs.unwatchFile(filename[, listener])：停止监视 filename 的变化，如果指定了 listener，则仅移除此特定监听器，否则将移除所有监听器，从而停止监视 filename
    
        fs.watch() 与 fs.watchFile()的区别：
            因为 fs.watchFile() 使用轮循方式检测文件变化，如果不设置 interval 或者设置较高的值会发现文件变化的监视有延迟，
            而 fs.watch() 监听操作系统提供的事件，而且可以监视目录变化，使用 fs.watch() 比 fs.watchFile() 更高效，平常应尽可能使用 fs.watch() 代替 fs.watchFile()     

但是这2个方法会有一些问题：       
> fs.watch:      
>● MacOS 有时候不提供 filename           
>● 在部分场景不触发修改事件（MacOS Sublime）        
>● 经常一次修改两次触发事件         
>● 大部分文件变化 eventType 都是 rename.      
>● 未提供简单的监视文件树方式       
> fs.watchFile:   
>● 事件处理问题和 fs.watch 一样烂      
>● 没有嵌套监听    
>● CPU 消耗大                

fs.watchFile() 性能问题，fs.watch() 平台不一致等两个方法都有不尽如人意的地方，所以可以选择一些比较优秀的库    
日常在监视文件变化可以选择社区的优秀方案       
[node-watch](https://www.npmjs.com/package/node-watch)  
[chokidar](https://www.npmjs.com/package/chokidar)  

    6. 其他常用API
        1. fs.existsSync(path)：判断路径是否存在，fs.exists(path, callback)已经废弃了
        2. fs.access(path[, mode], callback)：测试用户文件或者文件夹权限
            mode：设置值是文件可访问性常量，可以通过fs.constants获取
                F_OK：方法默认值，表明文件对调用进程可见，可用于判断文件是否存在；
                R_OK：表明调用进程可以读取文件；
                W_OK：表明调用进程可以写入文件；
                X_OK：表明调用进程可以执行文件，在 Windows 上无效（表现等同 fs.constants.F_OK）
        3. fs.rename(oldPath, newPath, callback)：将 oldPath 上的文件重命名为 newPath 提供的路径名，如果 newPath 已存在，则覆盖它；
        4. fs.chmod(path, mode, callback)：修改文件权限，mode 使用八进制表示 0o666

### 九. Buffer和Stream
#### **1.Buffer**
Buffer是一个用来存储和操作二进制数据的字节数组。  
数组每一个元素都是1个字节，超过1个字节的数据会通过 (value & 255) 截断，让它在0-255的范围之内。  
默认十六进制显示。  

实例化  
1.Buffer.from：  
Buffer.from(string [, encoding])：返回一个包含给定字符串的 Buffer  
Buffer.from(buffer)：返回给定 Buffer 的一个副本 Buffer  
Buffer.from(array)：返回一个内容包含所提供的字节副本的 Buffer，数组中每一项是一个表示八位字节的**数字**，所以值必须在 0 ~ 255 之间，否则会取模  
Buffer.from(arrayBuffer)：返回一个与给定的 ArrayBuffer 共享内存的新 Buffer  
Buffer.from(object[, offsetOrEncoding[, length]])：取 object 的 valueOf  或 Symbol.toPrimitive 初始化 Buffer  

2.Buffer.alloc：  
Buffer.alloc(size [, fill [, encoding]])：  
size：分配size个字节；  
fill：填充buffer的值，不传时默认为0；  
encoding：当fill为字符串时，encoding用来指定编码格式；默认UTF-8    

3.Buffer.allocUnsafe:  
和alloc相比不安全，因为这个方法分配内存时是没有初始化，而是可能直接拿已经存在旧数据的内存，所以这个方法的速度比alloc快，但是可能会拿到一些敏感的数据，就会不安全  
Buffer.allocUnsafe(size)  

编码  
Buffer 目前支持以下几种编码格式  
● ascii  
● utf8  
● utf16le  
● base64  
● binary  
● hex  

Buffer 和 String 之间的转换：  
String 转 buffer：  
const buf = Buffer.from('test')  
buffer 转 String：  
buf.toString()  

nodejs的自带string_decoder模块可以解决中文数据从buffer转回字符串时乱码问题  

Buffer 其它常用 API  
● Buffer.isBuffer：判断对象是否为 Buffer  
● Buffer.isEncoding：判断 Buffer 对象编码  
● Buffer.concat：将多个buffer拼接成一个buffer  
● buf.length：返回 分配内存的字节数，而不是数据实际占用的字节数  
● buf.indexOf：和数组的 indexOf 类似，返回某字符串、acsii 码或者 buf 在改 buf 中的位置  
● buf.copy(target)：将一个 buf 的（部分）内容复制到 target 中  

#### **2.Stream**
流是用于在 Node.js 中处理流数据的抽象接口。  

**使用流的好处：**  
流可以让数据一点一点地从数据源流到目的地，在这个流动的过程中，程序所占用的内存是很少的。  
如果直接把数据源的数据一次性地传递到目的地，在传递大文件时程序占用的内存会突然增大，如果大量的请求同时发起的话会使得程序崩溃，服务器爆炸。  

**流的分类：**  
1.可读流：对提供数据的源头(source)的抽象，是数据源用来供程序消费的流  
实例化可读流：  
const reader = fs.createReadStream(path,options)  
options可传参数:  
flags；  
encoding；  
fd： 传了 fd 的话，path 就会被忽略，也就不会触发 open 事件;  
mode；  
autoClose：默认为true，在 error 或者 finish 事件触发时会自动关闭文件描述符 fd;  
emitClose：默认为true，在流被销毁后触发 close 事件；  
start：从哪个位置开始读；  
end：读到哪个位置结束；  
highWaterMark：每次读取数据的最大字节数，默认 64 * 1024，即64kb；  
fs；  
signal；  

可读流的读取模式：  
**流动模式**：数据自动地从底层读取，形成流动现象，并通过事件提供给应用程序。  
以下情况能让可读流处于流动模式（暂停模式切换到流动模式）：  
监听 data 事件；  
使用 stream.pipe 方法；  
使用 stream.resume 方法；

**暂停模式**：数据会堆积在内部缓冲器中，必须显式调用 stream.read() 读取数据块。  
以下情况能让可读流处于暂停模式（流动模式切换到暂停模式）：  
移除 data 事件；    
调用 stream.pause 方法；  
调用 stream.unpipe 移除管道目标；  
在暂停模式下消费数据：  
监听 readable 事件 可读流在数据准备好后会触发该事件回调，此时需要在回调函数中使用 stream.read() 来主动消费数据。readable 事件表明流有新的动态：要么有新的数据，要么流已经读取所有数据  

自定义可读流：
2个要点：  
1.继承 **Readable** 类（require('stream').Readable）；  
2.重写 **_read** 方法，调用 this.push(String|Buffer [, encoding]) 方法把数据放到缓冲区（push只能传字符串或者buffer，第二参数是encoding），通过给 this.push 传递 null 停止生成数据，否则可读流会不断生产数据；

可读流事件：  
data 事件：产生可读数据时都会触发；  
readable 事件：可读流的数据准备好可以读的时候触发；  
pause 事件：在流动模式下调用 stream.pause 方法时触发；  
resume 事件：在暂停模式下调用 stram.resume 方法时触发；  
close 事件：内容读完后自动触发，整个读取过程最后触发的事件；  
end 事件：内容读完后自动触发，比 close 早触发；  

可读流常用方法：  
pause；  
resume；  
pipe；  
read；  

2.可写流：对数据写入目的地的一种抽象，是用来消费上游流过来的数据  
实例化可读流：  
const reader = fs.createReadStream(path,options)  
options可传参数:  
flags；  
encoding；  
fd：传了 fd 的话，path 就会被忽略，也就不会触发 open 事件;  
mode；  
autoClose：默认为true，在 error 或者 finish 事件触发时会自动关闭文件描述符 fd;  
emitClose：默认为true，在流被销毁后触发 close 事件；  
start：从哪个位置写入；  
fs；
signal；

自定义可写流：  
2个要点：  
1.继承 **Writable** 类（require('stream').Writable）；  
2.重写 **_write** 方法，参数是 chunk，encoding，callback；

可写流事件：  
pipe 事件：可读流调用 pipe 方法时触发；  
unpipe 事件：可读流调用 unpipe 方法时触发，在 finish 后面，在 close 前面；  
drain 事件：如果 stream.write 返回 false，这个事件会在数据能继续写入时触发；  
finish 事件：写入完成后触发，比 unpipe 触发早；  
close事件：最后触发；  

可写流常用方法：  
write：如果要写入的数据大于 highWaterMark，那么就先处理 highWaterMark 以内的数据，并且返回 false，等到缓冲区的数据写入完再去触发 drain 事件继续写入剩下的数据；  
end：如果用 write 方法写入数据，需要手动地调用 end 方法；  

#### **3.背压 back pressure**
什么是背压：  
它没有具体的定义，一般是指读取速度比写入速度快，即使流都有缓冲区，但当缓冲区也堆积满了数据之后，此时如果不做其他处理，读取出来的数据没有地方存放，会造成内存溢出，而垃圾回收机制又会把一些数据给清除掉，造成数据丢失。以上问题就是背压。

背压机制：  
针对上面问题，nodejs有一套背压机制来处理。  
这个机制的具体处理过程：  
根据 Writable 的 write 方法的返回值为 false（为 true 的话可以继续进行正常写入），表示 Writable 的缓冲区已经满了，同时让 Readable 暂停读取数据，等到 Writable 把剩下的数据处理完了，Writable 就触发 drain 事件，让 Readable 继续读取数据，然后继续整个过程直到结束。

通过 fs.createReadStream 和 fs.createWriteStream 生成的流，使用 pipe 方法时，底层已经使用了背压机制处理相关问题，所以不需要额外处理。  
但是在不使用 pipe 方法去处理流的时候，就要考虑这个问题了。例如直接用 write 方法写入，需要判断一下是否返回false，再进行相应的处理。

#### **4.双工流 Duplex 和转换流 transform**
双工流是同时实现 Writable 和 Readable 的流，转换流 transform 是实现 Transform 的流，而 Transform 是继承 Duplex 的，所以说转换流也是双工流，而且转换流实现了自己的 _write 和 _read 方法。

自定义双工流：  
1.继承 Duplex；  
2.实现 _write 方法；  
3.实现 _read 方法；

双工流的实例：  
TCP socket  
Zlib  
Crypto

自定义转换流：  
1.继承 Transform；  
2.实现 _transform 方法；  
3.实现 _flush 方法（可选）；

双工流的实例：  
Zlib  
Crypto

两者的区别：  
看起来 Transform 和 Duplex 重复了，但两者有一个重要的区别：Duplex 虽然同时具备可读流和可写流，但两者是相对独立的；Transform 的可读流的数据会经过一定的处理过程**自动**进入可写流

#### **5.pipeline**
pipeline 方法的作用类似于链式调用 pipe() 方法 ，在管道内传输多个流，在管道任务结束后提供回调  
stream.pipeline(source[, ...transforms], destination, callback)  
source：可读流  
...tranforms：双工流  
destination：可写流  
callback：当管道完全地完成时调用

    const { pipeline } = require('stream');
    const fs = require('fs');
    const zlib = require('zlib');

    pipeline(
    fs.createReadStream('archive.tar'),
    zlib.createGzip(),
    fs.createWriteStream('archive.tar.gz'),
    (err) => {
        if (err) {
            console.error('管道传送失败', err);
        } else {
            console.log('管道传送成功');
        }
    });

### 十. http模块
http.createServer：创建服务器  
const server = http.createServer(callback)：创建一个服务器。
callback的2个参数：  
req：一个可读流，常用的属性 url、method、headers；  
res：一个可写流，常用的属性和方法：  
writeHead(statusCode,[, StatusMessage[, headers]])：发送响应首部，包含状态码、状态信息、响应头  
write(chunk)：向响应主体中写入字符串或者 buffer  
end(chunk)：向服务器发出信号，可以携带最后发送的数据，表明已发送所有响应头和主体，每个响应都需要调用一次  
getHeader(name)：返回指定 name 的 header  
getHeaders()：返回包含了所有 header 信息的对象  
setHeader(name, value)：设置响应头，和 writeHead() 合并，有冲突时优先使用 writeHead()  
statusCode：设置响应 HTTP status  

服务器实例 server 的属性和方法：  
listen(callback)：启动 http 服务器的监听连接；  
listening：判断服务器是否正在监听连接；  
close(callback)：停止服务器接受新连接并关闭连接到该服务器的所有未发送请求或等待响应的连接

http.request：发送http请求
