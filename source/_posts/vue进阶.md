---
title: vue进阶
date: 2020-09-15 23:34:34
categories: front #分类
tags: vue
---
vue组件三要素：props参数、slot定制插槽、event自定义事件。
vue组件划分的思想
对vue项目来说，组件是构成项目的基本单元，为了方便理解，这里定义两类组件：页面组件，功能组件。
页面组件是无法复用的组件，功能组件是可以复用的组件。
页面组件的定义仅仅是整张页面的统筹，页面组件也是由一个个功能组件堆叠而成的，对功能组件的定义为，
他的展示只受父组件通过props组件传值过来的数据影响，他的所有状态均为内部私有，与外部组件的交互由vue提供的props以及事件分发vm.$emit()。
因为只有这也组件才能保证其完整的内部状态，在复用时不受其他因素的影响。
如何划分他们
划分页面组件最简单的方式是由路由划分，每一个路由对应一个页面组件。
功能组件的划分可能根据不同人的理解各不相同，但是原则只有一个：
功能组件应该类似面向对象编程一样，一个功能组件应该只是完成一个功能。
<!--more-->

v-for为什么要加key，能用index作为key么
key的作用就是更新组件时判断两个节点是否相同。相同就复用，不相同就删除旧的创建新的。在渲染简单的无状态组件时，如果不添加key组件默认都是就地复用，不会删除添加节点，只是改变列表项中的文本值，要知道节点操作是十分耗费性能的。而添加了key之后，当对比内容不一致时，就会认为是两个节点，会先删除掉旧节点，然后添加新节点。
index作为key，在表现上确实几乎没有问题，但它主要有两点不足：
1）index作为key，其实就等于不加key
2）index作为key，只适用于不依赖子组件状态或临时 DOM 状态 (例如：表单输入值) 的列表渲染输出（这是vue官网的说明）
diff算法默认使用“就地复用”的策略，是一个首尾交叉对比的过程。
用index作为key和不加key是一样的，都采用“就地复用”的策略
“就地复用”的策略，只适用于不依赖子组件状态或临时 DOM 状态 (例如：表单输入值) 的列表渲染输出。
将与元素唯一对应的值作为key，可以最大化利用dom节点，提升性能
就地复用的副作用主要产生在发生了重排序以及组件内部状态发生变化。

vue 首页白屏问题
VUE首页加载过慢，其原因是因为它是一个单页应用，需要将所有需要的资源都下载到浏览器端并解析。
因为是spa，而且所有的渲染都在脚本上，js执行需要时间。另外加载js也要时间，所以页面越大，加载时间越长，
而且js执行的时间也长，dcl发生的时间点就更晚，所以会白屏
解决办法：
优化 webpack 减少模块打包体积，code-split 按需加载
服务端渲染，在服务端事先拼装好首页所需的 html
首页加 loading 或 骨架屏 （仅仅是优化体验）
服务端开启gzip压缩
打包文件分包，提取公共文件包

SSR
在客户端请求服务器的时候，服务器到数据库中获取到相关的数据，并且在服务器内部将Vue组件渲染成HTML，并且将数据、HTML一并返回给客户端，这个在服务器将数据和组件转化为HTML的过程，叫做服务端渲染SSR。
而当客户端拿到服务器渲染的HTML和数据之后，由于数据已经有了，客户端不需要再一次请求数据，而只需要将数据同步到组件或者Vuex内部即可。除了数据以外，HTML也结构已经有了，客户端在渲染组件的时候，也只需要将HTML的DOM节点映射到Virtual DOM即可，不需要重新创建DOM节点，这个将数据和HTML同步的过程，又叫做客户端激活。
使用SSR的好处：
有利于SEO：其实就是有利于爬虫来爬你的页面，因为部分页面爬虫是不支持执行JavaScript的，这种不支持执行JavaScript的爬虫抓取到的非SSR的页面会是一个空的HTML页面，而有了SSR以后，这些爬虫就可以获取到完整的HTML结构的数据，进而收录到搜索引擎中。
白屏时间更短：相对于客户端渲染，服务端渲染在浏览器请求URL之后已经得到了一个带有数据的HTML文本，浏览器只需要解析HTML，直接构建DOM树就可以。而客户端渲染，需要先得到一个空的HTML页面，这个时候页面已经进入白屏，之后还需要经过加载并执行 JavaScript、请求后端服务器获取数据、JavaScript 渲染页面几个过程才可以看到最后的页面。特别是在复杂应用中，由于需要加载 JavaScript 脚本，越是复杂的应用，需要加载的 JavaScript 脚本就越多、越大，这会导致应用的首屏加载时间非常长，进而降低了体验感。

Vue源码之 props data computed 顺序
function initState (vm) {
        vm._watchers = [];
        var opts = vm.$options;
        if (opts.props) { initProps(vm, opts.props); }
        if (opts.methods) { initMethods(vm, opts.methods); }
        if (opts.data) {
            initData(vm);
        } else {
            observe(vm._data = {}, true /* asRootData */);
        }
        if (opts.computed) { initComputed(vm, opts.computed); }
        if (opts.watch && opts.watch !== nativeWatch) {
            initWatch(vm, opts.watch);
        }
    }
可以看出来computed在data之后，所以不要在data中引用computed中的属性，只能得到undefined。
data可以调用前面的props，methods的属性。
computed中可以调用props，methods，data中的属性。

Vue源码解析：this.$data、this._data、this.xxx 为什么都能获取数据？
new Vue(options) 时会调用 Vue 类中的 this._init() 原型方法，该原型方法中又会调用 initState(vm) 进行 props、methods、data 等初始化
export function initState (vm: Component) {
  vm._watchers = []
  const opts = vm.$options
  if (opts.props) initProps(vm, opts.props)
  if (opts.methods) initMethods(vm, opts.methods)
  if (opts.data) {
    initData(vm)
  } else {
    observe(vm._data = {}, true /* asRootData */)
  }
  if (opts.computed) initComputed(vm, opts.computed)
  if (opts.watch && opts.watch !== nativeWatch) {
    initWatch(vm, opts.watch)
  }
}
function initData (vm: Component) {
  let data = vm.$options.data // vm.$options 是访问自定属性，此处就是vue实例中的 this.$data
  data = vm._data = typeof data === 'function' ? getData(data, vm)   : data || {}  // 先执行三元表达式，然后赋值给 vm._data 和 data，这样子这两个值都是同时变化的，就是 this.$data.xxx 和 this._data 同时变化
      ………
}
export function getData (data: Function, vm: Component): any {
  pushTarget()
  try {
    return data.call(vm, vm)
  } catch (e) {
    handleError(e, vm, `data()`)
    return {}
  } finally {
    popTarget()
  }
}
this.$data.xxx、this._data.xxx、this.xxx 都能获取数据，先让 this.$data.xxx = this._data.xxx，然后用 call 让当前组件实例 this 继承了 this.$data 的值，也就是 getData 方法 return 出了一份用 call 改变指向将 $data 指向 this 的数据。
vue组件中 data 为什么是函数?
因为组件是用来复用的，JS 里对象是引用关系，这样作用域没有隔离，但是 data 定义成一个函数，将会 return 出一个唯一的对象，是不会被复用的，因此不存在引用对象的问题。

vue 父子组件的生命周期顺序
一、加载渲染过程
父beforeCreate->父created->父beforeMount->子beforeCreate->子created->子beforeMount->子mounted->父mounted
二、子组件更新过程
父beforeUpdate->子beforeUpdate->子updated->父updated
三、父组件更新过程
父beforeUpdate->父updated
四、销毁过程
父beforeDestroy->子beforeDestroy->子destroyed->父destroyed

vue和react的区别
react整体的思路就是函数式，所以推崇纯组件，数据不可变，单向数据流，当然需要双向的地方也可以做到，比如结合redux-form，而vue是基于可变数据的，支持双向绑定。react组件的扩展一般是通过高阶组件，而vue组件会使用mixin。vue内置了很多功能，而react做的很少，很多都是由社区来完成的，vue追求的是开发的简单，而react更在乎方式是否正确。

Vue3.0使用Proxy代替Object.defineProperty
为什么要取代Object.defineProperty
总结起来大概是下面两个：
1.在Vue中，Object.defineProperty无法监控到数组下标的变化，导致直接通过数组的下标给数组设置值，不能实时响应。 
为了解决这个问题，经过vue内部处理后可以使用以下几种方法来监听数组：
push() pop() shift() unshift() splice() sort(）reverse()
2.Object.defineProperty只能劫持对象的属性,因此我们需要对每个对象的每个属性进行遍历。
Vue里，是通过递归以及遍历data 对象来实现对数据的监控的，如果属性值也是对象那么需要深度遍历,显然如果能劫持一个完整的对象，不管是对操作性还是性能都会有一个很大的提升。
而要取代它的Proxy有以下两个优点;
可以劫持整个对象，并返回一个新对象
有13种劫持操作
基本用法：let p = new Proxy(target, handler);
var text = {
  data: 2,
  data1: {
   a:1,
   b: 3
  }
}
var oo = new Proxy(text, {
  get: function (target, key, receiver) {
    console.log(`proxy get ${key}`)
    return Reflect.get(target, key, receiver)
  },
  set: function (target, key, value, receiver) {
    console.log(`proxy set ${key}`)
    return Reflect.set(target, key, value, receiver)
  }
})
oo.data // proxy get data 2

var target = {
   name: 'poetries'
 };
 var logHandler = {
   get: function(target, key) {
     console.log(`${key} 被读取`);
     return target[key];
   },
   set: function(target, key, value) {
     console.log(`${key} 被设置为 ${value}`);
     target[key] = value;
   }
 }
 var targetWithLog = new Proxy(target, logHandler);
 
 targetWithLog.name; // 控制台输出：name 被读取
 targetWithLog.name = 'others'; // 控制台输出：name 被设置为 others

 console.log(target.name); // 控制台输出: others

Vue.use可以实现插件的注册
如果插件是一个对象，必须提供 install 方法。
如果插件是一个函数，它会被作为 install 方法。install 方法调用时，会将 Vue 作为参数传入。
该方法需要在调用 new Vue() 之前被调用。
当 install 方法被同一个插件多次调用，插件将只会被安装一次。

vue命令式组件和插件编写
vue各种UI库里的命令式组件比如element-ui里Notification组件，可以这样调用
        this.$notify({
          title: '偏移',
          message: '这是一条带有偏移的提示消息',
          offset: 100
        });
命令式调用的组件写法分三步：
1、编写组件代码，也就是.vue文件
2、Vue.extend()继承扩展组件，编写组件调用逻辑(使用这个extend，可以将引入的vue组件变成vue构造函数，实例化后方便进行扩展)
我们希望组件是在使用时才显示出来，那么就需要动态的向body中添加元素。使用$mount方法可以手动挂载一个vue实例
3、挂载到Vue原型上，使得每个组件实例可以调用
document.body.appendChild(newInstance.$mount().$el)  //使用$mount()后  可以理解为创建虚拟的dom
export default {
    install(vue){
        vue.prototype.$Confirm = caller
    }
}

vue render函数
export default {
  name: 'mars-btn',
  props: {
    tag: {
      type: String,
      default: 'a'
    },
    type: {
      type: String,
      default: 'click'
    },
    title: {
      type: String,
      default: ''
    },
    event: {
      type: String,
      default: ''
    }
  },
  render (createElement) {
    const _self = this
    const on = {
      click: (e) => {
        stat({
          type: 'click',
          event: _self.event,
          title: _self.title
        })
        this.$emit('click', e)
      }
    }
    return createElement(
     _self.tag, // 标签
     { on }, // 事件、样式
     _self.$slots.default) // 内容
  }
}

自定义指令
钩子函数
一个指令定义对象可以提供如下几个钩子函数 (均为可选)：
bind：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。
inserted：被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)。
update：所在组件的 VNode 更新时调用，但是可能发生在其子 VNode 更新之前。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新 (详细的钩子函数参数见下)。
componentUpdated：指令所在组件的 VNode 及其子 VNode 全部更新后调用。
unbind：只调用一次，指令与元素解绑时调用。
接下来我们来看一下钩子函数的参数 (即 el、binding、vnode 和 oldVnode)。
Vue.directive('demo', {
  bind: function (el, binding, vnode) {
    var s = JSON.stringify
    el.innerHTML =
      'name: '       + s(binding.name) + '<br>' +  // 指令名
      'value: '      + s(binding.value) + '<br>' + // 指令的绑定值
      'expression: ' + s(binding.expression) + '<br>' +
      'argument: '   + s(binding.arg) + '<br>' +
      'modifiers: '  + s(binding.modifiers) + '<br>' +
      'vnode keys: ' + Object.keys(vnode).join(', ') 
  }
})

new Vue({
  el: '#hook-arguments-example',
  data: {
    message: 'hello!'
  }
})

为什么Vue采用异步渲染
Vue是组件级更新的。
因为如果不采用异步更新，那么每次更新数据都会对当前组件进行重新渲染。
所以为了性能考虑，Vue会在本轮数据更新后，再去异步更新视图。

vue-router的两种模式的区别
hash —— 即地址栏 URL 中的 # 符号。
比如这个URL：http://www.abc.com/#/hello，hash 的值为 #/hello。它的特点在于：hash 虽然出现在 URL 中，
但不会被包括在 HTTP 请求中，对后端完全没有影响，因此改变 hash 不会重新加载页面。
history —— 利用了 HTML5 History Interface 中新增的 pushState() 和 replaceState() 方法。（需要特定浏览器支持）
这两个方法应用于浏览器的历史记录栈，在当前已有的 back、forward、go 的基础之上，它们提供了对历史记录进行修改的功能。
只是当它们执行修改时，虽然改变了当前的 URL，但浏览器不会立即向后端发送请求。
在hash模式下，前端路由修改的是#中的信息，而浏览器请求时是不带它玩的，所以没有问题。
但是在history下，你可以自由的修改path，当刷新时，如果服务器中没有相应的响应或者资源，会分分钟刷出一个404来。
hash原理：路由的哈希模式其实是利用了window可以监听onhashchange事件，也就是说你的url中的哈希值（#后面的值）如果有变化，前端是可以做到监听并做一些响应。
因为hash发生变化的url都会被浏览器记录下来，从而浏览器的前进后退都可以用了，这样一来，尽管浏览器没有请求服务器，但是页面状态和url一一关联起来。
HTTP请求中不包括#，也就是说改变#后的内容不会向服务器发起请求，因此也就不会引起页面重载。
history原理：HTML5规范为 window.history引入了两个新api，pushState(state,title,url)和 replaceState(state,title,url)，我们可以使用它很方便的达到改变url不重载页面的目的。
pushState与replaceState方法类似，都有改变当前地址栏URL的作用。主要区别在于pushState会在浏览器中创建一条新的历史纪录，而replaceState仅仅替换将当前地址为指定URL。
当我们使用pushState()和replaceState()进行处理时或者点击浏览器的前进后退的时候，popstate事件会被触发，通过监听popstate事件可以达到一系列功能

vue路由钩子
1.全局钩子
2.某个路由独享钩子
3.组件内钩子
全局钩子
beforeEach(全局前置守卫)、afterEach（全局后置钩子）
Router.beforeEach((to,from,next)=>{
}) // 在跳转之前执行，可以用来进行登录验证
Router.afterEach((to,from,next)=>{
}) // 在跳转之后执行
某个路由独享钩子
beforeEnter
beforeLeave
{
  path:"/home",
  component:resolve=>require(["../components/home.vue"],resolve),
  beforeEnter:(to,from,next)=>{
      ......
  },
  beforeLeave:(to,from,next)=>{
      ......
  }
}
组件内钩子
 methods:{
      beforeRouteLeave(to,from,next){
            .......
      }
  }
1.beforeRouteLeave
导航离开该组件的对应路由时调用
可以访问组件实例 `this`
2.beforeRouteEnter
在渲染该组件的对应路由被 confirm 前调用
不能获取组件实例 `this`，因为当钩子执行前，组件实例还没被创建
3.beforeRouteUpdate
在当前路由改变，但是该组件被复用时调用
举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
可以访问组件实例 `this`

nextTick源码浅析
this.$nextTick(() => {
        this.msg2 = this.$refs.msgDiv.innerHTML
      })
Vue.nextTick用于延迟执行一段代码，它接受2个参数（回调函数和执行回调函数的上下文环境），
如果没有提供回调函数，那么将返回promise对象。
首先，先了解nextTick中定义的三个重要变量。
callbacks：用来存储所有需要执行的回调函数
pending：用来标志是否正在执行回调函数
timerFunc：用来触发执行回调函数
了解nextTickHandler()函数。
function nextTickHandler () {
    pending = false
    const copies = callbacks.slice(0)
    callbacks.length = 0
    for (let i = 0; i < copies.length; i++) {
      copies[i]()
    }
  }
这个函数用来执行callbacks里存储的所有回调函数。
接下来是将触发方式赋值给timerFunc。
先判断是否原生支持promise，如果支持，则利用promise来触发执行回调函数；
否则，如果支持MutationObserver，则实例化一个观察者对象，观察文本节点发生变化时，触发执行所有回调函数。
如果都不支持，则利用setTimeout设置延时为0。
最后是queueNextTick函数。因为nextTick是一个即时函数，所以queueNextTick函数是返回的函数，
接受用户传入的参数，用来往callbacks里存入回调函数。
从上面的介绍，可以得知timeFunc()一共有三种实现方式。
Promise
MutationObserver
setTimeout
其中Promise和setTimeout很好理解，是一个异步任务，会在同步任务以及更新DOM的异步任务之后回调具体函数。
MutationObserver是HTML5中的新API，是个用来监视DOM变动的接口。
他能监听一个DOM对象上发生的子节点删除、属性修改、文本内容修改等等。
调用过程很简单，但是有点不太寻常：你需要先给他绑回调：
var mo = new MutationObserver(callback)
通过给MutationObserver的构造函数传入一个回调，能得到一个MutationObserver实例，
这个回调就会在MutationObserver实例监听到变动时触发。
这个时候你只是给MutationObserver实例绑定好了回调，他具体监听哪个DOM、监听节点删除还是监听属性修改，还没有设置。
而调用他的observer方法就可以完成这一步:
var domTarget = 你想要监听的dom节点
mo.observe(domTarget, {
      characterData: true //说明监听文本内容的修改。
})
以上就是vue的nextTick方法的实现原理了，总结一下就是：
vue用异步队列的方式来控制DOM更新和nextTick回调先后执行；
microtask因为其高优先级特性，能确保队列中的微任务在一次事件循环前被执行完毕；
因为兼容性问题，vue不得不做了microtask向macrotask的降级方案。

什么是虚拟DOM树？（Virtual DOM）
 虚拟DOM树其实就是一个普通的js对象，它是用来描述一段HTML片段的
1、当页面渲染的时候Vue会创建一颗虚拟DOM树
2、当页面发生改变Vue会再创建一颗新的虚拟DOM树
3、前后两颗新旧虚拟DOM树进行对比，Vue通过diff算法，去记录差异的地方
4、将有差异的地方更新到真实的DOM树中

虚拟DOM树有什么用？　
vue中的虚拟DOM树只会重新渲染页面修改的地方，
大大减少了对真实DOM树的操作。 -------虚拟DOM树是占内容的，但是可以帮我们提高DOM的性能。
可以这样理解，虚拟DOM树是用空间(虚拟DOM树占空间)换时间(虚拟DOM树可以提高DOM效率)。

object.freeze使用
使用场景：我们总会有一个场景，我们只是用来存储某个对象或者数组，并不要求它响应对应的视图，
但在这个过程中vue还是会用object.defineProperty来监听这个数组，这样就是一种浪费。
那就用到object.freeze方法了，它可以防止vue对它的监听，从而避免这种资源的浪费。

vue3新特性
Composition API
Composition API 主要提供两大好处：
1、清晰的代码结构 2、消除重复逻辑
import { ref, computed, watch } from 'vue'
export default {
  setup() {
    /* ---------------------------------------------------- */
    let count = ref(0)
    const countDouble = computed(() => count.value * 2)
    watch(count, newVal => {
      console.log('count changed', newVal)
    })
    const inc = () => {
      count.value += 1
    }
    const dec = () => {
      if (count.value !== 0) {
        count.value -= 1
      }
    }
    /* ---------------------------------------------------- */
    let msg= ref('some text')
    watch(msg, newVal => {
      console.log('msg changed', newVal)
    })
    const changeMessage = () => {
      msg.value = "new Message"
    }
    /* ---------------------------------------------------- */
    return {
      count,
      inc,
      dec,
      countDouble,
      msg,
      changeMessage
    }
  }
}
使用 Composition API 的好处：可以让我们更好地组织代码(state, methods, computed properties, watcher 等)。

2、Multiple root elements：
 Vue 2 中，tempalte 只能取一个根元素。即使我们只有两个 <p> 标记，我们也必须将它们包含在一个 <div> 标记中。在 Vue 3 中取消了这一限制
可以直接在<template></template>中使用任意数量的标签。
3、Suspense
通常前后端交互是一个异步的过程： 默认我们提供一个加载中的动画，当数据返回时配合使用 v-if 来控制数据显示。
Suspense 的出现大大简化了这个过程：它提供了 default 和 fallback 两种状态
<Suspense>
    <template #default>
      <div v-for="item in articleList" :key="item.id">
        <article>
          <h2>{{ item.title }}</h2>
          <p>{{ item.body }}</p>
        </article>
      </div>
    </template>
    <template #fallback>
      Articles loading...
    </template>
  </Suspense>
4、Multiple v-models
Vue 2 只允许在一个组件上使用一个 v-models。在 Vue 3 中，我们可以将任意数量的 v-model 绑定到我们的定制组件上:
 <survey-form v-model:name="name" v-model:age="age">
    {" "}
  </survey-form>
5、Reactivity
vue3修复了vue2中数组无法监听下标的缺陷
6、Transition
vue2使用 v-enter-active, v-enter, v-enter-to 这些状态。
现在 Vue 3 从命名上更直观了: v-enter 变成了 v-enter-from，v-leave 变成 v-leave-from。

Vue 响应式原理
vue.js 是采用数据劫持结合发布者-订阅者模式的方式，通过Object.defineProperty()来劫持各个属性的setter，getter，在数据变动时发布消息给订阅者(watcher)，触发相应的监听回调。
1、从 new Vue 开始，需要observe(监听器)的数据对象进行递归遍历，包括子属性对象的属性，都加上 setter和getter；
同时创建消息订阅器 Dep 用来搜集使用该 Data 的 Watcher。
2、编译模板，创建 Watcher，并将 Dep.target 标识为当前 Watcher。
3、编译模板时，如果使用到了 Data 中的数据，就会触发 Data 的 get 方法，然后调用 Dep.addSub 将 Watcher 搜集起来。
4、数据更新时，会触发 Data 的 set 方法，然后调用 Dep.notify 通知所有使用到该 Data 的 Watcher 去更新 DOM。
将dep.target设置为空，是因为这是个全局变量也是watcher与dep的唯一桥梁，任何时间都只能保证只有一个值。

VUE前端界面在iOS中遇到的坑
一、在需要滑动的位置加上如下css代码：-webkit-overflow-scrolling:touch;
二、界面点反应慢、延时问题解决方案
经过排查，导致问题的原因是iOS嵌套界面之后，界面点击效果会自动产生一个300毫秒的延时，解决方案：
vue 引入 fastclick（npm install fastclick -S）
然后再进行引入 FastClick.attach(document.body);
三、关于new Date()转换时间在iOS中不生效问题
ios不支持yyyy-mm-dd，得用yyyy/mm/dd
四、关于input输入框在iOS中获取到焦点之后界面上移无法回落问题
在联合移动端开发过程中，会遇到input输入框获取到焦点之后，软键盘自动顶起界面，但是失去焦点之后无法回落的问题，解决方法如下，添加如下代码：
const scrollHeight = document.documentElement.scrollTop || document.body.scrollTop || 0;
window.scrollTo(0, Math.max(scrollHeight - 1, 0));
