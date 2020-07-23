## 一、简答题

### 1、当我们点击按钮的时候动态给 data 增加的成员是否是响应式数据，如果不是的话，如果把新增成员设置成响应式数据，它的内部原理是什么。

```
let vm = new Vue({
 el: '#el'
 data: {
  o: 'object',
  dog: {}
 },
 method: {
  clickHandler () {
   // 该 name 属性是否是响应式的
   this.dog.name = 'Trump'
  }
 }
})
```

 答：不是响应式的。响应式对象和响应式数组是指在vue初始化时期，利用Object.defineProperty()方法对其进行监听，这样在修改数据时会及时体现在页面上。给 dog 的 name 属性设置一个初始值，可以为空字符串或者 undefined 之类的就可以是响应式的。

原理：当把一个普通的 JavaScript 对象传入 Vue 实例作为 `data` 选项，Vue 将遍历此对象所有的 property，并使用Object.definProperty或proxy把这些 property 全部转为 [getter/setter](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Working_with_Objects#定义_getters_与_setters)。这些 getter/setter 对用户来说是不可见的，但是在内部它们让 Vue 能够追踪依赖，在 property 被访问和修改时通知变更。每个组件实例都对应一个 **watcher** 实例，它会在组件渲染的过程中把“接触”过的数据 property 记录为依赖。之后当依赖项的 setter 触发时，会通知 watcher，从而使它关联的组件重新渲染。设置Vue实例中的属性名操作的时候会触发 data中属性名 的 setter 操作，data中属性名 的 setter 操作会 walk 这个新的值（walk方法是给data里的对象类型的值设置响应式），而题目中的 data 的 dog 是个空对象，没有任何属性，所以初始化 Vue 实例的时候，在给 dog 设置  Object.definProperty或proxy 的时候没有任何属性有 getter 和 setter 方法，所以在点击按钮动态的给 dog 添加 name 属性，并设置值的时候是不会触发 dog 对象下的属性 name 的 setter 方法，故不是响应式数据。而给 dog 对象添加了 name 的初始值后，dog 对象的 name 属性就有了 getter 和 setter 方法，故可以实现响应式。建议改成如下形式：

let vm = new Vue({
        el: '#app',
        data: {
            msg: 'object',
            dog: {
                name: ''
            }
        },
        method: {
            clickHandler() {
                // 该 name 属性是否是响应式的
                this.dog.name = 'Trump'
            }
        }
    })



### 2、请简述 Diff 算法的执行过程

 答：diff 的过程就是调用名为 patch 的函数，比较新旧节点。

当开发者更改了一处代码进行保存之后，会调用Object.defineProperty中的set方法，set方法会调用Dep.notify方法通知所有订阅者Watcher，订阅者就会调用patch方法去进行dom的判断。在patch方法中会调用sameVnode方法，通过sameVnode方法判断newNode与oldNode是否相同，如果不相同，直接销毁oldNode，将newNode添加到dom中，如果相同，则调用patchVnode方法比较下属节点：

​	如果oldVnode有子节点而newNode没有，则删除dom的子节点。

​	如果他们都有文本节点并且不相等，那么将dom的文本节点设置为newNode的文本节点。 如果oldVnode没有子节点而newNode有，则将newNode的子节点真实化之后添加到dom。

​	如果两者都有子节点，则执行updateChildren函数比较子节点。在updateChildren函数中，使用while循环遍历下面的子节点，并通过回调sameVnode方法继续进行判断，直到子节点对比完成






## 二、编程题

### 1、模拟 VueRouter 的 hash 模式的实现，实现思路和 History 模式类似，把 URL 中的 # 后面的内容作为路由的地址，可以通过 hashchange 事件监听路由地址的变化。

 答案：hash模式主要是用onHashChange()函数实现。

let _Vue = null
class VueRouter {
    static install(Vue){
        //1 判断当前插件是否被安装
        if(VueRouter.install.installed){
            return;
        }
        VueRouter.install.installed = true
        //2 把Vue的构造函数记录在全局
        _Vue = Vue//保存Vue引用
        //3 把创建Vue的实例传入的router对象注入到Vue实例
        // _Vue.prototype.$router = this.$options.router
        _Vue.mixin({//全局混入beforeCreate生命周期方法
            beforeCreate(){
                if(this.$options.router){// 只要在Vue根实例中挂载，利用只有根实例选项上有router属性找到根实例
                    _Vue.prototype.$router = this.$options.router
				}
			}
		})
	}
	constructor(options){
		this.options = options //保存路由选项
		this.routeMap = {}
		// observable
		this.data = _Vue.observable({
			current:"/"
		})
		this.init()
	}
	init(){
		this.createRouteMap()
		this.initComponent(_Vue)
		this.onHashChange()
	}
	createRouteMap(){
		//遍历所有的路由规则 把路由规则解析成键值对的形式存储到routeMap中
		this.options.routes.forEach(route => {
			this.routeMap[route.path] = route.component
		});
	}
	onHashChange () {
		// 这里只是先简单将 # 后的路径赋值给currentPath
		// this.currentPath = window.location.hash.slice(1)
		

		// 获取当前hash路径
		this.current = window.location.hash.slice(1)
		
		this.matched = []
		// 匹配hash路径对应的路由
		this.match()
	}
	match (routes) {
		routes = routes || this.$options.routes
		// 遍历路由选项
		for (let route of routes) {
			if (route.path === '/' && this.current === '/') {
				this.matched.push(route)
				return
			}        
			// 匹配嵌套路由的情况
			if (route.path !== '/' && this.current.indexOf(route.path) !== -1) {
				this.matched.push(route)
				if (route.children) {
					this.match(route.children)
				}
				return
			}
		}
	)
	initComponent(Vue){
		Vue.component("router-link",{
			props:{
				to:String
			},
			render(h){
				return h("a",{
					attrs:{
						href:this.to
					},
					on:{
						click:this.clickhander
					}
				},[this.$slots.default])
			},
			methods:{
				clickhander(e){
					history.pushState({},"",this.to)
					this.$router.data.current=this.to
					e.preventDefault()
				}
			}
			// template:"<a :href='to'><slot></slot><>"
		})
		const self = this
		Vue.component("router-view",{
			render(h){
				// self.data.current
				const cm=self.routeMap[self.data.current]
				return h(cm)
			}
		})
	}
### 2、在模拟 Vue.js 响应式源码的基础上实现 v-html 指令，以及 v-on 指令。

 实现v-html

class Compiler {
  constructor (vm) {
    this.el = vm.$el
    this.vm = vm
    this.compile(this.el)
  }
  // 编译模板，处理文本节点和元素节点
  compile (el) {
    let childNodes = el.childNodes
    Array.from(childNodes).forEach(node => {
      // 处理文本节点
      if (this.isTextNode(node)) {
        this.compileText(node)
      } else if (this.isElementNode(node)) {
        // 处理元素节点
        this.compileElement(node)
      }

// 判断node节点，是否有子节点，如果有子节点，要递归调用compile
  	if (node.childNodes && node.childNodes.length) {
    	this.compile(node)
      }
  })

}
  // 编译元素节点，处理指令
  compileElement (node) {
    // 遍历所有的属性节点
    Array.from(node.attributes).forEach(attr => {
      // 判断是否是指令
      let attrName = attr.name
      if (this.isDirective(attrName)) {
        attrName = attrName.substr(2)
        let key = attr.value
        this.update(node, key, attrName)
      }
    })
  }

  update (node, key, attrName) {
    let updateFn = this[attrName + 'Updater']
    updateFn && updateFn.call(this, node, this.vm[key], key)
  }



**//v-hmtl**

**htmlUpdater(node,value,key) {**

​	**this.innerHTML =  value**

​	**new Watcher(this.vm,key,(newValue) => {**

​		**node.innerHTML = newValue**		

​	**})**

**}**

//v-on

onUpdater(node, value, key) {

​	this.textContext = value

​	new Watcher(this.vm, key ,(newValue) =>{

​		node.addEventListener(key, () => {

​			this.vm[key] - node.value		

​		})

​	})

}

  // 编译文本节点，处理差值表达式
  compileText (node) {
    // console.dir(node)
    // {{  msg }}
    let reg = /\{\{(.+?)\}\}/
    let value = node.textContent 
    if (reg.test(value)) {
      let key = RegExp.$1.trim()
      node.textContent = value.replace(reg, this.vm[key])

      // 创建watcher对象，当数据改变更新视图
      new Watcher(this.vm, key, (newValue) => {
        node.textContent = newValue
      })
    }
  }
  // 判断元素属性是否是指令
  isDirective (attrName) {
    return attrName.startsWith('v-')
  }
  // 判断节点是否是文本节点
  isTextNode (node) {
    return node.nodeType === 3
  }
  // 判断节点是否是元素节点
  isElementNode (node) {
    return node.nodeType === 1
  }
}

### 3、参考 Snabbdom 提供的电影列表的示例，利用Snabbdom 实现类似的效果，如图：

![img](https://s0.lgstatic.com/i/image/M00/26/F2/Ciqc1F7zUZ-AWP5NAAN0Z_t_hDY449.png)

答案：

实现效果代码：

import { init } from '../../build/package/snabbdom.js'

import clazz from '../../build/package/modules/class.js'

import props from '../../build/package/modules/props.js'

import style from '../../build/package/modules/style.js'

import listeners from '../../build/package/modules/eventlisteners.js'

import h from '../../build/package/h.js'



var patch = init([clazz, props, style, listeners])



var vnode



var nextKey = 11

var margin = 8

var sortBy = 'rank'

var totalHeight = 0

var originalData = [

 { rank: 1, title: 'The Shawshank Redemption', desc: 'Two imprisoned men bond over a number of years, finding solace and eventual redemption through acts of common decency.', elmHeight: 0 },

 { rank: 2, title: 'The Godfather', desc: 'The aging patriarch of an organized crime dynasty transfers control of his clandestine empire to his reluctant son.', elmHeight: 0 },

 { rank: 3, title: 'The Godfather: Part II', desc: 'The early life and career of Vito Corleone in 1920s New York is portrayed while his son, Michael, expands and tightens his grip on his crime syndicate stretching from Lake Tahoe, Nevada to pre-revolution 1958 Cuba.', elmHeight: 0 },

 { rank: 4, title: 'The Dark Knight', desc: 'When the menace known as the Joker wreaks havoc and chaos on the people of Gotham, the caped crusader must come to terms with one of the greatest psychological tests of his ability to fight injustice.', elmHeight: 0 },

 { rank: 5, title: 'Pulp Fiction', desc: 'The lives of two mob hit men, a boxer, a gangster\'s wife, and a pair of diner bandits intertwine in four tales of violence and redemption.', elmHeight: 0 },

 { rank: 6, title: 'Schindler\'s List', desc: 'In Poland during World War II, Oskar Schindler gradually becomes concerned for his Jewish workforce after witnessing their persecution by the Nazis.', elmHeight: 0 },

 { rank: 7, title: '12 Angry Men', desc: 'A dissenting juror in a murder trial slowly manages to convince the others that the case is not as obviously clear as it seemed in court.', elmHeight: 0 },

 { rank: 8, title: 'The Good, the Bad and the Ugly', desc: 'A bounty hunting scam joins two men in an uneasy alliance against a third in a race to find a fortune in gold buried in a remote cemetery.', elmHeight: 0 },

 { rank: 9, title: 'The Lord of the Rings: The Return of the King', desc: 'Gandalf and Aragorn lead the World of Men against Sauron\'s army to draw his gaze from Frodo and Sam as they approach Mount Doom with the One Ring.', elmHeight: 0 },

 { rank: 10, title: 'Fight Club', desc: 'An insomniac office worker looking for a way to change his life crosses paths with a devil-may-care soap maker and they form an underground fight club that evolves into something much, much more...', elmHeight: 0 },

]

var data = [

 originalData[0],

 originalData[1],

 originalData[2],

 originalData[3],

 originalData[4],

 originalData[5],

 originalData[6],

 originalData[7],

 originalData[8],

 originalData[9],

]



function changeSort (prop) {

 sortBy = prop

 data.sort((a, b) => {

  if (a[prop] > b[prop]) {

   return 1

  }

  if (a[prop] < b[prop]) {

   return -1

  }

  return 0

 })

 render()

}



function add () {

 var n = originalData[Math.floor(Math.random() * 10)]

 data = [{ rank: nextKey++, title: n.title, desc: n.desc, elmHeight: 0 }].concat(data)

 render()

 render()

}



function remove (movie) {

 data = data.filter((m) => {

  return m !== movie

 })

 render()

}



function movieView (movie) {

 return h('div.row', {

  key: movie.rank,

  style: {

   opacity: '0',

   transform: 'translate(-200px)',

   delayed: { transform: `translateY(${movie.offset}px)`, opacity: '1' },

   remove: { opacity: '0', transform: `translateY(${movie.offset}px) translateX(200px)` }

  },

  hook: { insert: (vnode) => { movie.elmHeight = vnode.elm.offsetHeight } },

 }, [

  h('div', { style: { fontWeight: 'bold' } }, movie.rank),

  h('div', movie.title),

  h('div', movie.desc),

  h('div.btn.rm-btn', { on: { click: [remove, movie] } }, 'x'),

 ])

}



function render () {

 data = data.reduce((acc, m) => {

  var last = acc[acc.length - 1]

  m.offset = last ? last.offset + last.elmHeight + margin : margin

  return acc.concat(m)

 }, [])

 totalHeight = data[data.length - 1].offset + data[data.length - 1].elmHeight

 vnode = patch(vnode, view(data))

}



function view (data) {

 return h('div', [

  h('h1', 'Top 10 movies'),

  h('div', [

   h('a.btn.add', { on: { click: add } }, 'Add'),

   'Sort by: ',

   h('span.btn-group', [

​    h('a.btn.rank', { class: { active: sortBy === 'rank' }, on: { click: [changeSort, 'rank'] } }, 'Rank'),

​    h('a.btn.title', { class: { active: sortBy === 'title' }, on: { click: [changeSort, 'title'] } }, 'Title'),

​    h('a.btn.desc', { class: { active: sortBy === 'desc' }, on: { click: [changeSort, 'desc'] } }, 'Description'),

   ]),

  ]),

  h('div.list', { style: { height: totalHeight + 'px' } }, data.map(movieView)),

 ])

}



window.addEventListener('DOMContentLoaded', () => {

 var container = document.getElementById('container')

 vnode = patch(container, view(data))

 render()

})