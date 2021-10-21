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
