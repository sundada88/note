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

     

