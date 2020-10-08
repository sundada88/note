# Vue2.x的使用回顾

最近vue3.0已经被发布，思来想去，不如来一波2.x的复习，然后直接转去3.0，难道不香吗？

## Vue是什么？

`Vue`的官网称`Vue`是渐进式`Javascript`框架。这里面有两点：1.渐进式：`Vue`被设计成自底向上逐层应用，把整个的Vue拆分成好几块，比如声明式渲染，组件系统，路由，状态管理，构建系统等。当你做项目的时候，根据你的需要可以添加相应的功能模块，各功能模块可以组合在一起。2.`Javascript`框架：其实你用编写的`Vue`代码最终经过打包会生成`javascript`代码。

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

通过`script`中引入`Vue`之后，会在全局暴露一个`Vue`对象构造器，我们可以通过配置构造器里的参数进行初始化。`Vue`是典型的`MVVM`框架，所以我们需要`VIew`层就是标签内容，`Model`层就是我们`Vue`实例化中的参数配置，而`ViewModel`就是`Vue`内部做的事情。当我们将`View`视图层和`Model`数据层通过配置`el`属性进行绑定之后，我们后续的操作只需要聚焦到数据逻辑上来就行了，至于页面的刷新或者改变等操作交给`Vue`内部进行操作就行了，我们暂时不用关心。

### 语法

1. 插值表达式：{{}}

   当我们需要使用`Vue`实例中的`data`中的数据的时候，我们可以通过差值表达式进行绑定，Vue中的数据是响应式的，所以当我们`data`中的数据发生变化的时候，`View`中的使用`data`的地方也会相应的变化。

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

   当我们在控制台中输入`app.$data.msg = 'happy world'`，相应的页面中显示也会发生变化。由此我们可以`console.log(app)`，我们会看到打出很多`$`开头的属性，这些属性是暴露给我们的，我们可以直接使用，至于以`_`开头的额属性，这是`Vue`内部调用的属性，比如`Typescript`会有`private`，`public`等，而`javascript`中没有这种修饰符，所以通过 `_`来标识内部的属性，不建议使用，因为它会经常改变。
   
   我们发现当我们获取属性的时候，比如`$data`里面的属性，会比较麻烦，因此`Vue`也将`$data`等的属性直接代理到当前`Vue`的实例上面，我们也可以通过`app.msg`来获取`$data`上的属性`msg`。
   
2. 动态绑定参数：v-bind或：

   当我们给标签绑定属性的时候：

   ```html
   <div title="title">
   </div>
   ```

   这样就给`div`绑定了一个`title`属性，此时`title`的内容就是`title`，但是我们想改变`title`的值，如果使用原生的方法，就要获取`dom`的属性，然后改变。此时Vue提供了一个动态参数的指令：`v-bind`，我们可以书写如下：

   ```html
   <div id="app" v-bind:title="title">
       
   </div>
   <script>
   app = new Vue({
       el: "#app",
       data: {
           title: '这是title'
       }
   })
   </script>
   ```

   这样我们就将标签中的title属性和Vue中的data进行了绑定，如果我们需要改变标签中的title，直接通过更改Vue实例中的title属性就可以。v-bind还有一个简便的写法就是 `:`.

3. 事件处理：v-on

   当我们给`dom`添加点击事件的时候，原生的方法就是我们获取到这个`dom`，然后通过`addEventListener`添加事件。`Vue`为了方便我们的书写和开发效率，使用了`v-on`指令，如下：

   ```html
   <div id="app" v-on:click="handleClick">
       
   </div>
   <script>
   app = new Vue({
       el: "#app",
       methods: {
           handleClick (e) {
               console.log(e)
           }
       }
   })
   </script>
   ```

   此时就是给`div`绑定了一个点击事件，点击之后调用`handleClick`函数，注意一定要在Vue的配置项中添加`methods`属性。在`handleClick`函数中，我们还可以获取当前`dom`事件，通过第一个参数`e`获取。

   但是，如果我们需要往函数里面传参数的时候，此时函数的第一个参数就不是当前`dom`事件了。

   ```html
   <div id="app" v-on:click="handleClick(1)">
       
   </div>
   <script>
   app = new Vue({
       el: "#app",
       methods: {
           handleClick (e) {
               console.log(e)
           }
       }
   })
   </script>
   ```

   此时打印的`e`就是1，那么我们如何才能获取到当前`dom`事件呢？我们可以在`handleClick`中在传入一个`$event`的参数：

   ```html
   <div id="app" v-on:click="handleClick(1, $event)">
       
   </div>
   <script>
   app = new Vue({
       el: "#app",
       methods: {
           handleClick (arg, e) {
               console.log(e)
           }
       }
   })
   </script>
   ```

   此时的`e`就是当前`dom`事件。`v-on`还有一个简便的写法：`@`，比如`v-on:click` 也可以写成`@click`。

4. 计算属性：computed

   上述的插值表达式中可以写表达式，比如我们将`msg`的内容翻转表示：

   ```html
   <div id="app">
       {{msg.split('').reverse().join('')}}
   </div>
   <script>
   app = new Vue({
       el: "#app",
       data: {
           msg: 'hello world'
       }
   })
   </script>
   ```

   比如上述的方式，虽然可以实现我们需要的功能，但是在插值表达值中这样写太过冗余，还有一个就是不能让别人一眼看出你的意图，所以通过`computed`可以解决上面问题：

   ```html
   <div id="app">
       {{reverseMsg}}
   </div>
   <script>
   app = new Vue({
       el: "#app",
       data: {
           msg: 'hello world'
       },
       computed: {
           resverMsg () {
               return this.msg.split('').reverse().join('')
           }
       }
   })
   </script>
   ```

   以上的操作，我们也可以使用`methods`来实现，具体操作如下：

   ```html
   <div id="app">
       {{reverseMessage()}}
   </div>
   <script>
   app = new Vue({
       el: "#app",
       data: {
           msg: 'hello world'
       },
       methods: {
           reverseMessage () {
               return this.msg.split('').reverse().join('')
           }
       }
   })
   </script>
   ```

   那么`methods`和`computed`有什么区别呢？

   ```html
   <div id="app">
       {{reverseMessage()}} - {{reverseMsg}}
   </div>
   <script>
   app = new Vue({
       el: "#app",
       data: {
           msg: 'hello world'
       },
       methods: {
           reverseMessage () {
               console.log(1)
               return this.msg.split('').reverse().join('')
           }
       },
       computed: {
           reverseMsg () {
               console.log(2)
               return this.msg.split('').reverse().join('')
           }
       }
   })
   </script>
   ```

   当我们在控制台窗口输入`app.reverseMsg`不会打印出2，当我们在控制台输入`app.reverseMessage()`的时候会打印出1。也就是说，每次`methods`中的属性都会执行，而`computed`中的属性是有缓存的，当`computed`中所依赖的值发生改变的时候，此时的`computed`才会再次执行。

5. 侦听器：watch

   Vue还提供了一种更通用的方式来观察和响应Vue实例上的数据的变化。

   ```html
   <div id="app">
       {{msg}}
   </div>
   <script>
   app = new Vue({
       el: "#app",
       data: {
           msg: 'hello world'
       },
       watch: {
           msg (newVal, oldVal) {
   			console.log(newVal, oldVal)
           }
       }
   })
   </script>
   ```

   当我们在控制台中改变msg的时候，会触发侦听器函数的执行。

   有人可能发现了，这个`watch`是不是和`computed`可以实现一样的效果，答案是肯定的。

   ```html
   <div id="app">
       {{reverseMessage}} - {{reverseMsg}}
   </div>
   <script>
   app = new Vue({
       el: "#app",
       data: {
           msg: 'hello world',
           reverseMessage: ''
       },
       watch: {
           msg (newVal, oldVal) {
               this.reverseMessage = newVal.split('').reverse().join('')
           }
       },
       computed: {
           reverseMsg () {
               return this.msg.split('').reverse().join('')
           }
       }
   })
   </script>
   ```

   有人发现这样是不行的，刚开始进来`watch`中的函数并没有执行，这时我们可以通过更改选项来实现。

   ```html
   <div id="app">
       {{reverseMessage}} - {{reverseMsg}}
   </div>
   <script>
   app = new Vue({
       el: "#app",
       data: {
           msg: 'hello world',
           reverseMessage: ''
       },
       watch: {
           msg: {
               immediate: true,
               handler (newVal, oldVal) {
               	this.reverseMessage = newVal.split('').reverse().join('')
           	}
           }
       },
       computed: {
           reverseMsg () {
               return this.msg.split('').reverse().join('')
           }
       }
   })
   </script>
   ```

   `watch`和`computed`的区别：`watch`是监听一个，改变多个；而`computed`是监听多个，改变一个。

   当我们监听对象的时候：

   ```html
   <div id="app">{{info.a}}</div>
   <script>
       app = new Vue({
        el: '#app',
           data: {
               msg: 'hello world',
               reverseMessage: '',
               info: {
                   a: 1,
                   b: 2
               }
           },
           watch: {
               info(newVal, oldVal) {
                   console.log(123);
               }
           }
       });
   </script>   
   ```
   
   当我们在控制台中输入`app.info.a = 123`，此时视图中的数据变化了，但是没有触发`watch`中的函数执行，要想触发函数执行，需要：
   
   ```html
   <div id="app">{{info.a}}</div>
   <script>
       app = new Vue({
           el: '#app',
           data: {
               msg: 'hello world',
               reverseMessage: '',
               info: {
                   a: 1,
                   b: 2
               }
           },
           watch: {
               info: {
                   deep: true,
                   handler(newVal, oldVal) {
                       console.log(123);
                   }
               }
           }
       });
   </script>
   ```
   
   这样当`info.a`发生改变的时候，就可以触发这个函数了。但是此时，无论我是个改变`info.a`还是`info.b`都会触发这个函数，能不能我只改变`info.a`的时候才触发这个函数：
   
   ```html
   <div id="app">{{info.a}}-{{info.b}}</div>
   <script>
       app = new Vue({
           el: '#app',
           data: {
               msg: 'hello world',
               reverseMessage: '',
               info: {
                   a: 1,
                   b: 2
               }
           },
           watch: {
               'info.a'(newVal, oldVal) {
                       console.log(123);
                   }
           }
       });
   </script>
   ```
   
   这样就可以值监听`info.a`的变化了。
   
6. 条件渲染：v-if，v-else-if，v-show

   条件渲染其实和我们平时`javascript`中使用的`if`语句有相似之处，只不过条件渲染的指令我们放在标签中使用。

   ```html
   <div id="app" v-if="show">
       show
   </div>
   <script>
   app = new Vue({
       el: '#app',
       data:{
           show: true
       }
   })
   </script>
   ```

   当show为true的时候，dom才渲染。

   `v-show`和`v-if`的区别：`v-show`无论是否是`true`都会渲染，只不过是通过`true`来判断`dom`的`display`的属性是显示还是隐藏，`v-if`则只有在`true`的情况下，才会渲染。相比较，如果需要来回切换显示的话，使用`v-show`是比较理想的。

7. 列表渲染：v-for

   v-for`的作用就是可以遍历数组或者对象，如下:

   - 遍历对象

     ```html
     <div id="app">
              <ul>
                  <li v-for="(item, key) in lists">{{ item }} - {{key}}</li>
              </ul>
     </div>
     <script>
          app = new Vue({
              el: '#app',
              data: {
                  lists: {
                      111: {
                          name: 'xiaohong',
                          age: 18
                      },
                      222: {
                          name: 'xiaohei',
                          age: 19
                      }
                  }
              }
          });
      </script>
     ```
     
       如果`v-for`是两个参数的话，第一个参数就是属性值，第二个值就是属性。
     
      如果是三个参数的话，第三个参数就是`index`值。
     
      但是`for`出来的结果不会跟你在对象中的写的顺序是一致的，会因为`key`的值发生顺序的变化（`key`的值会根据前后顺序重新排列，然后遍历出来的结果会发生变化）。
     
   - 遍历数组
   
     ```html
     <div id="app">
           <ul>
             <li v-for="(item, index ) in list">{{ item }} - {{index}}</li>
           </ul>
         </div>
         <script>
           app = new Vue({
             el: '#app',
             data: {
               list: [
                 {
                   name: 'xiaohong',
                   age: 18
                 },
                 {
                   name: 'xiaohei',
                age: 17
                 }
            ]
             },
             watch: {
               'info.a'(newVal, oldVal) {
                 console.log(123);
               }
             }
           });
         </script>
     ```
   
     遍历数组最多有两个参数：第一个参数是每一项的`value`，第二个参数是`index`值。
   
      最后，可以通过在`v-for`的同一个标签内动态的绑定`key`的值，注意此时`key的唯一性，不要使用数组的index`作为`key`。加`key`的目的是，在`virtual dom`做`diff`算法的时候，会给性能提供部分优化。
   
   ```HTML
   <div id="app">
          <ul>
              <li v-for="(item, key ) in list" :key="item.name">
                  {{ item }} - {{key}}
              </li>
          </ul>
      </div>
      <script>
       app = new Vue({
              el: '#app',
           data: {
                  list: [
                   {
                          name: 'xiaohong',
                          age: 18
                      },
                      {
                          name: 'xiaohei',
                          age: 17
                      }
                  ]
              }
          });
      </script>  
   ```

8. class和style绑定

   - class绑定：

     `class`的绑定我们可以使用动态绑定参数的语法来实现：

     1. 绑定的class是一个对象

        ```html
        <style>
            .red {
                color:red;
            }
            .size {
                font-size: 20px;
            }
        </style>
        <div id="app" :class="classes">
            
        </div>
        <script>
        app = new Vue({
            el: '#app',
            data: {
                classes: {
                    red: true,
                    size:false
                }
            }
        })
        </script>
        ```

     2. 绑定的是一个数组

        ```html
        <style>
            .red {
                color:red;
            }
            .size {
                font-size: 20px;
            }
        </style>
        <div id="app" :class="classes">
            
        </div>
        <script>
        app = new Vue({
            el: '#app',
            data: {
                classes: ['red', 'size']
            }
        })
        </script>
        ```

     3. 绑定的数组中包含对象属性

        ```html
        <style>
            .red {
                color:red;
            }
            .size {
                font-size: 20px;
            }
        </style>
        <div id="app" :class="classes">
            
        </div>
        <script>
        app = new Vue({
            el: '#app',
            data: {
                classes: ['red', {
                    size: false
                }]
            }
        })
        </script>
        ```

   - style的绑定

     1. 绑定的是一个对象：

        ```html
        <div id="app" :style="style">
            
        </div>
        <script>
        app = new Vue({
            el: '#app',
            data: {
                style: {
                    color: 'red',
                    fontSize: '20px'
                }
            }
        })
        </script>
        ```

        注意：在对象中比如`font-size`这种羊肉串式的写法需要改成驼峰写法：`fontSize`。

     2. 绑定的是一个数组
     
        ```html
        <div id="app" :style="style">
            
        </div>
        <script>
        app = new Vue({
            el: '#app',
            data: {
                style: [{
                    color:'red'
                }, {
                    fontSize: '20px'
                }]
            }
        })
        </script>
        ```

9. input表单

   获取表单中输入的值：当我们输入的发生变化的时候，怎么获取到`input`中输入的值

   ```html
   <div id="app">
       <input type="text" @input="handleInput" />
   </div>
   <script>
   app = new Vue({
       el: '#app',
       methods: {
           handleInput(e) {
               console.log(e.target.value)
           }
       }
   })
   </script>
   ```

   当我们输入的数据发生变化的时候，会触发标签的`input`事件，通过获取`input`事件中的`e.target.value`可以获取到输入`input`框的值。

   如果我们要求刚开始的时候`input`中是有值显示的，则

   ```html
   <div id="app">
       <input type="text" :value="msg" @input="handleInput" />
   </div>
   <script>
   app = new Vue({
       el: '#app',
       data: {
           msg: '123'
       },
       methods: {
           handleInput(e) {
               console.log(e.target.value)
               this.msg = e.target.value
           }
       }
   })
   </script>
   ```

   即开始`input`框中显示的是`msg`的值，当`input`框中的值发生变化的时候，又将`input`中最新的值赋值给`msg`，所以这样就达到了双向数据绑定的效果，我们可以通过以下方式进行简写，和上述效果一样。

   ```html
   <div id="app">
       <input type="text" v-model="msg" />
   </div>
   <script>
   app = new Vue({
       el: '#app',
       msg: '123'
   })
   </script>
   ```

   上面我们抛出的事件是`input`，绑定的值是`value`的值.`v-model`还可以为内部不同的输入元素使用不同的`property`并抛出不同的事件。

   1. `checkbox`和`radio`使用`checked` `property`和`change`事件

      - 不用v-model的时候

        ```html
        <div id="app">
            {{isChecked}}
            <input type="checkbox" :checked="isChecked" @change="handleChange" />
        </div>
        <script>
            app = new Vue({
                el: '#app',
                data: {
                    isChecked: true
                },
                methods: {
                    handleChange(e) {
                        console.log(e.target.value);
                        this.isChecked = e.target.checked;
                    }
                }
            });
        </script>
        ```

      - 使用`v-model`的时候

        ```html
        <div id="app">
            {{isChecked}}
            <input type="checkbox" v-model="isChecked" />
        </div>
        <script>
            app = new Vue({
                el: '#app',
                data: {
                    isChecked: true
                }
            });
        </script>
        ```

   2. `text` 和` textarea` 元素使用 `value` property 和 `input` 事件；

   3. `select` 字段将 `value` 作为` prop` 并将 `change` 作为事件。
