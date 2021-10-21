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
