# Vue2.x的使用回顾

最近vue3.0已经被发布，思来想去，不如来一波2.x的复习，然后直接转去3.0，难道不香吗？

## Vue是什么？

Vue的官网称Vue是渐进式Javascript框架。这里面有两点：1.渐进式：Vue被设计成自底向上逐层应用，把整个的Vue拆分成好几块，比如声明式渲染，组件系统，路由，状态管理，构建系统等。当你做项目的时候，根据你的需要可以添加相应的功能模块，各功能模块可以组合在一起。2.Javascript框架：其实你用编写的Vue代码最终经过打包会生成javascript代码。

## Vue怎么使用？

### 安装

直接使用<script>便签引入

```js
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

### 使用

创建一个hello world

```html
<body>
    <div id="app">{{msg}}</div>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
      app = new Vue({
        el: '#app',
        data: {
          msg: 'hello world'
        }
      });
    </script>
</body>
```

通过script中引入Vue之后，会在全局暴露一个Vue对象构造器，我们可以通过配置构造器里的参数进行初始化。Vue是典型的MVVM框架，所以我们需要VIew层就是标签内容，Model层就是我们Vue实例化中的参数配置，而ViewModel就是Vue内部做的事情。当我们将View视图层和Model数据层通过配置el属性进行绑定之后，我们后续的操作只需要聚焦到数据逻辑上来就行了，至于页面的刷新或者改变等操作交给Vue内部进行操作就行了，我们暂时不用关心。

### 语法

1. 插值表达式：{{}}

   当我们需要使用Vue实例中的data中的数据的时候，我们可以通过差值表达式进行绑定，Vue中的数据是响应式的，所以当我们data中的数据发生变化的时候，View中的使用data的地方也会相应的变化。

   ```html
   <div id="app">{{msg}}</div>
   <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
   <script>
       app = new Vue({
           el: '#app',
           data: {
               msg: 'hello world'
           }
       });
   </script>
   ```

   当我们在控制台中输入app.$data.msg = 'happy world'，相应的页面中显示也会发生变化。由此我们可以console.log(app)，我们会看到打出很多$开头的属性，这些属性是暴露给我们的，我们可以直接使用，至于以_开头的额属性，这是Vue内部调用的属性，比如Typescript会有private，public等，而javascript中没有这种修饰符，所以通过 _来标识内部的属性，不建议使用，因为它会经常改变。

