# Vue3.0使用

## 快速开始

### 安装

1. 使用 `cdn`

   ```html
   <script src="http://cdn.bootcdn.net/ajax/libs/vue/3.0.0-rc.7/vue.global.js"></script>
   ```

2. 升级`vue-cli`

   ```js
   npm i -g @vue/cli@next
   ```

   通过 `vue create project` 的时候选择创建`Vue3.0`的项目。

3. 使用 `vite`

   ```bash
   $ npm init vite-app <project-name>
   $ cd <project-name>
   $ npm install
   $ npm run dev
   ```

### 新特性

1. 实例的获取变成一个工厂函数`createApp`而不是通过`new Vue`的一个实例，然后挂载在容器`#app`上面。

   `Vue2.x`

   ```js
   new Vue(App).$mount('#app')
   ```

   `Vue3.x`

   ```js
   createApp(App).mount('#app')
   ```

2. `composition Api` : 为`Vue`应用提供更好的逻辑复用和代码组织，比如某一块的代码是相关于这一数据的，那么我们可以通过`composition Api`将这一部分代码逻辑提取出来，避免在代码中来回横跳的问题。

   - 此时我们将响应式数据的声明和操作放在`setup` 中

     ```vue
     <template>
     {{data.counter}}
     </template>
     <script>
     import {reactive} from 'vue';
     export default {
     	 setup () {
              const data = reactive({
                  counter:1
              })
              return {data}
          }
     }
     </script>
     ```

   - 声明计算属性

     ```vue
     <template>
     {{data.counter}}
     {{data.doubleCounter}}
     </template>
     <script>
     import {reactive, computed} from 'vue';
     export default {
     	 setup () {
              const data = reactive({
                  counter:1,
                  doubleCounter:computed(() => {
                      return data.counter * 2
                  })
              })
              return {data}
          }
     }
     </script>
     ```

   - 添加生命周期

     ```vue
     <template>
     {{data.counter}}
     {{data.doubleCounter}}
     </template>
     <script>
     import {reactive, computed, onMounted, onUnmounted} from 'vue';
     export default {
     	 setup () {
              const data = reactive({
                  counter:1,
                  doubleCounter:computed(() => {
                      return data.counter * 2
                  })
              })
              let timer
              onMounted(() => {
                  timer = setInterval(() => {
                      data.counter ++
                  }, 1000)
              })
              onUnmounted(() => {
                  clearInterval(timer)
              })
              return {data}
          }
     }
     </script>
     ```

   - 基本类型的数据声明为响应式

     ```vue
     <template>
     {{message}}
     </template>
     <script>
         import {ref} from 'vue'
         setup() {
             let message = ref('message')
             return {message}
         }
     </script>
     ```

     注意：在`template`中使用基本类型响应式数据的时候，不需要加上`value`，当在`setup`中对声明的值进行操作的时候，因为我们声明的是一个包装器，需要操作响应式值的`value`的属性。

     ```js
     let message = ref('message')
     onMounted(() => {
         message.value += 1
     })
     return {message}
     ```

   - 此时我们做了两个与响应式数据相关的数据，如果逻辑较多，我们可以将他们分别提取出来，这样代码就比较清爽了，别的地方要是有相同的逻辑也可以提取到另一个文件中，方便复用。

     ```vue
     <template>
     {{data.counter}}
     {{data.doubleCounter}}
     {{message}}
     </template>
     <script>
     import {reactive, computed, onMounted, onUnmounted} from 'vue';
     function getData () {
         const data = reactive({
                  counter:1,
                  doubleCounter:computed(() => {
                      return data.counter * 2
                  })
              })
              let timer
              onMounted(() => {
                  timer = setInterval(() => {
                      data.counter ++
                  }, 1000)
              })
              onUnmounted(() => {
                  clearInterval(timer)
              })
         return data
     }
     function getMessage () {
         const message = ref('message')
         onMounted(() => {
             message.value += 1
         })
         return message
     }
     export default {
     	 setup () {
              const data = getDate()
              const maessage = getMessage()
              return {data, message}
          }
     }
     </script>
     ```

   - 但是上述的`data`我们每次在`template`中使用的时候都要加上`data`,可不可以将`data`中的数据解构出来

     ```js
     setup() {
         const data = getData()
         return {...data}
     }
     ```

     这样解构出来的数据不是响应式的了。可以通过在外面包裹`toRefs`称为响应式数据。

     ```js
     import {toRefs} from 'vue'
     setup() {
         const data = getData()
         return {...toRefs(data)}
     }
     ```

   - 通过`ref`获取元素

     `vue 2.x`通过`this.$refs.name`获取

     `vue3.x`中获取

     ```vue
     <template>
     <p ref='desc'></p>
     </template>
     <script>
     import {setup, ref} from 'vue'
     setup() {
         const desc = ref(null) // 由于赋值是null，回去元素中查找ref为desc的元素
         return {desc}
     }
     </script>
     ```

   - 侦听器

     ```vue
     <template>
     {{data.counter}}
     {{data.doubleCounter}}
     <p ref="desc"></p>
     </template>
     <script>
     import {reactive, computed, onMounted, onUnmounted,  watch} from 'vue';
     function getData () {
         const data = reactive({
                  counter:1,
                  doubleCounter:computed(() => {
                      return data.counter * 2
                  })
              })
              let timer
              onMounted(() => {
                  timer = setInterval(() => {
                      data.counter ++
                  }, 1000)
              })
              onUnmounted(() => {
                  clearInterval(timer)
              })
         return data
     }
     export default {
     	 setup () {
              const data = getDate()
              const desc = ref(null)
              watch(() => data.counter, (newValue, oldValue) => {
                  desc.value.textContent = `counter ${newValue}`
              })
              return {data, desc}
          }
     }
     </script>
     ```

3. `Teleport` 传送门

   当我们在业务组件中弹出弹框，而这个弹出框需要被添加在特定的元素上面，比如我们添加在`body`上面，这时候就可以使用`Teleport`组件了，通过`to`来决定添加在哪个元素上面。

   ```vue
   <template>
   	<Teleport to="body">
   		<div v-if="show">
               <button @click="handleClick">
                   
       		</button>
       	</div>
       </Teleport>
   </template>
   <script>
   export default {
       data () {
           return {
               show: false
           }
       },
       methods: {
           handleClick() {
               this.show = !this.show
           }
       }
   }
   </script>
   ```

   这样当弹框出现的时候，就是挂载到`body`上面。

4. `Fragments`，vue3中组件可以拥有多个根。

5. `inject` 变化

   `vue2.x`

   ```js
   inject: {
       elForm: {
           default: ''
       }
   }
   ```

   `vue3.x`

   ```js
   import {inject} from 'vue'
   setup () {
       const elForm = inject('elForm', {}) // 默认值是{}
   }
   ```

6. `provide`变化

   `vue2.x`

   ```js
   provide () {
       return {
           form: this
       }
   }
   ```

   `vue3.x`

   ```js
   import {getCurrentInstance, provide} from 'vue'
   const instance = getCurrentInstance()
   provide('form', instance)
   ```

   

7. `unref`

   ```js
   return unref(test)
   /*
   	如果test是一个ref的话，则返回test.value
   	如果test不是一个ref的话，则直接返回test的值
   ```

   

8. `Emits`选项

   如果一个组件向外触发一个`click` 事件，比如在`emit.vue`组件中。

   ```vue
   <template>
   	<div @click="$emit('click')">
       	这是一个按钮
       </div>
   </template>
   ```

   在父组件中使用`emit`组件：

   ```vue
   <template>
   	<emit @click="handleClick"></emit>
   </template>
   <script>
   import emit from './emit.vue'
   export default {
       components: {emit},
       methods: {
           handleClick () {
               window.console.log(123)
           }
       }
   }
   </script>
   ```

   此时点击`emit`组件会触发两次`handleClick`函数。一个是`emit`自定义触发的事件，一个是原生自带的`click`事件。通过添加`emits`选项，可以避免这个情况。

   ```vue
   <template>
   	<div @click="$emit('click')">
       	这是一个按钮
       </div>
   </template>
   < script>
   export default {
       emits: ['click'] //此时的click只被作为自定义事件去处理，原生的则不会处理。
   }
   </script>
   ```

   通过`emits`选项，可以更好的记录组件应该如何工作？将组件可以触发的事件一下便可以看清楚。

   如果对抛出的事件有要求，还可以验证抛出的事件，比如验证必须传入参数

   ```vue
   <template>
   	<div @click="$emit('click')">
       	这是一个按钮
       </div>
   </template>
   < script>
   export default {
       emits: {
           click: (...args) => {
               return args.length > 0
           }
       }
   }
   </script>
   ```

9. `Global API`改为应用程序实例调用

   `vue2.x`中有很多全局的`api`可以改变`vue`的行为，比如`Vue.component`，`Vue.use`，`Vue.directive`，`Vue.mixin`等，这导致一些问题：

   - `vue2`没有`app`的概念，`new Vue()`得到的根实例被作为`app`，这样的话所有创建的根实例是共享相同的全局配置，这在测试时会污染其他测试用例，导致测试变得困难
   - 全局配置也导致没有办法在单页面创建不同全局配置的多个`app`实例

   `vue3`中使用`createApp`返回`app`实例，由他暴露一系列 全局`api`

   ``` js
   import {createApp, h} from 'vue'
   const app = createApp({})
   	.component('comp', {
           render: () => h('div', 'i am comp')
       })
   	.mount('#app')
   ```

10. `Global and internal APIs`重构为可做摇树优化

   `vue2`中不少`global-api`是作为静态函数直接挂在构造函数上的，例如`Vue.nextTick()`,如果我们从未在代码中用过他们，就会形成所谓的`dead code`，这类`global-api`造成的`dead code`无法使用`webpack`的`tree-shaking`排除掉。

   ```js
   import Vue fom 'vue'
   Vue.nextTick(() => {
       // something something DOM-related
   })
   ```

   `vue3` 中做了相应的变化，将他们抽取成单个函数，这样打包工具的摇树优化可以将这些`dead code`排除掉。

   ```js
   import {nextTick} from 'vue'
   nextTick(() => {
       // something something DOM-related
   })
   ```

   受影响的`api`:

   1. `Vue.nextTick`
   2. `Vue.observable`：被`Vue.reactive`替换掉了
   3. `Vue.version`
   4. `Vue.compile`
   5. `Vue.set`
   6. `Vue.delete`

11. `v-model` 使用的变化

   `v-model`选项和`v-bind`的`sync`修饰符被移除，统一改为`v-model`形式

   ```html
   <div id="app">
       <h3>
           {{data}}
       </h3>
       <comp v-model="data"></comp>
   </div>
   ```

   ```js
   app.component('comp', {
       template: `
   		<div @click="$emit('update:modelValue', 'new Value')">
   			i am comp
   		</div>
   	`,
       props: ['modelValue']
   })
   ```

   如果上述我不想在子组件中使用`modelValue`，想要使用其他的名称`counter`

   ```js
   app.component('comp', {
       template: `
   		<div @click="$emit('update:counter', 'new Value')">
   			i am comp
   		</div>
   	`,
       props: ['counter']
   })
   ```

   ```html
   <div id="app">
       <h3>
           {{data}}
       </h3>
       <comp v-model:counter="data"></comp>
   </div>
   ```

11. 函数式组件使用的变化

    函数式组件仅能通过简单函数方式创建，`funtional`选项废弃。

    函数式组件变化较大，主要有以下几点：

    - 性能提升在`vue3`中可以忽略不计，所以`vue3`中推荐使用状态组件
    - 函数式组件仅能通过纯函数形式声明，接收`porps`和`context`两个参数
    - `SFC` 中 `temlate`不能添加`functional`特性声明函数式组件
    - `{functional:true}`组件选项移除

    声明一个函数式组件，`Functional.vue`

    ```vue
    <script>
    import {h} from 'vue'
    const Heading = (props, context) => {
        return h(`h${props.level}`, context.attrs. context.slots)
    }
    Heading.props = ['level']
    export default Heading
    </script>
    ```

12. 异步组件

    `vue2.x`通过返回`import`引入的值来达到异步组件的目的。

    ```vue
    <template>
    <AsyncComp></AsyncComp>
    </template>
    <script>
    	components: {
            AsyncComp: () => import('./AsyncComp.vue')
        }
    </script>
    ```

    由于`vue3.0`中函数式组件必须定义为纯函数，异步组件定义时有如下变化：

    - 必须明确使用`defineAsyncComponent`包裹
- `component`选项重命名为`loader`
    - `loader`函数不在接收`resolve` and `reject`且必须返回一个`Promise`	
    
    定义一个异步组件

    ```js
import {defineAsyncComponent} from 'vue'
    // 不带配置的异步组件
    const asyncPage = defineAsyncComponent(() => import('./index.vue'))
    ```
    
    带配置的异步组件，`loader`选项是以前的`component`

    ```js
import ErrorComponent from './ErrorComponent.vue'
    import LoadingComponent from './LoadingComponent.vue'
    // 待配置的异步组件
    const asyncPageWithOptions = defineAsyncComponent({
        loader: () => import('./nextPage.vue'),
        delay:200,
        timeout:3000,
        errorComponent: ErrorComponent,
        loadingComponent: LoadingComponent
    })
    ```
    
11. 组件`data` 选项应该总是声明为函数

    `vue2.x`的时候只有根组件的`data` 我们写作对象，`vue3.x`中的多有`data`选项都要写作函数。

12. 自定义组件白名单，比如我自己通过自定义渲染器渲染的某些元素，为了避免报错信息，可以添加到白名单中。

    `vue3.x`中自定义元素检测发生在模板编译时，如果要添加一些`vue`之外的自定义元素，需要在编译器选项中设置`isCustomElement` 选项。

    - 如果是`vue-cli`创建的项目

      ```js
      // vue.config.js
      rules:[
          {
              test: /\.vue$/,
              use: 'vue-loader',
              options: {
                  compilerOptions: {
                      isCustomElement: tag => tag === 'plastic-button' // 当标签是plastic-button的时候放过编译
                  }
              }
          }
      ]
      ```

    - 如果是`vite`

      ```js
      // vite.config.js
      module.exports = {
          vueCompilerOptions: {
              isCustomElement: tag => tag === 'plastic-button'
          }
      }
      ```

    - 如果是运行时编译版本的`vue`，可通过全局配置`isCustomElement`

      ```js
      const app = createApp({})
      app.config.isCustomElement = tag => tag === 'plastic-button'
      ```

13. `is` 属性仅限用在`component`标签上

    `vue3`中设置动态组件，`is` 属性仅能用于`component`标签上

    ```html
    <component is="comp"></component>
    ```

    `dom`内模板解析使用`v-is`替代

    ```html
    <table>
        <tr v-is="'blog-post-row'"></tr>
    </table>
    ```

    仅限`in-dom`模板，因此我们测试放到独立页面测试，`index.html`

    ```html
    <div id="app">
        <table>
            <tr v-is="'row'" v-for="item in items" :data="item"></tr>
        </table>
    </div>
    <script src="./vue.js"></script>
    <script>
    Vue.createApp({
        data () {
            return {
                items: ['aaa', 'bbb']
            }
        }
    })
        .component('row', {
        props: ['data'],
        template: `<tr><td>{{this.data}}</td></tr>`
    })
        .mount('#app')
    </script>
    ```

14.  `$scopedSlots`属性被移除，都用`$slots`替代

    修改`$slots.xx` 为 `$slots.xx()`

15. 自定义指令`API`和组件保持一致

    `vue3`中指令`api`和组件保持一致，具体表现在：

    - `bind` -> `beforeMount`
    - `inserted` -> `mounted`
    - `beforeMount`: new ! 元素自身更新前调用，和组件生命周期钩子很像
    - `update` -> removed! 和updated基本相同，因此移除，使用updated替代
    - `componentUpdated` -> `updated`
    - `beforeUnmount` new ! 和组件声明周期钩子相似，元素将要被移除之前调用
    - `unbind` -> `unmounted`

    比如：

    ```js
    // main.js
    createApp({})
    .directive('lighlight', {
        boforeMount (el, binding, vnode) {
            el.style.background = binding.value
        }
    })
    ```

16. `transition`类名变化

    - `v-enter` => `v-enter-from`
    - `v-leave` => `v-leave-from`

17. 组件`watch`选项和实例方法`$watch`不在支持点分割符字符串路径，如果需要监听某个对象的某个属性，则需要第一个参数为一个`get`函数

    ```js
    const state = reactive({
        counter:1
    })
    watch(() => state.counter, (newValue, oldValue) => {
        console.log(newValue)
    })
    ```

18. `vue2.x`中应用程序根容器的`outerHTML`会被根组件的模板替换（或者编译为template）,`vue3.x`现在使用根容器的`innerHTML`取代

19. `app.config.globalProperties.$ELEMENT`挂载在全局上的属性怎么在组件中获取

    `vue2.x`

    ```js
    const install = (Vue, opts = {}) {
        Vue.prototype.$ELEMENT = {
        	size:399,
        	zIndex: 
    	}
    }
    ```

    ```js
    // 在vue组件中使用
    this.$ELEMENT
    ```

    `vue3.x`

    ```js
    const install = (app, opts = {}) {
        app.config.globalProperties.$ELEMENT = {
            size: 399,
            zIndex: opts.zIndex || 2000
        }
    }
    ```

    ```js
    import {getCurrentInstance} from 'vue'
    setup () {
        const ELEMENT = getCurrentInstance().ctx.$ELEMENT // 这样就获取了
    }
    ```

    

20. `vue3.0`中移除的东西

    1. `keyCode` 不在支持

       ```html
       <!-- keyCode方式不在支持 -->
       <input @keyup.13="submit" />
       <!-- 只能使用alias方式 -->
       <input @keyup.enter='submit' />
       ```

    2. `$on`, `$off`, `$once`被移除

       取而代之的是通过第三方库实现
       
       ```bash
       npm install mitt
       ```
       
       ```js
       // 事件派发和监听
       import mitt from 'mitt'
       const emitter = mitt() // 注意此时的派发事件和监听事件是同一个emitter
       // 派发事件
       emitter.emit('some-event', 'arguments')
       // 监听事件
       emitter.on('some-event', (data) => {
           console.log(data)
       })
       ```

21. `vue-router4`的变化

    引入方式：

    1. cdn

    2. `npm`

        ```bash
       npm install vue-router@next
       ```

    使用：

    1. 创建路由对象

       `vue2.x`是通过`new Router`出来的一个实例创建路由对象，现在是通过一个工厂函数

       ```js
       import {createRoute, createWebHashHistory} from 'vue-router'
       const router = creteRoute({
           history: createWebHashHistory(),// 之前的mode选项现在改为history
           rotues: [
           	{
                   path: '/',
                   component: index
               }
           ]
       })
       ```

    2. 以插件的形式使用

       上面我们也说过，`vue`中使用插件的`use`方法属于`app`实例的属性

       ```js
       import {createRoute, createWebHashHistory} from 'vue-router'
       const router = createRoute({
           history: createWebHashHistory(),
           rotues: [
           	{
                   path: '/',
                   component: index
               }
           ]
       })
       createApp({}).use(router)
       ```
       
    3. 动态的添加

       ```js
       const router = createRoute({
           history: createHashHistory(),
           routes: [
               {
                   path: '/',
                   component: index
               }
           ]
       })
       router.addRoute({
           path: '/about',
           name: 'about',
           component: () => import('./about.vue')
       })
       createApp(App).use(router)
       ```

       如果在`about`中再加入子路由：

       ```js
       router.addRoute('about', {
           path: '/about/info',
           component: () => import('./info.vue')
       })
       ```

    4. 和`composition API`结合使用，在`setup`中访问到路由实例

       ```vue
       <template>
       	<button @click="handleClick">
               返回上一级
           </button>
       </template>
       <script>
       import {useRouter} from 'vue-router'
       setup () {
           // 获取路由实例
           const router = useRouter()
           return {
               handleClick() {
                   router.go(-1)
               }
           }
       }
       </script>
       ```

       `route`是响应式对象，可监控变化

       ```vue
       <script>
       import useRoute from 'vue-router'
       setup () {
           const route = useRoute()
           watchEffect(() => route.query, query => {
               console.log(query)
           })
       }
       </script>
       ```

       组件内路由守卫

       ```vue
       <script>
       import {onBeforeRouteLeave, onBeforeRouteUpdate} from 'vue-router'
       setup () {
           onBeforeRouteLeave((to, from) => {
       		// 路由离开的时候做的操作
           }),
           onBeforeRouterUpdate((to, from) => {
               // 路由更新的时候做的操作
           })
    }
       </script>
       ```
       
    5. `router-link`组件自定义化

       ```vue
       <!-- NavLink.vue -->
       <template>
       	<div :class="{active:isActive}" @click="navigate">
               {{route.name}}
           </div>
       </template>
       <script>
       import {RouterLink, useLink} from 'vue-router'
       export default { 
           props: {
               // RouterLink相当于router-link组件，比如在router-link中使用的props属性现在都可以在NavLink组件中使用
               ...RouterLink.props,
               inactiveClass: String
           },
           setup (props) {
               // 获取RouterLink内部的属性和方法
               const {route, href, isActive, isExactActive, navigate} = useLink(props)
               return {route, isActive, navigate}
           }
       }
       </script>
       <style>
           .active {
               background-color: red;
           }
       </style>
       ```

    6. 一些破坏性的变化

       - 实例创建方式：`new Router()` => `createRouter`

       - 路由模式：`mode` => `history`

         - `history` => `createWebHistory`
         - `hash` => `createWebHashHistory`
         - `abstract` => `createMemoryHistory`

       - `baseUrl` => `createWebHistory({base: 'xxx'})`

         `vue-router2.x`

         ```js
         new Router({
             mode: 'history',
             baseUrl: 'xxx'
         })
         ```

         `vue-router@next`

         ```js
         createRouter({
             history: createWebHistory({base: 'xxx'})
         })
         ```

       - 通配符改变

         - `vue-router2.x`

           ```js
           routes: [
               {
                   path: '/',
                   name: 'index',
                   component: () => import('./index.vue')
               },
               {
                   path: '*',
                   name: '404',
                   component: () => import('./notFound.vue')
               }
           ]
           ```

         - `vue-router@next` 必须通过`parmas`传入

           ```js
           {
               path: '/:pathMatch(.*)*',
               name: 'notFound',
               component: () => import('notFound.vue')
           }
           // 使用命名导航至404页面，resolve最终生成一个location对象，可以查看location等
           router.resolve({
               name: 'notFound',
               params: {
                   pathMatch: ['not', 'found'] // /not/found
               }
           })
           ```

       - `onReady()` => `isReady()`

         当我们往`router`中`push`的时候，由于`router`中的方法都是异步的所以需要等`onReady`之后，在执行

         - `vue-router`

           ```js
           router.push()
           router.onReady(onSuccess, onError)
           ```

         - `vue-router`

           ```js
           router.isReady().then(onSuccess).catch(onError)
           ```

       - `scrollBehavior`变化

         - `vue-router`

           ```js
           new Router({
               scrollBehavior(to, from, savedPosition) {
                   return {
                       x: 0,
                       y: 0
                   }
               }
           })
           /*
           	to和from是路由对象
           	要是想滚动位置{x: 0, y: 0}
           */
           ```

         - `vue-router`

           ```js
           createRouter({
               scrollBehavior(to, from, savedPosition) {
                   if (savedPosition) {
                       return savedPosition
                   } else {
                       return {
                       	top: 0,
                       	left: 0
                   	}
                   }
               }
           })
           ```

       - `keep-alive`和`transtion`标签

         - `vue-router`

           ```html
           <router-alive>
           	<router-view></router-view>
           </router-alive>
           ```

         - `vue-router-next`

           ```html
           <router-view v-slot="{Component}">
               <keep-alive>
               	<component :is="Component" />
               </keep-alive>
           </router-view>
           ```

       - `router-link`

         移除了一票属性

         - `append`

           ```html
           <router-link to="child-route" append /> // 使用append加上父路由，/parent/child-route
           ```

         - `tag/event`

           - `vue-router`

             ```html
             <router-link to="/xx" tag="span" event="click"></router-link>
             ```

             将`router-link`标签渲染为`span`标签，通过`click`事件跳转到响应的路由

           - `vue-router@next`

             ```html
             <router-link to="/xx" custom v-slot="{navigate}" >
             	<span @click='navigate'></span>
             </router-link>
             ```

             `custom`告诉自定义，`router-link`就不会生成默认的`a`标签，通过作用域插槽获取内部的方法，然后默认插槽中的内容会被渲染成`router-link`中的内容。

         - `exact`: 现在完全匹配逻辑简化

         - `mixins`中的路由守卫将被忽略
         
         - `match`被移除，统一合并到`resolve`中
         
         - `router.getMatchComponents()`被移除
         
           ```js
           router.currentRoute.value.matched // 获取相关的组件进行操作
           ```
         
         - 包括首屏导航在内的所有导航均为异步
         
           ```js
           app.use(router)
           router.isReady().then(() => app.mount('#app'))
           ```
         
           > 如果首屏存在路由守卫，则可以不等待就绪直接挂载，产生的结果和`vue2`相同
         
         - `route`的`parent`属性被移除
         
           ```js
           const parent = this.$route.matched[this.$route.matched.length - 2]
           ```
         
         - `routes` 选项是必填项
         
           ```js
           createRouter({
               routes: []
           })
           ```
         
         - 跳转不存在命名路由报错
         
           ```js
           router.push({name: 'indexx'})
           ```
         
         - 缺少必填参会抛出异常
         
         - 命名子路由如果`path`为空的时候不在追加`/`
         
           ```js
           [
               {
                   path: '/index',
                   children: [
                       {
                           path: '',
                       	component: index
                       }
                   ]
               }
           ]
           ```
         
           以前生成的`url`：`/index/`
         
           现在生成的`url`: `/index`
         
           副作用：给设置了重定向`redirect`选项的子路由带来副作用
         
           ```js
           [
               {
                   path: '/index',
                   children: [
                       {
                           path: '',
                       	redirect: 'home'
                       },
                       {
           				path: 'home',
                           component: homeComp
                       }
                   ]
               }
           ]
           ```
         
           之前：`/index/home`
         
           现在：`/home`
         
           解决办法：
         
           ```js
           [
               {
                   path: '/index',
                   children: [
                       {
                           path: '',
                       	redirect: '/index/home'
                       },
                       {
           				path: 'home',
                           component: homeComp
                       }
                   ]
               }
           ]
           ```
         
         - `$route` 属性编码行为
         
         




