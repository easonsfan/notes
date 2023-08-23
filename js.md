JavaScript高级程序设计：18章没看，20章有部分没看 20.1、20.3、20.5、20.9、

### axios：
    使用post传递参数：会自动根据传的参数类型设置content-type
    data属性传对象时，content-type自动设置为application/json；
    data属性传序列化key1=value1&key2=value2的格式时，content-type自动设置为application/x-www-form-urlencoded；
    data属性传formdata格式时，content-type自动设置为multipart/form-data
    相反，如果设置了某个content-type，就要传对应格式的参数数据

    使用get传递参数：
    get请求只能用？key=value方式传参，请求体为空，设置content-type没有意义

### ==隐式转换
    x==y比较时类型转换：最终都会被转换为字符串和字符串或者数字和数字比较（对象和对象比较时,即x、y都是对象，只会比较地址是否一致）
    1. 当x、y其中一个为字符串，另一个为数字时，字符串转换为数字比较；
    2. x或者y为Boolean时，会被转换为数字比较；
    3. 用对象比较时，对象会调用valueOf方法，如果返回值是基本数据类型，就用这个值比较；如果不是就调用toString方法，如果返回值是基本数据类型，就用这个值比较；
       如果返回值都不是基本数据类型，就报错

### margin负值：
    对于没有设置宽度的元素，设置margin-left/right负值会增加元素宽度
    在普通文档流，margin-left/top为负值，元素向left或者top移动；margin-bottom/right为负值，元素不会移动，但是对应方向上相邻的元素会靠近
    在浮动的元素中，因为父元素宽度不够导致换行，可以用margin负值向上移动，但是会覆盖上面已有的元素

***********************************************************************************

    ArrayBuffer是什么？
    存储二进制（以字节来表示一个长度，跟node的buffer一样）的缓冲区域，只读不写，和node的buffer不同，buffer可以读写，
    要想操作，需要用TypeArray、DataArray，所以ArrayBuffer可以说是前端用来操作二进制数据的一个类，而node后端则用buffer

    Blob是什么？
    用来存放二进制数据的类文件对象，创建出来的对象可以当作文件读取。
    同时可以用URL.createObjectURL(blob)创建一个url字符串给web worker使用

    URL是什么？
    用来创建一个url对象，对象的静态方法createObjectURL可以为blob或者file对象文件创建一个指向内存地址的字符串，这样浏览器就可以识别到它。
    例如，上传图片时，需要用一个img标签做预览，用这个URL字符串给img标签的src属性赋值就可以显示图片

    URLSearchParams是什么？
    参数可以是一个key=value字符串，也可以是一个对象
    就是URL问号 '?' 后面的查询参数 key1=value1&key2=value2,但是这个会返回一个对象

***********************************************************************************

    Array构造函数是Function的实例对象，所以Array.__proto__ === Function.prototype;
    Function也是Function的实例对象，所以Function.__proto__ === Function.prototype;
    Object构造函数也是Function的实例对象，所以Object.__proto__ ==== Function.prototype;

****************************************************************************************

    可迭代需要符合2个协议：
    1. 可迭代协议：
      对象要有Symbol.iterator接口，实际上是一个函数，返回一个迭代器
    2. 迭代器协议
      对象里要有一个next方法，而这个方法返回一个有value和done属性的对象

    生成器对象可迭代，是因为有可迭代接口Symbol.iterator，但是调用可迭代接口返回的是生成器对象本身，而它本身就有next方法，符合2个协议，所以可迭代

*******************************************************************************************

### flex布局的伸缩规则：
    有剩余的空间，元素就会根据flex-grow的份数增加自己的宽度或高度；
    有多余的空间，元素就会根据flex-shrink的份数减少自己的宽度或高度；
    flex-basis会改变元素的宽度或高度，从而影响分配的结果
    注意：如果文本的内容超出了容器的空间，flex布局没有效果

    width和height的2个值：
    max-content：在宽度或高度上尽可能大；
    min-content：在宽度或高度上尽可能小；

    flex-basis：
    设置为auto时：如果元素有width或者height，flex-basis的值就等于他们的值；如果没有设置width或者height，就取有文本时的max-content的值
    设置为0时：把元素width或者height的值当作0(有内容时会就取max-content的值)

    flex行数问题：
    单行内容时，不管子元素是否填充满交叉轴，一行的高度都是按父元素的高度算
    多行内容时，按照内容来分行，每行占的高度等于父元素高度/行数

    align-items是在当前行内，往cross axis的方向对齐
    align-content是直接把每一行，往cross axis的方向对齐，同时会把每一行原来的高度挤压到最小高度

***********************************************************************************************

### function参数和arguments：
    function foo(a,b){
        arguments[0] = 1
    }
    foo('123');
    1. 传入的实际参数有多少，arguments里就有多少个元素，与形式参数无关；
    2. 形式参数和arguments保持同步，但并不是访问同一个内存地址,同步的意思是修改形式参数，arguments对应的跟着改变；
    3. 形式参数是否跟arguments保持同步，取决于传入了多少实际参数：
      1. 如果实际参数大于等于形式参数个数，那么所有形式参数都会和arguments保持同步；
          function foo(a,b){
              a = 10
              console.log(arguments[0]) // 10
              console.log(a) // 10
          }
          foo('1','2');
      2. 如果实际参数少于形式参数，那么只有与实际参数对应的形式参数和arguments会保持同步；
          function foo(a,b){
              b = 10
              console.log(arguments[1]) // undefined
              console.log(b) // 10
          }
          foo('1');
      3. 使用了默认参数的形参，不会与arguments保持同步；

    js的函数传参只有值传递，没有引用传递：
      let a = 1
      function change(b){
          b = 2
      }
      change(a)
      console.log(a) // 1
    引用传递是指，把基本数据类型1传入函数，修改为2时，a也被修改为2；
    js中传递对象，只是把对象的内存地址复制一份给函数的形参
***********************************************************************************************

    Object.keys:  自身可枚举的属性
    Object.getOwnPropertyNames: 自身所有属性
    Object.prototype.hasOwnProperty: 对象某个属性是不是自身属性(不是继承来的)

***********************************************************************************************
### window.location 和 window.history：

    1.location：可以修改URL内容
      改变URL的hash值不会重新加载页面，改变其他会重新加载页面；
      每改变一次URL，就会在history中增加一条历史记录。

    2.history.pushState：添加历史记录，不会重新加载页面。 
      第一个参数传一个对象，可以存储一些需要的属性数据；
      第二个参数无所谓，一般传空字符串；
      第三个参数可以修改URL；

    vue使用history模式时会报404，因为URL的路径和参数等会在发送请求时携带到后端，后端没有对应的接口，就会报404；
    而hash模式不会报错，是因为hash值不会被请求携带到后端，而且修改hash也不会导致发送请求

***********************************************************************************************

    scrollTo、scrollTop和scrollLeft等方法和属性是针对有滚动条的时候，元素内部内容的移动，具体的数值是移动距离；
    而srollIntoView是对一个元素的移动，如果元素已经在视口了，就不会移动，不能设置具体的数值来移动距离;


***********************************************************************************************
### 偏移尺寸：
    1. element.offsetLeft、element.offsetTop：
      是相对于element.offsetParent的偏移量，距离是从element的边框外侧到offsetParent的边框内侧
    2. element的offsetParent：
      包含element的最近的设置定位的元素，如果没有定位就是最近的body、table、td、th标签
    3. offsetWidth offsetHeight：
      元素的宽度和高度，从边框外侧向里面开始计算

### 客户端尺寸：
    1. clientWidth、clientHeight：
      元素的padding+content部分，也就是元素的内部空间，从边框内侧向里面计算
    2. clientTop、clientLeft：
      元素顶部和左边border的宽度

### 滚动尺寸：
    1. scrollLeft、scrollTop：
      元素滚动条移动的距离，或者说元素内容向左或向上移动了多少距离
    2. scrollWidth、scrollHeight：
      没有滚动条时等于clientWidth和clientHeight，有滚动条时等于元素内容的全部宽度或全部高度（包括外边距、边框、内边距、content）

### window的大小和位置属性：
    浏览器大小：
      1. innerWidth：浏览器视口宽度；
      2. innerHeight：浏览器视口高度；
      3. outerWidth：整个浏览器的宽度；
      4. outerWidth：整个浏览器的高度；
    整个文档页面滚动距离：
      1. pageXoffset/scrollX：向左滚动的距离
      2. pageYoffset/scrollY：向上滚动的距离
      **html或者body的scrollTop和srollLeft属性与这2个属性一样**
    浏览器边框到屏幕边缘的距离：
      screenX/screenLeft、screenY/screenTop

### 鼠标事件的event对象的相关属性
    1. 客户端坐标：
      clientX、clientY：鼠标到浏览器视口的位置
    2. 页面坐标：
      pageX、pageY：鼠标在页面的位置
    3. 屏幕坐标：
      screenX、screenY：在整个屏幕的位置

***********************************************************************************************

### defer、async：
    1. 在没有defer或者async属性时，script标签引入外部js会阻塞dom解析；
    2. 使用defer属性，遇到script标签会立即加载，但是会等到dom解析完才会执行js代码，而且js代码会在DOMContentLoaded事件之前执行
    3. 使用async属性，遇到script标签会立即加载并且立即执行，而且不确定js的执行顺序，可能在DOMContentLoaded事件之前或者之后执行
    ** js代码的异步任务，不会影响load或者DOMContentLoaded事件的触发**
    不过最佳实践还是把script标签放到body标签里面的最后，这样不会影响页面dom结构解析渲染，防止造成首页白屏

***********************************************************************************************
### 跨域通信、跨窗口通信：postMessage
    1.postMessage的实质是自己给自己发消息，只要在一个窗口里有另外一个窗口的引用opener，就可以用这个opener给它自己发送消息

      let open = window.open('xxxx')
      这样就可以引用被打开窗口对象了
      let opener = window.opener
      这样就有了打开此窗口的原窗口对象了

    web worker使用postMessage好像可以不用加targetOrigin(用字符串形式时)

    worker和open方式打开窗口的不同：
    1. Worker生成一个实例后，实例worker和线程里的self，既能监听message事件又能使用postMessage，所以只用worker和self就能通信。
    2. 使用open打开窗口，打开窗口后的open实例不能监听message，只能postMessage，所以需要通过当前的window来监听message来接收新
       打开的窗口发来的信息；新打开的窗口要发送消息，只能通过open.opener(即原来的窗口)来发送，因为open实例没办法监听
***********************************************************************************************
    FileReader：读取文件的
      1. 通过几个方法，将读取到的内容用这些格式来表示：
          readAsDataURL：用data：URL格式的字符串（base64编码）
          readAsBinaryString：用原始二进制字符串的格式
          readAsText：根据编码格式（第二个参数）转化为对应的字符串
          readAsArrayBuffer：用ArrayBuffer存放读取到的内容
      2. 通过上面的方法读取之后，以下事件才会触发：
          load、progress、error、loadstart、loadend等。

    FileReaderSync：同样是读取文件的
      1. 只能在web worker里使用
      2. 调用方法读取文件后返回数据

***********************************************************************************************
    commonjs：
      1. module.exports = exports = { }，在文件顶部就赋值，所以给exports重新赋值不会影响module.exports
      2. 最后导出的是module.exports而不是exports

    es6 module：
      1. export { name } 不是导出一个对象，而是要导出变量的列表，js引擎到时会在内存中重新定义变量const name = name，
          而import导入的就是新定义的name；
      2. 一旦name在被导入的模块中改变，内存会更新name，import导入的name也会更新
***********************************************************************************************
### 移动端插件：
    lib-flexible 自动根据屏幕设置根元素的字体大小
    postcss-plugin-px2rem     px自动转rem
***********************************************************************************************
### 检查数据类型：
    1. typeof直接在计算机底层基于数据类型的值（二进制）进行检测，对象存储在计算机中，都是以000开始的二进制存储
        typeof null  == 'object'     //  null也是开头为000的二进制存储，所以null检测为对象
        typeof 普通对象/数组对象/日期对象/正则对象/  == 'object'

    2. instanceof 检测当前实例是否属于某个类，底层机制：只要当前类出现在实例的原型链上，结果都是true
        1. 由于原型是可以被修改的，所以检测结果可能不准确；
        2. 不能检测基本数据类型：1 instanceof Number， 结果为false 

    3. constructor 跟instanceof差不多，但是constructor可以检测基本数据类型：1.constructor === Number  // true
        同样由于constructor是可以被修改的，所以检测结果可能不准确

    4. Object.prototype.toString.call(value)
        标准检测数据类型的方法：它并不是转换为字符串，而是返回当前实例所属类的信息
        注意：是要这样子调用Object.prototype.toString.call(value)，而不是这样调用 [1,2].toString()，直接调用的toString方法是底层重写过的，
            原始的toString方法就是返回当前实例所属类的信息。
            针对数字，重写过的toString可以传入一个参数，表示用多少进制来显示字符串

    总结： 并非所有情况都用第4种方式，在实际检测类型或者深拷贝时应该先用typeof判断，如果是对象再用Object.prototype.toString.call(value)判断

### 三类循环对比及性能分析：forEach、for in、for of
    for和while：第一、第二
        1. 使用var 声明i时，2个性能差不多，使用let声明i时，for循环性能更好

    forEach循环：第三
        1. forEach比for性能慢很多

    for in循环：第五
        1. 迭代当前对象中所有的可枚举属性，查找机制上一定会查到原型链上
        2. for in是最慢的；
        3. 遍历以数字优先
        4. 无法遍历symbol属性
        5. 可以遍历到公有属性中的可枚举属性

    for of循环：第四
        1. 迭代有实现iterator规范的对象


### 从输入url地址到看见页面，中间都经历了什么
    第一步：url解析：
        1.url地址解析：http://www.baidu.com?name=zs&age=18#id
        2.url编码：encodeURI/decodeURI、encodeURIComponent/decodeURIComponent，把url中的特殊字符进行编码
    第二步：缓存检查：
        缓存位置：memory cache 内存缓存    disk cache 硬盘缓存 
        1.新打开一个网页：找的是disk cache中的缓存，因为memory cache已经被清除了
        2.普通刷新：先找memory cache，然后才是disk cache
        3.强制刷新：不使用缓存，直接发送请求获取最新的内容
    第三步：DNS解析：
        1.递归查询：
            浏览器缓存-->本地hosts文件-->本地DNS解析器缓存-->本地DNS服务器
        2.迭代查询：
            如果本地DNS服务器也没有，本地DNS服务器依次向 根域名服务器、顶级域名服务器、权威域名服务器获取相应信息，然后再去访问对应ip
        3.DNS解析性能优化：DNS预解析
            如果没有DNS缓存，DNS解析需要耗费一些时间，这样会阻塞网页加载，DNS预解析可以把要访问的域名预先解析了  
            <link rel="dns-prefetch" href="//static.360buyimg.com">  // 京东网站DNS预解析
    第四步：TCP三次握手：
    第五步：传输数据：
    第六步：TCP四次挥手：
    第七步：页面渲染
***********************************************************************************************
### 谷歌的RAIL性能模型：
    R（Response）响应：尽可能快的响应用户，应该在100ms以内响应用户操作，
    A（Animation）动画：展示动画，应该每一帧以10ms进行渲染。因为屏幕的刷新率时60帧/s，所以1帧就是1000ms/60约等于16.6ms，
                       除去浏览器绘制新帧的时间，留给执行代码的时间仅10ms左右。
    I（Idle）空闲：当使用Javascript主线程的时候，应该把任务划分到执行时间小于50ms的片段中去，这样可以释放线程以进行用户交互。
    L（loading）加载：首次加载应该在小于5s内加载完成，并可以进行用户交互。对于后续加载，建议在2s内完成
***********************************************************************************************
