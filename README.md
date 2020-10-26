# vue-cli4

## 安装步骤

1. 安装 `node.js` <br>

    官网下载安装

2. 准备npm

    ``` shell
    npm -v 
    
    npm config get registry //查看镜像，不是国内，设置淘宝镜像即可 

    npm config set registry https://registry.npm.taobao.org
    ```
    如果想要设置 `npm` 下载的全局组件的安装位置<br>
    首先，可以使用 `npm config ls` 命令查看npm的配置信息<br>
    然后使用 `npm config set prefix "you path"` 修改路径

3. 安装vue-cli
    ``` shell
    npm i @vue/cli -g
    ```
    运行 `vue --version` 指令来查看是否安装成功

    此时就已经可以使用`vue-cli`了
    
----
## 快速原型开发

- 快速原型开发

    > 调试单个vue文件而非整个项目<br>

    安装扩展
    ```
    npm i @vue/cli-service-global -g
    ```

    使用 `vue serve` 目录，可以直接运行；使用 `vue build` 可以打包
    ```
    vue serve

    vue build
    ```
    
    当需要直接运行非默认名称的 `vue` 文件(`App.vue`)时，可以使用以下指令
    ```
     vue serve -c index.vue
    ```
    >`-o, --open` 打开浏览器<br>
     `-c, --copy` 将本地 URL 复制到剪切板<br>
     `-h, --help` 输出用法信息 


----
## vue 项目

- 项目创建

    ```
    vue create projectName
    ```

- 运行项目
    进入项目目录然后执行
    ```
    npm run serve
    ```

- 打包
    ```
    npm run build
    ```
# 路由

> 路由一般用来构建单页面项目

## 基本使用

- 定义路由

    ``` javascript
    <script>
        const writeback = {
            template : '#writeback'
        };
        const call = {
            template : '#call'
        };
        const praise = {
            template : '#praise'
        };
        const router = new VueRouter({
            routes: [
                { //默认的路由跳转
                    path: "/",
                    redirect: "/writeback"
                },
                {
                    name: "writeback", //路由名称
                    path: "/writeback", //路由路径
                    component: writeback //路由绑定组件
                },
                {
                    name: "call",
                    path: "/call",
                    component: call
                },
                {
                    name: "praise",
                    path: "/praise",
                    component: praise
                }
            ]
        });
        let vm = new Vue({
            el: "#app",
            router : router //定义路由
        })
    </script>
    ```

- 使用
    > 需要注意的是，当 `a` 标签被点击时会给该 `a` 标签添加 `class` `.router-link-exact-active .router-link-active` ，可以利用该 `class` 对式样进行调整

    ``` html
    <!-- route-link会自动解析为跳转至对应路径的 a 标签 -->
    <li><router-link to="/writeback">回复我的</router-link></li>
    <li><router-link to="/call">@我的</router-link></li>
    <li><router-link to="/praise">收到的赞</router-link></li>

    <!-- router-view则会加载对应路由所绑定的组件 -->
    <router-view></router-view>
    ```

## 路由模式

- `hash`模式
    > `http://127.0.0.1:5500/vue2/test11/index2.html#/static/writeback`<br>
    > 好处： <br> 无需后端参与，完全的前端路由<br>
    兼容性好, 浏览器都能支持<br>
    > 缺点： 不美观，不符合规范

- `history`模式
    > `http://127.0.0.1:5500/static/writeback`<br>
    > 优点： 符合规范，美观<br>
    > 缺点： 需要后端配合使用<br>
    ``` javascript
    const router = new VueRouter({
        mode : 'history',
        routes: []
    })
    ```

## 动态路由

|    模式    |   路径    |   获取参数     |
|    ----    |   ----    |   ----        |
| user?id=5  | user?id=5 | $route.query  |
| /user/:id  | /user/5   | $route.params |

<br>

- `query` 模式 <br>
    ``` html
    <li><router-link to="/writeback?id=1">回复我的</router-link></li>

    <template id="writeback">
        <div>
            <div>回复我的</div>
            <div>这是回复我的第{{$route.query.id}}条消息</div>
        </div>
    </template>
    ```

- `param` 模式 <br>
    ``` html
    <template id="call">
        <div>
            <div>@我的</div>
            <div>这是@我的第{{$route.params.id}}条,名字叫做{{$route.params.name}}</div>
        </div>
    </template>
    ```

    ``` javascript
    {
        path: '/call/:id/:name',
        name: 'call',
        component: call
    },
    ```
    访问连接 `/call/23/jack`


- 动态路由监听
    > 监听路由的变化
    ``` javascript
    //在父组件进行监听
    watch: {
        $route(to, from){
            console.log(to);
            console.log(from);
        }
    },
    ```
- 捕获没有匹配到的路由
    ``` javascript
    { 
        path: '*', 
        name: 'Home', 
        component: Home 
    },
    { 
        path: '/user-*', 
        name: 'Home', 
        component: Home 
    },
    ```

## 路由传参

- 直接使用
    ``` html
    <!-- params 模式  -->
    <div>{{$route.params.id}}</div>
    <!-- query 模式  -->
    <div>{{$route.query.id}}</div>
    ```
    但是使用 `$route` 过于冗长，希望能够直接使用 `id`

- 动态路由 `params` 传参 布尔模式

    关键字 : `props: true`
    ``` html
    <template id="msg">
        私信列表
        <li v-for="i in userlist">
            <router-link :to="{name: 'msgContent', params:{id: i.id}}">{{i.name}}</router-link>
        </li>
        <router-view></router-view>
    </template>
    <template id="msgContent">
        <div>
            <!-- 此处可以直接使用 -->
            我是用户 {{id}} 的聊天框
        </div>
    </template>
    ```


    ``` javascript
    {
        path: '/msg',
        name: 'msg',
        component: msg,
        children: [
            {
                name: 'msgContent',
                path: ':id',
                component: msgContent,
            // 设置为 true 后，所有传递进来的参数，都会默认被注册为 对应的数据
                props: true
            }
        ]
    }

    const msgContent = {
        template: '#msgContent',
        //在此处定义参数
        props: ['id']
    }
    ```

- 动态路由 `query` 传参
    ``` html
    <template id="msg">
        <div class="msg-list">
            私信列表
            <li v-for="i in userlist">
                <router-link :to="{path: '/msg/msg-content', query:{id: i.id}}">{{i.name}}</router-link>
            </li>
        </div>
        <div class="msg-content">
            聊天内容
            <router-view></router-view>
        </div>
    </template>
    <template id="msgContent">
        <div>
            我是用户 {{id}} 的聊天框
        </div>
    </template>
    ```

    ``` javascript
    {
        path: '/msg',
        name: 'msg',
        component: msg,
        children: [
            {
                path: 'msg-content',
                component: msgContent,
                //将参数一一对应
                props: route => ({
                    id: route.query.id
                })
            }
        ]
    }

    const msgContent = {
        template: '#msgContent',
        props: ['id']
    }
    ```

    如果是命名视图
    ``` javascript
    {
        path: '/msg',
        name: 'msg',
        component: msg,
        children: [
            {
                path: 'msg-content',
                components: {
                    default: msgContent,
                    content2: content2
                },
                //将参数一一对应
                props: {
                    default: route => ({
                        id: route.query.id
                    })
                }
            }
        ]
    }
    ```

- 静态路由

    ``` javascript
    //路由内的 props
    props : {
        age : 23,
        name: '李四'
    },

    //如果是命名视图
    props : {
        default : {
            age : 23,
            name: '李四'
        }
    }
    ```

- 总结

    > 无论是哪种路由，组件内的 `props` 都是一样的写法

    > 区别在于

    ``` javascript
    // params 动态路由
    props : true,

    // query 动态路由
    props : route => ({ 
        search: route.query.search 
    }),

    //静态路由
    props : {
        default : {
            age : 23,
            name: '李四'
        }
    }
    ```

## 嵌套路由

- 嵌套路由 
    ``` html
    <template id="msg">
        <div>
            <div>我的私信</div>
            <div class="msg-list">
                私信列表
                <li v-for="i in userlist">
                    <router-link :to="'/msg/'+i.id">{{i.name}}</router-link>
                </li>
            </div>
            <div class="msg-content">
                聊天内容
                <router-view></router-view>
            </div>
        </div>
    </template>
    <template id="msgContent">
        <div>
            我是用户的聊天框
        </div>
    </template>
    ```

    ``` javascript
    {
        path: '/msg',
        name: 'msg',
        component: msg,
        children: [
            {
                path: ':id',
                component: msgContent,
            }
        ]
    }
    ```


## 编程式导航

- 路由跳转

    即点击事件再手动跳转的行为
    相较于 `routerlink` 更加灵活一点

    ``` html
    <div class="msg-list">
        私信列表
        <li v-for="i in userlist">
            <button @click="getMsgContent(i.id)">{{i.name}}</button>
        </li>
    </div>
    ```

    方法中的 `this` 即为当前组件
    ``` javascript
    const msg = {
        template: '#msg',
        props: ['userlist'],
        methods: {
            //方法要定义在 routerlink 所在的组件下，即当前组件
            getMsgContent(id){
                //默认方式  参数即 path  '/'代表根目录， 如果想写子路由去掉最前面的 '/'
                this.$router.push('/content');

                //对象方式1 使用路由名
                this.$router.push({
                    name: 'msgContent' //路由名是区分大小写的
                })

                //对象方式2 使用path
                this.$router.push({
                    path: '/content'  //同默认方式的写法
                })

                //params 传值
                this.$router.push({
                    name: 'msgContent', // 固定写法 name 与 params结合
                    params: {id}
                });

                //query 传值
                this.$router.push({
                    path: '/msgContent', // path 和 query 结合
                    query: {id}
                })
            }
        },
    }

    const router = new VueRouter({
        routes: [
            {
                path: '/msg',
                name: 'msg',
                component: msg,
                children: [
                    {
                        name: 'msgContent',
                        path: ':id',
                        component: msgContent,
                    }
                ]
            }
        ]
    })
    ```

- 上下页

    直接这样用

    ``` html
    <button @click="go(-1)">goUp</button> 
    <button @click="go(1)">goDown</button> 
    ```
    ``` javascript
    go(step) { 
        this.$router.go(step) 
    },
    ```

## 命名视图和命名路由

- 命名视图

    >使用命名视图的前提是，一个路由需要绑定多个组件，这多个组件都要同时使用
    ``` javascript
    const router = new VueRouter({
        routes: [
            {
                path: "/",
                redirect: "writeback"
            },
            {
                name: "writeback",
                path: "/writeback",
                components: { // 一个路由可以绑定多个组件
                    default: writeback,
                    footer: writebackFooter //这里给组件 writebackFooter 一个命名 footer
                }
            }
        ]
    )}
    ```

    ``` html
    <!-- 当访问writeback路由时，router-view默认会访问default组件 -->
    <router-view></router-view>  
    <!-- 带上属性name就可以加载绑定的其他组件 -->
    <router-view name="footer"></router-view>
    ```

- 命名路由

    > 当路由较为复杂时，可以给路由起名，访问路由时，访问路由的名称即可
    ``` javascript
    {
        name: "writeback", //当路由较为复杂时，可以给路由起名，访问路由时，访问路由的名称即可
        path: "/static/writeback",
        component: writeback
    }
    ```

    > 注意要绑定 `to` 属性
    ``` html
    <!-- 需要绑定to属性 -->
    <li><router-link :to="{name:'writeback'}">回复我的</router-link></li>
    <li><router-link :to="{name:'call'}">@我的</router-link></li>
    <li><router-link :to="{name:'praise'}">收到的赞</router-link></li>
    ```

    > 也可以实现参数的绑定
    ``` html
    <router-link :to="'/msg/'+i.id">{{i.name}}</router-link>
    ↓
    <router-link :to="{name:'msg', params:{id: i.id}}">{{i.name}}</router-link>

    <router-link :to="'/msg?id='+i.id">{{i.name}}</router-link>
    ↓
    <li><router-link :to="{path:'/msg', query:{id: i.id}}">{{i.name}}</router-link></li>
    ```

## 重定向

- 普通重定向
    ``` javascript
    //地址为/test时重定向至首页
    {
        path: '/test',
        redirect: '/' //默认为path
    },
    //重定向至命名路由
    {
        path: '/test',
        redirect: {
            mame: 'List'
        }
    },
    ```

- 闭包处理
    ``` javascript
    //跳转前的逻辑处理
    {
        path: '/test',
        redirect: () => { //支持闭包处理
            if(true){
                return '/'; //返回值可以写默认的path

                return {
                    name : 'List'
                }           //也支持返回对象
            }
        }
    },
    //该闭包也支持获取跳转路由的信息
    {
        path: '/test',
        redirect: to => { // 这个参数即为跳转后的路由的信息
            console.log(to);
            return '/';
        }
    },
    ```

- 别名 
    ``` javascript
    // 别名，不是单纯的跳转至新的路由，而是显示别名，加载新的路由
    { 
        path: '/list', 
        name: 'List', 
        alias : '/liebiao', 
    }
    ```

## 元信息

- 元信息

    可以给路由配置一个 `meta` 属性，这个属性一般设置对象即可<br>
    该属性是固定名称，不可自定义

    ``` javascript
    {
        name: 'Login',
        path: '/login',
        component: login,
        meta: {
            info: '登陆页面'
        }
    }
    router.beforeEach((to, from, next) => {
        if(to.meta.info !== undefined){
            console.log(to.meta.info);
        }
        next();
    })
    ```

## 懒加载

- 懒加载

    跳转到路由时，再加载对应组件，提高运行效率

    ``` javascript
    { 
        path: '/about', 
        name: 'About', 
        component: () => import('../views/About.vue') 
    }
    ```

## 滚动重置

- 滚动重置

    我们跳转到新路由组件时，在有足够高度的情况下会记住当前位置；<br> 我们不需要记住当前位置，直接让它 top，或者指定位置


    ``` javascript
    const router = new VueRouter({ 
        mode : 'history', 
        routes, 
        scrollBehavior(to, from) { 
            return { x: 0, y: 0 } 
        } 
    })
    ```

    也支持前进后退时保留
    ``` javascript
    scrollBehavior (to, from, savedPosition) { 
        if (savedPosition) { 
            return savedPosition //保存之前的位置 
        } else { 
            return { x: 0, y: 0 } 
        } 
    }
    ```


# 导航守卫

## 全局前置守卫

> 用于跳转或取消跳转的导航功能，如：登录跳转

- 使用
    >为了避免无限递归，需要给请求一个出口，即 `next()`

    ``` javascript
    //从后端请求回来登陆状态
    let logStatus = false;

    //根据登陆状态加载页面
    router.beforeEach((to, from, next) => {
        if(logStatus){
            if(to.name == 'Login' || to.name == 'Register'){
                next('/mypage'); // 此时是登录状态下的第一次点击，重定向到 mypage
            }else{
                next(); // 重定向后的请求 会访问到这里来， 或是在登录状态下直接访问mypage 此时放行
            }
        }else{
            if(to.name == 'Login'){
                next(); // 非登录状态下 访问login 放行
            }else{
                next('/login'); // 非登录状态下 访问其他路由，重定向到 login
            }
        }
    }))
    ```

- 三个参数

    `to` 代表将要进入的路由对象<br>
    `from` 代表将要离开的路由对象<br>
    `next` 即跳转行为
    ``` javascript
    next()         //继续执行跳转
    next('/login') //跳转至`login`页面
    next({
        name : 'Login' //也支持对象模式
    })
    ```

## 全局后置守卫

>用于页面加载完成后的一些操作 如： 回归滚动条，懒加载结束，更新页面title等

- 两个参数

    `to` 和 `from` 都同上

    ``` javascript
    router.afterEach((to, from)=>{
        
    })
    ```

## 路由独享守卫

> 仅供某个路由使用

- 使用

    ``` javascript
    name: 'Login',
    path: '/login',
    component: login,
    beforeEnter: (to, from, next) => {
        //因为触发时一定是在点击当前路由时，所以无需判断to.name
        if(logStatus){
            next('/mypage');
        }else{
            next();
        }
    }
    ```

## 组件内守卫

- 使用

    ``` javascript
    const login = {
        template: '#login',
        beforeRouteEnter (to, from, next) {
            console.log(this);
            console.log('访问组件时触发');
            //因为是在组件激活前就触发了，即使没有写next，函数体内的代码也依旧会执行
            next();
            //同时访问到的 this 是 window 对象
            //想要访问到当前组件对象，则需要在 next 中使用回调
            next(vm => {
                console.log(vm);
            });
        },
        beforeRouteLeave (to, from, next) {
            console.log(this);
            console.log('离开组件时触发');
            next();
        },
        beforeRouteUpdate (to, from, next) {
            //如使用动态路由时会触发
            console.log(this);
            console.log('组件复用时触发');
            next();
        }
    }
    ```

## 触发流程

1、 触发路由 <br>
2、 调用失活组件内的方法 `beforeRouteLeave` <br>
3、 调用全局 `beforeEach`<br>
4、 调用重用组件内的方法 `beforeRouteUpdate` <br>
5、 调用路由独享守卫 `beforeEnter`<br>
6、 解析组件<br>
7、 调用被激活的组件内的 `beforeRouteEnter`<br>
8、 调用全局 `beforeResolve`<br>
9、 路由激活<br>
10、调用全局 `afterEach`<br>
11、DOM更新<br>
12、调用 `beforeRouteEnter` 中的回调函数

|   系统  | 全局 |   路由内    | 组件内  |
|   ----  |----  |   ----     |   ----  |
|触发路由  |     |    |                  |
|         |     |    | beforeRouteLeave (其他组件) |
|         |beforeEach|        |          |
|         |     |    | beforeRouteUpdate (组件再次激活前) |
|         |     | beforeEnter |          |
|解析组件  |     |    |                   |
|         |     |    | beforeRouteEnter  |
|         | beforeResolve |    |         |
|路由激活  |     |              |         |
|         |afterEach|          |         |
|DOM更新  |     |              |         |
|         |     |    | beforeRouteEnter next 回调 |
|触发新路由|     |              |         |
|         |     |    | beforeRouteLeave (当前组件) |
|   ....  |     |             |         |
|         |     |              |         |


## 使用建议

组件内守卫有一个优点，即可以使用 `this` 去调用方法或者去修改数据，利用这个特定可以进行初始化，或是重置的操作,此时的 `this` 是组件对象

全局守卫和路由内守卫 中获得的 `this` 则是 `window` 对象


# vuex

引入地址 ：https://unpkg.com/vuex

## store模式

>官方建议，如果不是大型的单页面应用，没有必要使用 `vuex`，可以使用 `store` 模式去代替

- `store`模式

    ``` javascript
    //共享store
    const store = {
        //共享的数据
        state: {
            num: 0
        },
        //操作数据的方法
        increment: function() {
            this.state.num++;
        }
    }

    //组件内
    const page1 = {
        template : '#page1',
        data() {
            return {
                num: 0,
                //和store的数据绑定
                storeState: store.state
            }
        },
        methods: {
            //调用store内的方法
            increment: function(){
                store.increment();
            }
        },
    }
    ```
    `store` 的数据可以灵活的在多个组件中使用
    ``` html
    <template id='page1'>
        <div>
            <div>私有：{{num}}</div>
            <button @click="num++">私有+</button>

            <div>共享：{{storeState.num}}</div>
            <button @click="increment">共享+</button>
        </div>
    </template>
    ```

## state 状态设置 mapState

- 传统做法

    利用 `计算属性` 去获取和修改 `state`

    ``` html
    <div>
        {{name}}<br>
        {{age}}<br>
        <input type="text" name="" id="" v-model="age"><br>
        {{gender}}
    </div>
    ```

    ``` javascript
    const page1 = {
        template: '#page1',
        computed: {
            name: function(){
                return this.$store.state.name;
            },
            gender: function(){
                return this.$store.state.gender;
            },
            age: {
                get: function(){
                    return this.$store.state.age;
                },
                set: function(value){
                    this.$store.commit('setAge', value);
                }
            },
        },
    }

    const store = new Vuex.Store({
        state: {
            name: '张三',
            age: 23,
            gender: '男'
        },
        mutations: {
            setAge : function(state, value){
                state.age = value;
            }
        }
    })
    ```
    >缺点： `state` 较多时，重复代码量过大

- `mapState` 数组

    利用 `mapState` 可以不用定义大量的计算属性<br>
    也是因为没有定义计算属性，无法使用 `v-model` 双向绑定修改数据，因此通过触发输入事件去修改数据
    ``` html
    {{name}}<br>
    {{age}}<br>
    <input type="text" name="" id="" :value="age" @input="setAge"><br>
    {{gender}}
    ```

    `cli` 项目使用 `mapState` 需要导出辅助函数
    ``` javascript
    import { mapState } from 'vuex'
    computed: mapState(['name', 'age', 'gender']),
    ```

    引入的 `vuex` 可以直接这样使用
    ``` javascript
    computed: Vuex.mapState(['name', 'age', 'gender']),
    ```

    ``` javascript
    const page1 = {
        template: '#page1',
        computed: Vuex.mapState(['name', 'age', 'gender']),
        methods: {
            //事件
            setAge: function(e){
                //事件触发时，获取当前value赋值给state
                this.$store.commit('setAge', e.target.value);
            }
        },
    }

    const store = new Vuex.Store({
        state: {
            name: '张三',
            age: 23,
            gender: '男'
        },
        mutations: {
            setAge : function(state, value){
                state.age = value;
            }
        }
    })
    ```

    >缺点：如果 `state` 和模板插值的名不同，则无法使用<br>

- `mapState` 对象

    对象写法中，可以修改插值的名，也可以在数据插值前再处理一下<br>
    这种写法仍然需要使用事件触发的方式修改 `state`

    ``` html
    <div>
        {{mingzi}}<br>
        {{nianling}}<br>
        <input type="text" name="" id="" :value="nianling" @input="setAge"><br>
        {{xingbie}}
    </div>
    ```

    ``` javascript
    const page1 = {
        template: '#page1',
        computed: Vuex.mapState({
            //此处是箭头函数的简写，形参 state，返回 state.name
            mingzi: state => state.name,

            //箭头函数展开就是这样
            nianling: function(state){
                //函数体内可以对数据再做处理
                return state.age;
            },

            //简写版，等于上面的写法
            xingbie: 'gender'

        }),
        methods: {
            setAge: function(e){
                this.$store.commit('setAge', e.target.value);
            }
        },
    }
    ```
    > 缺点： 无法添加其他的计算属性

- `mapState` 优化

    使用 `v-model` 去双向绑定
    ``` html
    <div>
        {{mingzi}}<br>
        {{nianling}}<br>
        <input type="text" name="" id="" v-model="nianling"><br>
        {{xingbie}}
    </div>
    ```

    对我们需要修改的插值使用计算属性，其他的只需要获取值的数据使用 `mapState`
    ``` javascript
    const page1 = {
        template: '#page1',
        computed: {
            //使用计算属性去获取和修改 数据
            nianling: {
                get: function(){
                    return this.$store.state.age;
                },
                set: function(value){
                    this.$store.commit('setAge', value);
                }
            },

            //使用对象展开运算符
            ...Vuex.mapState({
                mingzi: state => state.name,
                xingbie: 'gender',
            }),

            //如果对这些其他数据没有重命名的需求，也可以使用数组形式
            ...Vuex.mapState(['name','gender']),
        },
    }
    ```

## getters 状态派生 mapGetters

当组件数量很多时，使用 `mapState` 和 计算属性 仍旧很繁琐，`vuex` 提供了 `getters` 来提前处理好 `state`再被调用

- 基本使用<br>
    `getters` 可以简单理解为是 `vuex` 的计算属性

    ``` javascript
    const store = new Vuex.Store({
        state: {
            name: '张三',
            age: 23,
            gender: '男'
        },
        getters: {
            getGender: function(state){
                return '性别：' + state.gender;
            }
        },
    })
    ```

    组件内可以通过计算属性去获取该值
    ``` javascript
    getGender() { 
        return this.$store.getters.getGender 
    },
    ```
    或者 `html` 中直接获取
    ```
    {{$store.getters.getGender}}
    ```

- 参数传递

    `getters`

    >使用双箭头是因为，我们再计算属性返回的是一个方法，也就是第一层箭头函数的函数体，此时的参数就是和第二层箭头函数对应的

    ``` javascript
    getters: {
        //双箭头函数
        getGender: state => id => {
            return '性别：' + state.gender + ',' + id;
        },

        //等同于
        getGender: state => {
            return id => {
                return '性别：' + state.gender + ',' + id;
            }
        }
    }
    ```

    使用
    ``` javascript
    //计算属性中使用
    computed: {
        gender: function(){
            return this.$store.getters.getGender(2);
        },
    }

    //插值处直接使用
    {{gender(2)}}

    computed: {
        gender: function(){
            return this.$store.getters.getGender;
        },
    }

    //数组简介时也可以在插值处直接使用
    {{getGender(2)}}

    computed: Vuex.mapGetters(['getGender'])
    ```

- `mapGetters` 辅助函数

    上面的基本写法需要写计算属性还是很麻烦，所以 `getters` 也有辅助函数<br>

    `cli` 项目同样需要导入辅助函数
    ``` javascript
    import { mapState,mapGetters } from 'vuex
    ```

    如果没有修改插值名称和再添加计算属性的需求，可以直接简写了，它和 `mapState` 的用法是基本一致的

    ``` javascript
    //数组
    computed: Vuex.mapGetters(['getGender'])

    //对象
    computed: Vuex.mapGetters({
        getGender: 'getGender'
    })

    //对象展开对象
    computed: {
        gender: function(){
            return this.$store.getters.getGender;
        },

        ...Vuex.mapGetters({
            name: 'getName',
            age: 'getAge'
        })
    }

    //对象展开数组
    computed: {
        gender: function(){
            return this.$store.getters.getGender;
        },
        ...Vuex.mapGetters(['getName', 'getAge'])
    }
    ```

## mutations 状态提交 mapMutations

我们需要对 `state` 的值进行修改时，会用到 `mutations`去操作<br>
但是 `mutations` 必须是同步函数

- 基本使用

    >`mutations` 可以理解为是 `vuex` 的`methods`

    ``` javascript
    methods: {
        setAge: function(e){
            this.$store.commit('setAge', e.target.value);
        }
    }

    mutations: {
        setAge : function(state, value){
            state.age = value;
        }
    }
    ```

- 参数
    >提交载荷(`payload`)，在大多数情况下传递一个对象，可以包含多个字段
    ``` javascript
    //上面的例子可以修改为
    methods: {
        setAge: function(e){
            this.$store.commit('setAge', e);
        }
    }

    mutations: {
        setAge : function(state, payload){
            state.age = payload.target.value;
        }
    }
    ```

- `mapMutations`

    也有辅组函数，用法和其他的辅组函数还是一样
    ``` html
    <input type="text" name="" id="" @input="setAge" :value="age"><br>
    ```
    ``` javascript
    //上面的例子还可以修改成这样
    methods : { 
        ...Vuex.mapMutations(['setAge']) //支持荷载，会自动传参 
    }

    mutations: {
        setAge : function(state, payload){
            state.age = payload.target.value;
        }
    }
    ```

## actions 异步提交 mapActions
<br>

- 基本使用<br>
    在 `mutations`中使用异步函数，结果是，页面内容有所变化，但是`vuex.store` 却没有监听到数据的更新

    为解决这个问题，提供了 `actions` ，在 `actions` 中使用进行异步操作，异步操作的回调函数内再去调用 `mutations` 中的方法

    ``` html
    <input type="text" name="" id="" :value="age" @keyup.enter="setAge"><br>
    ```

    `action` 中也是支持 `payload` 的
    ``` javascript
    methods: {
        setAge: function(event){
            //使用 diapatch 去调用action
            // 第二个参数 payload也可以使用
            this.$store.dispatch('setAge', event);
        },
    },
    ```

    `actions` 中的方法 默认携带一个参数 `context` 可以理解为是 `Vuex.$store`
    ``` javascript
    mutations: {
        setAge : function(state, payload){
            state.age = payload.target.value;
        }
    },
    actions: {
        setAge : function(context, payload){
            setTimeout(()=>{
                //在异步操作的回调内调用 mutations
                context.commit('setAge', payload);
            }, 1000)
        }
    }}
    ```

- `mapActions`

    `mapActions` 中会自动获取 `event` 赋值给 `payload` 并传递
    ``` javascript
    //也可以这样写
    methods: {
        ...Vuex.mapActions(['setAge']),
    },
    ```

- `context` 解构

    ``` javascript
    actions: {
        setAge : function({ commit }, payload){
            setTimeout(()=>{
                commit('setAge', payload);
            }, 1000)
        }
    }
    ```

## modules 模块化

- 基本使用

    重新定义一个对象，其结构和`store`完全一致，在`store`中的`module`中引用

    ``` javascript
    const list = {
        state: {},
        getters: {},
        mutations: {},
        actions: {},
    }

    const store = new Vuex.Store({
        modules: {
            list
        }
    })
    ```

    存在一个问题，如果模块状态中和主状态中名称一致，该如何去访问模块状态中的内容

- 命名空间

    ``` javascript
    const list = {
        namespace: true,
        state: {},
        getters: {},
        mutations: {},
        actions: {},
    }
    ```

    `state` 使用<br>

    ``` javascript
    computed: {
        name: function(){ 
            return this.$store.state.list.name
        },
        age: function(){
            return this.$store.state.list.age
        },
        gender: function(){
            return this.$store.state.list.gender
        },
    }
    ```

    `getters` 使用<br>
    ``` javascript
    computed: {
        getAge: function(){
            return this.$store.getters['list/getAge'];
        }

        //或者这样
        ...Vuex.mapGetters({
            getAge: 'list/getAge'
        }),

    },
    ```

    `mutations` 使用<br>
    ``` javascript
    methods: {
        setAge: function(event){
            this.$store.commit('list/setAge', event);
        },

        //或者这样
        ...Vuex.mapMutations({
            setAge: 'list/setAge'
        })
    }
    ```

    `actions` 使用 <br>
    ``` javascript
    setAge: function(event){
        this.$store.dispatch('list/setAge', event);
    },

    //或者这样
    ...Vuex.mapActions({
        setAge: 'list/setAge'
    }))
    ```


# axios

## 安装

- `npm`安装
    ```
    npm install axios
    ```

- 在线版

    ``` html
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    ```

## 基本功能

- 请求方法别名

    `get` `post` `put` `delete`
    使用方式均如下<br>
    `config` 指配置，常可以用来配置头信息，设置`timeout`之类的
    ``` javascript
    axios.post(url,params,config)
    .then(res => {
        console.log(res)
    })
    .catch(err => {
        console.error(err); 
    })
    ```

    需要注意`params`是一个对象，展开格式如下
    ``` javascript
    //npm安装的需要引入
    const axios = require('axios'); 
    //定义配置
    let url = 'http://noncross.domain.com/data/user.php';
    const params = {
        params: {
            id : 3,
            status : 1
        }
    }
    //使用 get 请求获取数据 
    axios.get(url, params)
    .then(res => {
        //返回结果
        console.log(res);
        this.userlist = res.data;
    })
    .catch(err => {
        //执行错误
        console.error(err);
    });
    ```

    最终 `header` 中请求地址如下
    ``` javascript
    Request URL: http://noncross.domain.com/data/user.php?id=3
    ```

    如果是一个`post`请求，同时`url`要携带参数
    ``` javascript
    const data = {
        params: {
            id : 3,
            status : 1
        },
        data: {
            type: 1
        }
    }
    ```

- 配置

    基本配置信息 <br>
    `method` 
    `url` <br>
    `params` : get 请求的参数<br>
    `data` : post put patch 发送的数据<br>
    [常用配置信息](http://www.axios-js.com/zh-cn/docs/#%E8%AF%B7%E6%B1%82%E9%85%8D%E7%BD%AE)<br> `headers`: 头部信息<br>
    `auth`: basic认证信息<br>
    `timeout`: 访问超时时间<br> 
    `responseType`: 返回数据类型 等
    
    ``` javascript
    axios({
        method:'get',
        url: 'http://noncross.domain.com/data/user.php',
        params: {
            id:3
        }
    }).then(res => {
        console.log(res.data);
    }).catch(err => {
        console.log(err);
    })
    ```

- 并发操作

    我们写多个 `ajax` 异步请求时，回调函数会根据他们各自的请求时间的顺序来执行<br>
    如果想要让他们所有异步请求完成后，再按照指定顺序执行回调 可以使用 `all()`
    ``` javascript
    axios.all([callback1,callback2])
    .then(axios.spread((res1, res2)=>{
    }))
    ```

    ``` javascript
    let url = 'http://noncross.domain.com/data/user.php'; 
    const callback1 = axios({
        method: 'get',
        url,
        data: {
            id:1
        }
    })
    const callback2 = axios({
        method:'get',
        url,
        data: {
            id:2
        }
    })
    axios.all([callback1,callback2])
    .then(axios.spread((res1, res2)=>{
        console.log(res1);
        console.log(res2);
    }))
    ```

## 实例化

- 全局配置<br>
    配置中的重复部分，可以拿出来做一个公共的配置

    ``` javascript
    //全局默认值设置，baseUrl 会自动在 url 前面添加 
    axios.defaults.baseURL = 'http://noncross.domain.com/data/';
    ```

- 实例化<br>
    实例化一个`axios`对象出来，具有独立性
    ``` javascript
    //可以将公共部分作为配置 再实例化
    const myAxios = axios.create({
        baseURL: 'http://noncross.domain.com/data/',
        timeout: 1000,
        headers: {'X-Custom-Header': 'foobar'},
    })

    //request相当于前面的直接使用axios的写法
    //也可以使用方法别名
    myAxios.request({
        methods: 'get',
        url: 'user.php',
    }).then(res => console.log(res.data))
    ```

## 拦截器

- 请求拦截<br>
    可以在请求发出之前拦截，此时可以对配置进行修改，loading，或是进行数据验证等
    ``` javascript
    myAxios.interceptors.request.use(
        config => { 
            console.log('loading...');
            return config; 
        }
    );
    ```

- 响应拦截<br>
    在输出数据前进行拦截，可以对数据进行过滤或是修改再返回
    ``` javascript
    myAxios.interceptors.response.use(
        response => { 
            //在输出数据前，对数据进行拦截处理 
            return response.data; 
        }
    );
    ```

- `mock`拦截<br>
    `api`上线前可以用 `mock` 拦截掉，添加一些模拟数据进行测试
    ``` javascript
    axios.request({
        methods: 'get',
        url: 'http://noncross.domain.com/data/user.php',
    }).then(res => console.log(res.data))

    //拦截并添加数据
    Mock.mock('http://noncross.domain.com/data/user.php', { 
        'list|5-10': [{ 
            'id|+1': 1, 
            'username' : '@cname', 
            'gender' : '@boolean' 
        }] 
    })
    ```


## 跨域


## vue 封装
