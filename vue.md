## vue2：
### 组件渲染流程：
      $mount('#app') -> mountComponent -> vm._update(vm._render()) -> patch -> createElm
         1. 父组件先挂载，就会调用render方法生成vnode，但是会先生成子组件的vnode，因为render方法 _c('div',data,[_c('my-cpn',data,xxx)]) 会先执行里面的_c，
         2. 生成组件vnode，tag为'vue-component'开头，data里写init方法（用来生成VueComponent实例，同时给实例赋值componentInstance，然后直接调用$mount方法）
         3. update方法生成真实节点（先递归生成完子组件的真实节点，最后再生成父组件的），生成组件时是在createElm调用init方法来生成一个真实节点

### 组件vnode和组件里的标签vnode的关系：
      组件vnode的componentInstance的render方法返回的就是真实标签的vnode，组件vnode又在父组件vnode的children里
      组件vnode如何生成真实节点？
      在createElm方法里，遇到组件vnode时，会调用组件实例的$mount，给实例的$el赋上真实标签的节点，然后返回这个$el，这样真实节点就会添加到父组件上

### $vnode和_vnode的区别？
      $vnode是组件占位符的vnode，_vnode是真实标签的vnode，而_vnode.parent = $vnode

### keep-alive组件：
      在创建keep-alive组件实例时，实例调用init方法时，判断是否有keepAlive属性表示缓存过，
      有就调用prepatch方法更新，没有才创建子组件实例；
      在挂载时，调用组件的render方法，这个方法缓存当前要显示的子组件实例并且返回子组件vnode，

      

## vue3：
### reactive函数：
      1. 使用weakmap是防止一个原始对象重复地做响应式，所以把原始对象缓存起来，
         这样一个原始对象就不会重复生成多个响应式对象了，而且实际也没有这个必要；
      2. 使用枚举类ReactiveFlags是防止已经做过响应式的对象再放进来继续做响应式，这个也没必要，虽然继续代理不会报错。

### WeakMap和Map：
      WeakMap能防止内存泄漏，是因为WeakMap不会对key的对象进行引用，在对象没有引用时，垃圾回收机制会清除掉这个对象；
      而Map是会引用key的对象的，对象没有外部引用，但是Map还是会引用这个对象，自然不能防止内存泄漏

### 全局变量：
      定义在 app.config.globalProperties 的变量，可以在 getCurrentInstance() 的 appContext 里获取