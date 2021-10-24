# Vue初学上
## 编写Hello world
创建一个新的vue实例:
```js
Vue.createApp({
    data() {
        return {
            content: 1
        }
    },
    mounted() {
        setInterval(()=> {
            this.content += 1
        },1000)
    },
    template: '<div>{{content}}</div>'
}).mount('#root')
// 页面加载完成,会执行mounted()函数
// 双花括号表示里面是一个js表达式
```
## 翻转字符串
```js
 Vue.createApp({
        data() {
            return {
                content: 'hello world'
            }
        },
        // 方法全部写在methods中
        methods: {
            handleButtonClick() {
                this.content = this.content.split('').reverse().join('')
            }
        },
        template: `
        {{content}}
        <button 
        // 绑定事件使用v-on
        v-on:click="handleButtonClick">翻转</button>
        `
    }).mount('#root')
```
## 编写TODOList
```js
Vue.createApp({
        data() {
            return {
                inputValue: '',
                list:[]
            }
        },
        methods: {
            handleAddItem() {
                this.list.push(this.inputValue)
                this.inputValue = ''
            }
        },
        template: 
        // v-model用于将数据与data()中的对应值进行双向绑定
        `
        <div>
            <input v-model="inputValue" />
            <button v-on:click="handleAddItem">增加</button>
            <ul>
                <li v-for="(item,index) of list">{{item}} {{index}} </li>
            </ul>
        </div>
        `
    }).mount('#root')
```
## 关于组件
标签属性想和数据进行绑定的时候,需要使用v-bind指令

标签内部需要使用数据时,直接使用{{}}的插值表达式即可

如果组件需要使用父组件的值,需要使用props

## 生命周期
**beforeCreate: 在实例生成之前会自动执行的函数**

**created: 在实例生成之后会自动执行的函数**

**beforeMount: 在组件内容被渲染到页面之前自动执行的函数**

**Mounted: 在组件内容被渲染到页面之后自动执行的函数**

**beforeUpdate: 当数据发生变化时会立即自动执行的函数**

**Updated: 当数据发生变化，页面重新渲染后，会自动执行的函数**

**beforeUnmount: 当 Vue 应用失效是，自动执行的函数**

**Mounted: 当 Vue 应用失效时，且 dom 完全销毁之后，自动执行的函数**

## data methods 计算属性和侦听
```js
// data & methods & computed & watcher
  // computed 和 method 都能实现的一个功能，建议使用 computed，因为有缓存
  // computed 和 watcher 都能实现的功能，建议使用 computed 因为更加简洁
  const app = Vue.createApp({
    data() {
      return {
        message: "hello world",
        count: 2,
        price: 5,
        newTotal: 10,
      }
    },
    watch: {
      // price 发生变化时，函数会执行
      price(current, prev) {
        this.newTotal = current * this.count;
      }
    },
    computed: {
      // 当计算属性依赖的内容发生变更时，才会重新执行计算
      total() {
        return Date.now() + this.count;
        // return this.count * this.price
      }
    },
    methods: {
      formatString(string) {
        return string.toUpperCase();
      },
      // 只要页面重新渲染，才会重新计算
      getTotal() {
        return Date.now();
        // return this.count * this.price;
      },
    },
    template: `
     <div> {{message}} {{newTotal}} </div>
    `
  });
  const vm = app.mount('#root');
  ```

## 事件绑定
**事件修饰符: .prevent .self .once...**

**按键修饰符: .enter .tab .delete .backspace**

**鼠标修饰符: .left .right...**

**精确修饰符: .ctrl.exact 只有按住ctrl才会执行**

**传递多个参数时,需要使用原生event时,改成$event**

## 双向绑定
**双向绑定: v-model**
```js
// 用于input,textarea,CheckBox,radio,select
// 修饰符: lazy:懒加载(性能更高) number:类型转换 trim: 去除内容前后的空格
const app = Vue.createApp({
    data() {
        return {
            message: "hello"
            // 使用CheckBox时,如果有多个复选框,将message变成一个数组即可
            // 使用radio时,用字符串存储即可
            // 使用select时,将v-model绑定在seelct上
        }
    },
    template: `
    <div> 
     {{message}}
     <input v-model="message" />
    </div>
    `
});
const vm = app.mount('#root')
```

****
# Vue基础入门(中)
## 组件 局部组件和全局组件
**组件具备复用性,组件的数据只有在自己的组件内部使用**

**全局组件,只要定义了,处处可以使用,但性能不高**

**局部组件,定义了,要注册后才可以使用,性能较高,建议大写字母开头,驼峰命名**

**局部组件使用时,要做一个名字和组件间的映射对象,你不写映射,Vue底层也会尝试帮你做映射**
```js
// 全局组件
app.component("counter", {
    data() {
        return {
            count: 1
        }
    },
    template: 
    `
    <div>{{count}}</div>
    `
})
//局部组件
const counter = {
    data() {
        return {
            count: 1
        }
    },
    template: 
    `
    <div @click="counter += 1">{{count}}</div>
    `
}
// 使用局部组件,需要在父组件中通过components引入
const app  = Vue.createApp({
    components: {'Dell: counter}, 
    //compones: {counter},
    template: 
    `
    <div>
        <Dell />
    </div>
    `
});
```

## 关于组件间传值和传值校验
组件传值,使用props
```js
const app = Vue.createApp({
    template: 
    `
    <div><test content="hello world" /></div>
    `
});
//type: String Boolean Array Object Function Symbol
// required: 必填
// default: 默认值
app.compoonents("test", {
    // 传值校验
    // props: {
    //     content: String
    // }
    props:["content"],
    template: 
    `
    <div>{{content}}</div>
    `
});
const vm = app.mount("#root");
```
## 单项数据流的理解
**如果需要将一个用横线间隔的值向子组件传递时,props里应该用驼峰命名法**
```js
//父组件
template: 
`
<div><test :content-abc="content" /></div>
`
//子组件:
app.components("test",{
    props: ['contentAbc'],
    template: 
    `
    <div>{{contentAbc}}</div>
    `
})
```
**单项数据流的概念: 父组件的值可以流入子组件,但子组件不可更改父组件的值,是只读的**

## Non-Props属性初探
使用$attrs.进行Non-props属性传值

## 父子组件通过事件进行通信
子组件通过$emit的方法像父组件传递事件信息
```js
const app = Vue.createApp({
    data() {
        return{
            count: 1
        }
    },
    methods:{
        // 通过params对参数进行传递
        handleAddOne(param) {
            this.count += param;
        }
    }
    //触发事件使用驼峰命名,监听事件时使用横线命名
    template:
    `
    <div>
        <counter :count="count" @add-one="handleAddOne"/>
    </div>
    `
});
app.component("counter", {
    props:['count'],
    methods:{
        handleItemClick  () {
            this.$emit("addOne",2);
        }
    },
    template:
    `
    <div @click="handleItemClick">{{count}}</div>
    `
});
```
## 组件间双向绑定高级内容(选学)
**需要从父子组件传递多于一个值时,使用v-model:xx的语法**
```js
const app = Vue.createApp({
    data() {
        return{
            count: 1,
            count1: 1
        }
    },
    //触发事件使用驼峰命名,监听事件时使用横线命名
    template:
    `
    <counter v-model:count="count" v-model:count1="count1" />
    `
});
app.component("counter", {
    props:['count',"count1"],
    methods:{
        handleItemClick  () {
            this.$emit("update:count", this.count += 3);
        },
        handleItemClick1() {
            this.$emit("update:count1",this.count1 += 2);
        }
    },
    template:
    `
    <div @click="handleItemClick">{{count}}</div>
    <div @click="handleItemClick1">{{count1}}</div>
    `
});
```
modifiers:通过在v-model后通过点语法添加自定义修饰符
```js
props: {
    "modelValue": String,
    "modelModifiers": {
        default: ()=>({})
    }
},
```
## 使用插槽和具名插槽解决组件内容传递问题
```js
// slot 插槽
// slot中使用数据的作用域问题:
// 父模板里调用的数据属性, 使用的都是父模板里的数据
// 子模板里调用的数据属性, 使用的都是子模板里的数据
// 具名插槽
const app = Vue.createApp({
    template:
    `
    <myform>
        <div>提交</div>
    </myform>
    <myform>
        <submit>提交</submit>
    </myform>
    `
});
app.component("myform",{
    methods:{
        handleClick() {
            alert(123);
        }
    },
    template:
    `
    <div>
        <input />
        <slot></slot>
    </div>
    `
})
```
**如果需要将slot分开来,就需要用到v-slot:指令,(注意在外层添加template占位符,同时v-slot指令写在template占位符上) 子组件使用时则需要使用name属性**
```js
父组件:
<layout>
    <template v-slot:header>
        <div>header</div>
    </template>
     <template v-slot:footer>
        <div>footer</div>
    </template>
</layout>
子组件:
<div>
    <slot name="header"></slot>
    <div>content</div>
    <slot name="footer"></slot>
</div>
```

## 作用域插槽
**v-slot:header可以简写成: #header**
```js
父组件:
<list v-slot="slotProps">
// 通过slotProps.item接收子组件传递过来的内容
    <span>{{slotProps.item}}</span>
</list>
子组件:
<div>
    <slot v-for="item in list" :item="item" />
</div>
```

## 动态组件和异步组件
**动态组件直接使用<component />,根据数据的变化, 结合component标签,来随时动态切换组件的显示的, 若想在页面重新渲染后, 数据保持不变, 需要在外部包裹<keep-alive>**
```js
父组件:
const app = Vue.createApp({
    data() {
        return{
            currentItem: "input-item"
        }
    },
    methods: {
        handleClick() {
            if(this.currentItem === "input-item") {
                this.currentItem = "common-item";
            }else {
                this.currentItem = "input-item";
            }
        }
    },
    template: 
    <keep-alive>
        <component :is="currentItem" />
    </keep-alive>
    <button @click="handleClick">切换</button>
});
app.component("input-item", {
    template: <input />
});
app.component("common-item", {
    template: <div>hello world</div>
})
```

**异步组件(一般是通过Promise进行异步操作)**
```js
父组件:
<div>
    <common-item />
    <async-common-item />
</div>
子组件:
app.component("common-item", {
    template: `<div>hello world</div>`
});

app.component("async-common-item",Vue.defineAsyncComponent(()=> {
  return new Promise((resolve,reject) => {
    setTimeout(() => {
        resolve({
            template: `<div>this is an async component</div>`
        })
    })
  },4000)  
}))
```

## 做点补充
**v-once: 只对指定标签内容渲染一次**

**ref: 获取dom节点/组件引用的一个语法**

**provide/inject： 跨组件之间多级之间值得传递** 

## Vue实现基础CSS过渡与动画效果
```js
<style>
    // 动画
    @keyframes leftToRight {
      0% {
        transform: translateX(-100px);
      }
      50% {
        transform: translateX(-50px);
      }
      0% {
        transform: translateX(0px);
      }
    }
    .animation {
      animation: leftToRight 3s;
    } */

    // 过渡
    .transition {
      transition: 3s background-color ease;
    }
    .blue {
      background: blue;
    }
    .green {
      background: green;
    } */

    .transition {
      transition: 3s background-color ease;
    }

  </style>
  <script src="https://unpkg.com/vue@next"></script>
</head>
<body>
  <div id="root"></div>
</body>
<script>
  const app = Vue.createApp({
    data() {
      return {
        styleObj: {
          background: 'blue'
        }
      }
    },
    methods: {
      handleClick() {
        if(this.styleObj.background === 'blue') {
          this.styleObj.background = 'green';
        }else {
          this.styleObj.background = 'blue'
        }
      }
    },
    template: `
      <div>
        <div class="transition" :style="styleObj">hello world</div>
        <button @click="handleClick">切换</button>
      </div>
    `
  });

  const vm = app.mount('#root');
```
## 使用transition标签实现单元素组件的过渡和动画效果
**1. 使用transition标签,如果需要使用过渡,需要在style中加入几个固定样式:.v-eneter/leave-from, .v-enter/leave-to, .v-enter/leave-active**

**2. 使用动画效果时,类似的,需要在style中额外写入几个固定样式:.v-enter/leave-active(里面写animation 动画名称 时间)**

**3. 也可以在transition标签里自定义类的名字:**
```html
<transition enter-active-class="hello">

</transition>
```
**4. 实现js动画效果时, 需要在transition中加上:css="false"属性, 并加上对应的钩子: before-enter, enter(el, done) -- done用于告知动画已执行结束, after-enter, before-leave, leave,  leave-after**

## 列表动画
**使用transition-group**

## 状态动画

****
# Vue基础入门(下)
## 关于mixin
**mixin是混入,就是将mixin里的数据或函数混入组件里进行使用**
```js
const myMixin = {number:1}
const app = Vue.createApp({
    data() {
        return{
            number: 1
        }
    },
    mixins:[myMixin],
    template:`<div>{{number}}</div>`
})
// 局部mixin:子组件中的变量不能获取到mixin里的值,只能崽子组件中在调用以下mixins,类似于局部组件,即需要使用mixins:[]接收
// 
```
**需要注意以下几点**

**1. 组件里的data和methods优先级高于mixin里的data和methods,会覆盖mixin**

**2. 生命周期函数,不会覆盖,先执行mixin里的,后执行组件里的**

**3. 自定义的属性,组件中的属性优先级高于mixin属性的优先级**

## 开发实现Vue中的自定义指令
**app.directives() --- 全局注册**

**const directives = xxx  --局部注册**




## Teleport传送门
**使用teleport标签将对应标签传送到指定位置:**
```js
// 这段代码的意思是将div标签对传送到挂body下,使body成为该元素的父亲
<teleplate to="body">
    <div class="mask" v-show="show"></div>
</teleport>
```

## 探究render函数(底层)
**template -> 解析成render ->调用h函数 ->生成虚拟DOM(JS对象) ->真实DOM ->展示到页面上控制展示对应标签**

## 插件的定义与使用
```js
// 定义插件
const myPlugin = {
    install(app,options) {
        // 使用app.provide()对插件进行扩展
        app.provide("name", "Phil")
        console.log(app,options)
        // options接受传进来的一些值
    }
}
app.component("my-title", {
    // 使用插件扩展内容,需要引入inject
    inject: ["name"],
    template:`<div>{{name}}</div>`
})
// 使用插件 后面的name就是自己传进去的参数,可以在options中打印出来
app.use(myPlugin , {name: "phil"})
```

## 实际开发 -- 数据校验插件
```js
    const app = Vue.createApp({
        data() {
            return{
                name: "Phil",
                age: 23,
            }
        },
        rules: {
            age: {
                validate: age => age > 25,
                message: "too young too simple"
            },
            name: {
                validate: name => name.length >= 4,
                message: "too short"
            }
        },
        template: 
        `
        <div>name:{{name}}, age:{{age}}</div>
        `
    });
// 对插件进行封装
const validatorPlugin = (app,options) => {
    app.mixin({
        created() {
            for(let key in this.$options.rules) {
                const item = this.$options.rules[key];
                this.$watch(key,(value)=> {
                    const result = item.validate(value);
                    if(!result) console.log(item.message);
                })
            }
        }
    })
}
// 使用插件
app.use(validatorPlugin);
const vm = app.mount("#root");
```

# Vue3.x -Composition API
 
## Setup函数的使用
**1. setup方法是在实例完全初始化(created)之前执行的,方法里返回的数据可以直接被模板外部引用**

**2. 由于实例还未被初始化,所以在setup中无法使用this,实例初始化后,setup方法会被挂载到实例上并且可以被实例中的其他函数调用**
```js
setup(props,context) {
    // name 和 handleClick函数都可以直接被模板外部直接使用
    return {
        name: "phil",
        handleClick: ()=> {
            alert("hello")
        }
    }
}
```

## ref,reactive响应式引用的用法及原理
**原理:通过proxy对数据进行封装,当数据变化时,触发模板等内容的更新**

**ref处理基础类型的数据 ```let name = ref("phil")```这样的name就成了一个响应式的变量,"phil"变成 proxy({value:"phil")}这样一个响应式引用,但需要修改成```name.value = "phil"```才会生效,另外,要使用ref,需要通过```const {ref} = Vue```来引入**

**reactive处理非基础类型的数据(数据,对象等) ```const nameObj = reactive({name:"phil"});```同样也需要通过```const{reactive} = Vue进行引入``**

**toRefs()将reactive类型的格式,转化成ref的格式,使reactive里的数据也具备响应式的特性 同样需要通过```const {toRefs} = Vue```进行引入**

## toRef && context参数
**context中接收三个参数:***

**1. attrs: 接收的是Non-props属性,相当于不用Composition API时的this.$attrs**

**2. slots: 可以直接调用slots,配合h函数,slots相当于不用Composition API时的this.$slots**

**3. emit: 调用父组件的方法,相当于不用Composition API时的this.$emit**

## 总结: ref, reactive, toRefs, toRef辨析
#### ref
1.作用:接受一个内部值并返回响应式可变的ref对象,ref对象具有指向内部值得单个property.value

**·**
ref需要通过```const {ref} = Vue```进行引用

**·**
ref一般定义基础数据类型

**·**
模板中使用数据时,会自动调用value属性,不需要使用value

sample:
```js
var app = Vue.createApp({
    template:`<div>{{name}}</div>`,
    setup() {
        const{ref} = Vue;
        let name = ref("hello");
        setTimeout(()=> {
            name.value = "phil"
        },2000);
        return{name}
    }
})
```
### reactive
1.作用：接受一个内部值并返回响应式且可变的对象

**·**
reactive需要通过```const {reactive} = Vue```进行引用

**·**
ref一般定义非基础数据类型

sample:
```js
var app = Vue.createApp({
    template:`<div>{{arr[0]}}</div>`,
    setup() {
        const{reactive} = Vue;
        let arr = reactive([123]);
        setTimeout(()=> {
            arr[0] = 456
        },2000);
        return{arr}
    }
})
```
2.数据解构:
**·**
如果解构的数据时引用数据类型,解构后仍是响应式的

**·**
如果解构数据的值是基本类型,那么解构之后再返回就不具备响应式了,这时候可以通过toRefs进行包装

### toRefs
原理:{{name:"hello"}}通过reactive包装后会变为proxy({name:"hello"}),再次通过toRefs包装后会变成proxy({name:proxy({value:"hello"})})

sample:
```js
var app = Vue.createApp({
    template:`<div>{{name}}</div>`,
    setup() {
        const{toRefs,reactive} = Vue;
        let obj = reactive({name:"hello"});
        setTimeout(()=> {
            obj.name = "phil"
        },2000);
        const name = toRefs(obj);
        return{name}
    }
})
```
toRefs封装,如果响应式数据本身不存在某个数据,那么不会给一个默认的引用,而是给一个undefined,不具备响应式,如果想给默认引用,推荐使用toRef方法

### toRef
原理: toRef方法不需要解构,两个参数,一个总数据,一个获取的数据,

## 练练手 --- 使用Composition API开发todolist
**使用setup函数进行响应式开发,将相关数据,方法通过return的形式供外部调用**

```js
 const app = Vue.createApp({
        setup() {
            const {ref,reactive} = Vue;
            const inputValue = ref("");
            const list = reactive([]);
            const handleInputValueChange = (e) => {
                inputValue.value = e.target.value;
            }
            const handleSubmit = () => {
                list.push(inputValue.value);
                inputValue.value = "";
            }
            return {
                inputValue,
                list,
                handleInputValueChange,
                handleSubmit
            }
        },
        template: `
        <div>
            <div>
                <input :value="inputValue"  @input="handleInputValueChange" />
                <button @click="handleSubmit">提交</button>
            </div>
            <ul>
                <li v-for="(item,index) in list" :key="index">{{item}}</li>
            </ul>
        </div>`
    });
```
## computed方法生成计算属性
**computed计算属性**

放回调函数里:
```js
let countAddFive = computed(()=return count.value += 5;)
```
放对象里:
```js
let countAddFive = computed({
    get: () => {
        return countObj.count + 5;
    },
    set: (param) => {
        countObj.count = param - 5;
    }
})
```
## watch 和 watchEffect的使用和差异性
**watch具备一定的惰性,参数可以拿到原始和当前值,可以侦听多个参数,用一个侦听器承载**
```js
watch(name,(currentValue,prevValue) => {
    console.log(currentValue, prevValue);
})
```
**watchEffect侦听器(偏向Effect)**

**```watchEffect(()=>{})```**

**立即执行,没有惰性,immediate**

**不需要传递你要侦听的内容,自动感知代码依赖,不需要传递很多参数,只要一个回调函数**

**不能获取之前数据的值**

## 生命周期函数新写法

**beforeMount => onBeforeMount**

**mounted => onMounted**

**beforeUpdate => onBeforeUpdate**

**beforeUnmount => onBeforeUnmount**

**unmounted => onUnmounted**

注:composition API没有提供beforeCreate和created,因为setup执行事件点在beforeCreate和created之间

新增生命周期函数:
1. onRenderTracked:每次渲染后重新收集响应式依赖,在onMounted和onUpdated之前;
2. onRenderTriggered:每次页面重新渲染之前自动执行,在beforeUpdate之前

## Provide,inject,模板Ref的用法
TBC

# Vue脚手架,路由与VueX
## VueCLI的使用和单文件组件
**使用脚手架工具必须用Node.js**

1、安装node.js，并且自动安装npm

（node -v查看Node.js版本号）

（npm -v查看npm版本号）

2、输入 npm install nrm -g，安装完后可以获取很多国内镜像源。

3、输入nrm ls，显示很多国内镜像源。选择其中一个，如：nrm use taobao

**安装Vue脚手架工具：**

脚手架工具是快速搭建工程的工具，其中webpack打包、工程目录设计都已经搭建好。

1、卸载旧版本：npm uninstall vue-cli -g

2、安装新的：npm install -g @vue/cli

3、在文件目录打开cmd，输入：vue create 文件夹名

4、跳出选择。如不选自定义，选择人工。

5、跳出选择列表。。。。。。

6、安装完后，demo目录下cmd或VScode的terminal里输入npm run serve启动服务器。

7、cmd里按住CTRL+C就能退出。

**单文件组件**
xx.vue文件包含:1. template标签对 2. script:组件的逻辑 3. 组件的样式

## Vue-Router路由的理解与使用
```import router from "./router"```

**路由:是指根据url的不同,展示不同的内容**

配置路由项:
```js
const routers = [
    {
        path: "/",
        name: "Home",
        component: Home
    },
    {
        path: "/about",
        name: "About",
        // 异步加载路由,需要跳转时才加载这个页面
        component: () => import("../../About.vue")
    }
]
```

router-link是跳转路由的标签:
```js
<router-link to="/">Home</router-link>
<router-linkto="/about">About</router-link>
```
router-view展示当前路由对应的组件内容:
```js
<router-view />
```
**index.js里面是对路由的配置,App.vue里面是对路由的展示和跳转**

## VueX语法详解
VueX实际上是一个数据管理框架,创建了一个全局唯一的仓库来存放数据,可以通过this.$store.state.name进行使用

**用createStore创建一个store,数据放到state里,然后从组件里的computed中获取数据**
```js
export default createStore({
    state: {
        name: "pihl"
    },
    mutations: {},
    actions: {},
    modules: {}
})
```
**修改store的数据步骤:**

1. 调用this.$store.dispatch(change), 派发一个action,名字叫做change --- dispatch和actions做关联
   
2. 感知到change这个action,执行store中actions下面的change方法

3. commit 提交一个叫做change的数据改变 --- commit和mutation做关联

4. mutation感知到提交对的change改变,执行change方法改变数据

5. 在mutation里修改数据

```js
export default createStore({
    state: {
        name: "pihl"
    },
    mutations: {
        // 4. mutation感知到提交对的change改变,执行change方法改变数据
        change() {
            // 5. 在mutation里修改数据
            this.state.name = "xia"
        }
    },
    actions: {
        // 2. 感知到change这个action,执行store中actions下面的change方法
        change() {
            // 3. commit 提交一个叫做change的数据改变
            this.commit("change");
        }
    },
    modules: {}
})
```
注:如果不涉及异步修改数据的话,可以考虑直接对数据进行commit操作:
```js
// 相应组件中:
this.$store.commit("change");
// 接着直接在mutation中执行change方法修改数据
//mutation中一般只写同步代码,一般不写异步代码,而actions一般放异步代码
mutations: {
        change() {
            // 5. 在mutation里修改数据
            this.state.name = "xia"
        }
    },
```

## Composition API使用VueX
**Composition API里面的核心要引入useStore,才能获取到全局对象**
```js
import {toRefs } from "vue"
import {useStore} from "vuex"
export default {
    name: "Home",
    setup() {
        const store = useStore();//获取全局对象
        const name = toRefs(store.state);
        const handleClick = () => {
            store.dispatch("getData");
        }
        return {
            name,
            handleClick
        }
    }
}
```
## 使用axios发送ajax请求
引入axios:```import axios from "axios"```
```js
axios.get("url").then((response)=>{
    const msg = response.data.message;

})
```
## one more thing --- 关于大小写
**1. 如果组件名称是小写字母,且带有连字符,在使用组件的时候,也需要同样的写法**

**2. 如果是首字母大写的驼峰式命名,在使用组件时,连字符或驼峰式都是可以的**

**3. 在自定义事件名称时, 事件监听器在DOM模板中会被自动转换成全小写(因为HTML是大小写不敏感的). 例如:自定义事件名称为:@myEvent, 会自动变成@myevent, 或写成带有连字符的形式; 当触发事件时myEvent时, 则会监听不到**


## ---完结撒花~常回来看看---







