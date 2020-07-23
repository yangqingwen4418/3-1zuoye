# 3-1vue笔记

\1. Jslint和jscs可以检查代码规范、UglifyJSHE mINIFYcss优化代码体积，如移除空格、缩短函数和变量名。ImageOption和ImageMagick移除并处理图片元信息，优化图片。

**2.** ***\*调试\*******\*-\****默认支持node调试，代码打断点后，按F5选择nodejs启动调试。若要支持网页调试，需安装Debugger for Chrome，侧边目录导航选择“Extension（ctrl+shift+x）”按钮，然后输入“debugger for Chrome”，安装，然后在选定的根文件夹内建html文件保存，按F5，选中“chrome”，用绝对路径访问的话在生成的launch.json中修改“webRoot：${workspaceFoldef}/***\*wwwroot\****”,再按F5启动，控制台（ctrl+~打开）执行npm install -g http-server，安装完后控制台再输入http-server命令启动，在浏览器会自动打开文件，如下是基本配置方式
{
// Use IntelliSense to learn about possible attributes.
// Hover to view descriptions of existing attributes.
// For more information, visit: https://go.microsoft.com/fwlink/?
linkid=830387
"version": "0.2.0",
"configurations": [
{
"name": "Launch localhost",
"type": "chrome",
"request": "launch",
"url": "http://localhost/index.html",或"http://localhost/8080"
***\*"webRoot": "${workspaceFolder}/wwwroot"\*******\*
\****},
{
"name": "Launch index.html",
"type": "chrome",
"request": "launch",
"file": "${workspaceFolder}/index.html"
},]} 

\3. vscode集成git操作。迁出某个github上的项目，找到vscode的侧边导航栏的source control（ctrl+shift+g），打开view里面的命令面板command Palette（ctrl+shift+p），输入“git:”可过滤出git相关指令，选择git clone，后粘贴github要克隆的项目地址。找到vscode的侧边导航栏的source control（ctrl+shift+g），点一下“+”号，初始化当前库（目录）变成本地的respository，弹出的地址可选当前目录地址，也可选其他目录地址，然后添加远程地址关联当前仓库，在控制面板输入git:add remote,后写一个远程的名字，后附加远程地址，然后就可以做正常的git操作，如提交、更新。方法一可以在控制面板输入命令，方法二可以ctrl+shift+g后，点三个点选择相应的git操作，更新用pull，推送用push。所有前提是本地安装过git

\4. 安装拓展vetur代码检查、高亮等和vscode snippets提供vue快捷键：vbase、vfor、p>input、vmethod、vdata、vcomputed

\5. 用vue-cli创建项目时，选择上router会自动安装vue-router插件且生成vue-router代码基本结构。

第一步：创建视图。使用方式为在src/router/index.js文件中开头引入vue和vue-route：import Vue from ‘vue’ 和import VueRouter from ‘vue-router’。

第二步：然后用Vue.use(VueRouter)注册路由插件，该方法接受一个函数或对象的参数。如果接受的是函数，会直接调用这个函数注册组件，如果传入对象，它会调用传入对象的install方法注册插件，然后定义路由规则，用const routes = []定义一个数组，数组里存储多个对象，这些对象即路由规则，分别有path、name、component；然后用const router = new VueRouter（{routes}）创建路由对象，里面需传递路由规则；最后导出该路由对象export default router。然后在main.js中开头导入刚创建好的router对象：import router from ‘./router’，

第三步：在创建vue实例new Vue({router})的过程中,注册router对象。二三步会给vue注入创建$route（存储路由数据或规则）和$router（提供router的一些常用方法）两个属性。在插件里没法拿到$route但可以想办法拿到$router。

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB4F3.tmp.jpg) 

第四步：在App.vue中创建路由组件的占位<router-view/>。

第五步：后通过<router-link to=‘/’>xxx</router-link>创建连接，所有工作完毕，最后执行npm run server，到浏览器测试效果。

\6. 动态路由通过占位（如/:id）匹配变化的位置，后面通过懒加载的方式提高程序性能。如何在组件中获取动态路由呢？有两种方式：第一种，在组件中使用{{$route.params.id(动态路由名)}},这种方式强依赖路由，即需要有路由或路由传递相应的参数，不推荐第一种方式。解决强依赖的办法是第二种：路由规则中开启props传参。在路由视图文件index.js中定义动态路由时，在下面设置一个***\*props:true\****，开启props，会把URL中的参数传递给组件，在组件中通过props来接收URL参数，与父子组件传值方式一样，然后在组件文件中的<script>中的***\*export default{props：[‘id’]}位置导入props的id，且在<template>中{{id}}\****,通过开启props获取。推荐使用第二种方式。

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB504.tmp.jpg) 

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB505.tmp.jpg) 

\7. 嵌套路由。当多个组件有相同内容时，可以把相同内容提取到一个公共组件中，把不同的部位用<router-view></router-view>占位,在index.js文件中设置嵌套路由时，外部路由path属性与children里面的path会合并，然后会分别按顺序加载path对应的component合并到一起，但外面路径path是根路径path:’/’时,children里面的path可为空’’,children里面path配置时可以是相对或绝对路径，绝对路径是path：‘/’加上外面路径path值和/斜杠,再加剩下的正常的children里的路径参数。

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB506.tmp.jpg) 

\8. 编程式导航。$router.push()可接受字符串或对象，对象必须有name，这个name为router/index.js中的路由配置中的name属性，还可以接受额外的params:{动态路由名：动态路由传递的值}字符串则对应为该文件中path的值。$router.replace()方法与push()方法类似，都可以跳到指定的路径，参数形式也是一样的，但replace（）方法不会记录本次历史，会把当前历史改变为其里面的字符串值。$router.go()，可以传负数，会返回到相应页面，如-1返回上页，-2返回上上页。

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB517.tmp.jpg) 

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB518.tmp.jpg) 

\9. Hash和history模式区别。都是客户端路由的实现方式，即当路径变化时，不会向服务器发送请求，是js监视路径的变化，然后根据不同地址，渲染不同内容，如需服务端内容，会发送ajax请求。表现形式上区别：hash模式后面带#号，#后是路由地址，通过问号携带url参数。History模式则没有额外的符号，要用好history模式，还需要服务端配置支持。原理区别：hash模式是基于锚点及onhashchange事件，通过锚点的值作为路由地址，当地址变化后触发onhashchange事件，根据路径决定页面上呈现的内容，而history模式是基于HTML5中的History API：history.pushState()(IE10后才支持,与history.push方法区别是当调用push时会发生路径变化，会向服务器发送请求，而pushState不会向服务器发送请求，只会改变浏览器地址栏中的地址且将该地址记录到历史记录里，从而可实现客户端路由，但有IE兼容问题，这时候就只能用hash模式)和history.replaceState()

\10. HTML5 History 模式的使用。History 需要服务器的支持；单页应用中，服务端不存在 http://www.testurl.com/login 这样的地址会返回找不到该页面即404页面；在服务端应该除了静态资源外都返回单页应用的 index.html。在项目中添加404.vue文件，然后在router/index.js中添加{path：’*’,name:’404’,component:()=>import

(/* webpackChunkName: “404” */ ‘../views/404.vue’} 

\11. 开启 History 模式 ：const router = new VueRouter({// mode: 'hash',mode: 'history',routes}) 

\12. History模式在node.js中，下图为node开发服务器案例。执行node app.js即可执行。当没有开启history模式时，点击about按钮跳到刷新页面时，刷新浏览器就会发现，由于在node服务器中没有部署about对应的页面，node服务器会发送默认的404页面过来，而不是项目中定义的404.vue。记住当代码改变时需要重启服务器，即执行ctro+c后再输入一遍启动指令node app.js。当在服务器开启了history后会调用历史的项目中的404.vue，而服务器的默认404不会起作用。

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB519.tmp.jpg) 

\13. history模式在nginx服务器中的配置。
第一步，从官网下载 nginx 的压缩包：http://nginx.org/en/download.html
第二步，把压缩包解压到 c 盘根目录(目录名不能有中文)，c:\nginx-1.18.0 文件夹
第三步，修改 conf\nginx.conf 文件

location / {

root   html; 
index   index.html index.htm;

\#新添加内容
\#尝试读取$uri(当前请求的路径)，如果读取不到读取$uri/这个文件夹下的首页
\#如果都获取不到返回根目录中的 index.html
try_files $uri $uri/ /index.html;} 

第四步，打开命令行，切换到目录 c:\nginx-1.18.0
	第五步，nginx 启动、重启和停止.#启动start nginx(会在后台启动，不会阻塞命令	行的运行)

   \# 重启nginx -s reload  # 停止nginx -s stop 

Nginx默认的html目录为放置我们的网站，为项目的根目录。要把打包的项目放到html文件夹中，用自己项目中的index.html替代默认的index.html文件。在cmd中切换到niginx安装目录后测试安装成功与否指令start ng不需要全写完niginx，按tab键会自动补齐，若没任何输出则成功，在浏览器输入localhost即可启动，不需要加端口号，默认80端口，如果该端口被占用了，可以关掉占用的程序，也可以修改nginx文件-conf/nginx.conf修改http里的server中的listen，其中里面的location/{root html即设置html目录为根目录;index index.html设置首页;***\*try_files $uri $uri/ /index.html;可解决nginx中的history模式\****}，重启服务器刷新浏览器后，向服务器请求地址，niginx会根据路径找到对应的文件，如果找不到但设置了try_files时会默认返回根目录下的index.html，当浏览器接收到该网页后，会再去判断这个路由地址，会在客户端解析对应的组件后呈现。

\14. VueRouter原理。

\15. Vue-router使用（如下代码）首先导入vue-router模块，因为是vue的插件，所以调用Vue.use（）来注册插件，vue功能的强大就在于它的插件机制，vue-router和vuex和一些组件都是通过插件形式实现的，Vue.use（）方法可以传入函数或对象，若传入函数，vue内部会直接调用这个函数，如果是对象会调用对象里的install方法，故如果传入的是对象要实现一个install方法，接下来用new VueRouter()实现一个构造实例，所以vue-router应该是一个构造函数或类，且有个静态的install方法，vue-router需要接受一个参数，该参数是对象形式传入一些路由规则，核心是路由规则和对应的组件，最后在new Vue（）里传入刚创建的new VueRouter()对象。下图为VueRouter类图，



![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB529.tmp.jpg) 

Vue.use(VueRouter)
// 创建路由对象
const router = new VueRouter({
routes: [
{ name: 'home', path: '/', component: homeComponent }]})
// 创建 Vue 实例，注册 router 对象
new Vue({
router,
render: h => h(App)
}).$mount('#app')

实现类中的属性和方法，上面是类名，中间是类的属性，下面是类的方法。options属性记录构造函数中传入的对象，options用来记录new VueRouter()传入的对象，即各种路由规则，routeMap是个对象，用来记录路由地址和组件对应的关系，将来会把路由规则解析到routeMap里面来，data是个对象，里面有个属性current，用来记录当前路由地址的，data对象是响应式对象，因为路由地址变化后对应组件要自动更新，可以调用vue.observable方法。下面的方法中前面带加号的为对外公开的方法，下划线是静态的方法，install就是用来实现vue的插件机制，构造函数constructor初始化上面的属性；剩下的为初始化方法：init方法调用下面几个方法，把不同的代码分割到不同的方法中实现，initEvent方法用来注册propState，监听浏览器历史变化，createRouteMap方法是初始化routeMap属性的，把构造函数中传入的对象转换成键值对形式存储到routeMap里，routeMap是对象，键是地址，值是对应的组件，在router-view组件会使用routeMap，initComponents用来创建router-link和router-view两个组件的。

实现history模式模拟思路和代码如下：

第一步，实现install方法的模拟。创建 LVueRouter 插件

 ![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB52A.tmp.jpg) 

let _Vue = null

export default class VueRouter {

static install (Vue) {//Vue.use（）调用install()会传递2个参数，一个是vue的构造函数，二个是可选的选项对象，
// 如果插件已经安装直接返回
if (VueRouter.install.installed && _Vue === Vue) return
VueRouter.install.installed = true
_Vue = Vue
Vue.mixin({
beforeCreate () {
// 判断 router 对象是否已经挂载了 Vue 实例上
if (this.$options.router) {//排除组件也调用，只vue实例调用
// 把 router 对象注入到 Vue 实例上
_Vue.prototype.$router = this.$options.router}}})}

第二步，实现 LVueRouter 类 - 构造函数

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB52B.tmp.jpg)
实现 LVueRouter 类 - initRouteMap()和注册事件及router-link 和 router-view 组件

constructor (options) {
this.options = options
// 记录路径和对应的组件
this.routeMap = {}
this.app = new _Vue({data: {// 当前的默认路径current: '/'}})} 

第三步，实现createRouteMap()方法。

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB53C.tmp.jpg) 

第四步，实现initComponent方法和init方法



![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB53D.tmp.jpg)![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB53E.tmp.jpg)



![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB53F.tmp.jpg)

Vue-cli创建的项目默认使用运行时版本，不包含编译器（编译器的作用是把template编译为render函数），故不支持template模板，解决办法一：要切换为带编译器的完整版，在项目根目录下新建vue.config.js，输入如下内容：module.exports={runtimeCompiler：true}，然后重启服务器。官网如下。

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB540.tmp.jpg) 

解决办法二：写一个render()函数，如下图：

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB541.tmp.jpg) 

第五步，实现router-view和router-link。实现router-link时要注意既不刷新浏览器，又在客户端改变浏览器地址栏中的地址。history的pushState方法，可以改变浏览器地址栏中的地址，但不会向服务器发送请求，具体代码如下图：

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB542.tmp.jpg) 

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB543.tmp.jpg) 

第六步，实现initEvent。浏览器历史改变的时候加载地址栏中的地址对应的组件并且把它渲染出来，要用到propState，该事件当历史发生变化的时候触发的，调用pushState和replace方法时，是不会触发propState的。

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB544.tmp.jpg) 

}

\16. Vue响应式原理。

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB554.tmp.jpg) 

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB555.tmp.jpg) 

Vue2.0中响应式原理，核心是Object.defineProperty()方法：

MDN中的Object.defineProperty()：[https://developer.mozilla.org/zh-CN/](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)

docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB556.tmp.jpg) 

监听多个时，则需要循环：

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB557.tmp.jpg) 

Vue3中数据响应式是采用proxy实现的，数据劫持整体采用es6的proxy，直接监听对象，而非属性，则监听多个属性时不需要使用循环，但IE不支持，性能有浏览器优化，性能比definProperty()更好。

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB568.tmp.jpg) 

\17. 发布订阅模式和观察者模式。这两种模式本质是一样的，兄弟组件，调用$emit()的是发布者，触发事件，发布消息；调用$on()的是订阅者，注册事件（可注册多个事件，也可给同一个事件注册多个处理函数，存储事件时，记录所有的事件名称及对应的处理函数，即键值对形式），订阅消息。源代码如下图：

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB569.tmp.jpg)![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB56A.tmp.jpg) 

\18. 观察者模式。

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB56B.tmp.jpg)
观察者模式：

观察者(订阅者) -- Watcher
	update()：当事件发生时，具体要做的事情
目标(发布者) -- Dep
	subs 数组：存储所有的观察者
	addSub()：添加观察者
	notify()：当事件发生，调用所有观察者的 update() 方法
没有事件中心 。

实现源码：

// 目标(发布者)- Dependency
class Dep {
	constructor () {
		// 存储所有的观察者
		this.subs = []
	} // 添加观察者
	addSub (sub) {
		if (sub && sub.update) {

this.subs.push(sub)
}} // 通知所有观察者
notify () {
	this.subs.forEach(sub => {
		sub.update()})}} // 观察者(订阅者)

class Watcher {	update () {console.log('update')}} 

// 测试
let dep = new Dep()
let watcher = new Watcher()
dep.addSub(watcher)
dep.notify()

\19. 发布订阅模式与观察者模式对比总结：

观察者模式：当目标对象发生变化时，会调用他的notify()方法，通知所有观察者(订阅者),调用订阅者的update方法做各自的业务，谁对目标对象的变化有兴趣，就调用目标对象的addSub()方法，把自己订阅到目标对象里来，发布者和观察者存在相互依赖的关系。

发布订阅模式：多了一个事件中心来隔离发布者和订阅者，发布者发布事件，会调用事件中心的$emit()方法，$emit()方法会找到事件中心对应的事件处理函数并执行，该事件又由订阅者提供的，订阅者要知道事件中心该事件的变化就需要到事件中心订阅该事件，事件中心作用是隔离发布者和订阅者，去除她们之间的依赖关系，从而变得更灵活。

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB57C.tmp.jpg) 

\20. 模拟vue响应式原理分析。Vue内部构造函数需要把data中的成员转换成getter和setter注入到vue实例上，这样做的目的是在其他地方使用时可以用’this.data中的属性名’这种形式来使用，data中的成员被记录到$data中，并且转化成getter和setter，$data中的setter是真正监视数据变化的地方，$options把构造函数的参数记录到了$options中，_data和$data指向同一个对象，下划线开头的是私有成员，$开头的是公共成员，只需模拟$data即可，$el对应选项中的el,可以使选择器也可以是dom对象，如果是选择器，vue构造函数需要把选择器转换成dom对象。要模拟的最简短版vue结构如下图：

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB57D.tmp.jpg) 

\21. Vue。可以用js中的构造函数也可以用ES6中的类实现。哲学：每个函数或方法尽可能小，并且只有一个功能即只做一件事，这样方便引用。

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB59D.tmp.jpg) 



 ![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB59E.tmp.jpg)    

 3个属性记录构造函数传过来的参数。

_proxyData()作用是把data中的属性转换为

getter和setter注入到Vue实例中。 

Vue模拟实现源码如下图：注意箭头函数的使用是为了不改变this的指向。

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB5A0.tmp.jpg) 

\22. Observer。

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB5A1.tmp.jpg) 

Walk()方法，遍历data中的所有属性，所以他的参数是data对象。defineReactive()方法定义响应式数据，通过调用Object.defineProperty()把属性转化为getter和setter，walk（）方法在循环中会调用defineReactive（）方法。实现模拟源码如下图：注意三点：1、在html中引入vue.js和observer.js时，注意依赖关系的先后顺序，若A.js文件对B.js文件依赖，则把B.js文件放在A.js前面。2、注意不能obj[key]代替val，因为每次调用obj[key]就会调用get()方法，会形成死递归，而是只能用val。3、val作为参数局部变量，应该释放掉，因为obj就是$data这个属性，而$data引用了get()方法，而get()方法又用到了val，形成闭包，故不会释放。

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB5A2.tmp.jpg) 

\23. Vue中data的属性如果是对象，这个对象也是响应式的，对象中的属性也是响应式的，解决办法是在defineReactive()中调用一次walk()方法，如果是对象则遍历属性。如果重新赋值修改属性值，要使是响应式的，需在set()方法里面调用walk()方法，但要注意this指向问题，用that=this，替代this。

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB5A3.tmp.jpg) 

\24. Compiler。功能其实是在操作dom。下面的compiler方法都是在执行dom，之所以有这么多方法，是因为写在一个方法里臃肿，且不利于维护，拆开成多个方法，方便组装。解析是文本还是元素节点，后调用不同的方法。注：console.dir(a)代表把里面的a以对象形式打印出来。



![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB5B3.tmp.jpg) 

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB5B4.tmp.jpg) 

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB5B6.tmp.jpg) 

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB5B7.tmp.jpg) 

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB5B8.tmp.jpg) 

\25. Dep。

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB5B9.tmp.jpg) 

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB5CA.tmp.jpg) 

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB5CB.tmp.jpg) 

\26. Watcher。不同的watcher更新的视图是不一样的，所以watcher有cb属性，即callback回调函数，当new Watcher时需要传入一个回调函数，这个回调函数里应指明如何更新视图，当更新视图是还需要一个数据，故需设置key，key即data中的属性名称，再拿到vm中的实例，就可获取到key属性对应的值。oldValue用来记录数据变化之前的值，update()方法可以获取最新的值，拿到新旧值后比较，如有变化则更新视图，没变化不更新。



![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB5CC.tmp.jpg) 

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB5CD.tmp.jpg) 

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB5CE.tmp.jpg)



![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB5CF.tmp.jpg) 

\27. 模拟vue双向绑定机制。当视图即文本框改变时，data中的数据也要改变,当文本框内容发生变化时，会触发一个相应的事件，取名为input事件，只要在该事件触发时，把文本框中的值取出来重新赋给data。

![img](file:///C:\Users\qinhe\AppData\Local\Temp\ksohtml\wpsB5D0.tmp.jpg) 

\28.  Snabdom翻译文档地址：https://github.com/coconilu/Blog/issues/152。Snabbdom源码地址：https：//github.com/snabbdom/snabbdom

