# Vue
    vue官网说：Vue.js（读音 /vjuː/，类似于 view） 是一套构建用户界面的渐进式框架。与其他重量级框架不同的是，Vue 采用自底向上增量开发的设计。 
1. Vue的优点
>- 易用
>>- 已经会了 HTML、CSS、JavaScript？即刻阅读指南开始构建应用！
>- 灵活
>>- 不断繁荣的生态系统，可以在一个库和一套完整框架之间自如伸缩。
>- 高效
>>- 20kB min+gzip 运行大小
>>- 超快虚拟 DOM 
>>- 最省心的优化
>- 虚拟的DOM的核心思想是：对复杂的文档DOM结构，提供一种方便的工具，进行最小化地DOM操作。这句话，也许过于抽象，却基本概况了虚拟DOM的设计思想
>>- (1) 提供一种方便的工具，使得开发效率得到保证            type: 'POST',

>>- (2) 保证最小化的DOM操作，使得执行效率得到保证
# 插值语法
    {{ }} 用于输出对象属性和函数返回值。  
>- 语法
>>- {{ 绑定的对象属性 }} 
>- 代码
```html
<div id="app">
    <!--普通插值-->
    <p>姓名:{{name}}</p>
    <p>年龄:{{age}}</p>
    <!--{{}}执行表达式-->
    <p>{{ 5+5 }}</p>
    <p>{{ ok ? 'YES' : 'NO' }}</p>
    <p>{{ name.split('').reverse().join('') }}</p>
    
</div>
```
```javascript
// 创建vue实例对象
var app = new Vue({
    // 绑定元素
    el:'#app',
    // 所有的数据都放在数据属性data中
    data:{
        name:'小杰',
        age:18,
        ok:true
    }
})
```
>- 浏览器渲染结果
```
姓名:小杰
年龄:18
10
YES
杰小
```
我们已经成功创建了第一个 Vue 应用！看起来这跟渲染一个字符串模板非常类似，
但是 Vue 在背后做了大量工作。现在数据和 DOM 已经被建立了关联，所有东西都是响应式的。
当我们打开 JavaScript 控制台 (就在这个页面打开)，并修改 app.name 或者 app.$data.name 的值，
你将看到上例相应地更新。
```
app.$data.name = 'Hello Vue'

>- 网页渲染结果
姓名:Hello Vue
年龄:18
10
YES
euV olleH
```
# Vue指令
    指令是带有 v- 前缀的特殊属性。所谓指令系统，大家可以联想咱们的cmd命令行工具，只要我输入一条正确的指令，系统就开始干活了。在vue中，指令系统，设置一些命令之后，来操作我们的数据属性，并展示到我们的DOM上

### v-if,  v-else-if, v-else， v-show
    在vue中，我们使用以上指令，控制dom元素显示
```html
<div id="app">
    <p v-if = "type === 'thunder'">打雷</p>
    <p v-else-if = "type === 'rain'">下雨</p>
    <p v-else >雷阵雨</p>
    <!--v-show 指令也是控制dom元素知否限制， 但是仅仅是操控元素的display属性-->
    <p v-show="ok">网址导航</p>
</div>
```
```javascript
var app = new Vue({
    el:'#app',
    data:{
        type :'thunder',
        isShow:true
    }
})
```
### v-html
    默认在页面渲染HTML标签的时候， 会把整个标签显示到页面不做渲染，
    v-html 功能是直接把标签渲染到页面上
```html
<div id="app" v-html="str"></div>
```
```javascript
var app = new Vue({
    el:"#app",
    data:{
        str:"<p>哈哈哈</p>"
    }
})
```
### v-bind
    操作元素的属性, 可以把元素属性绑定位Vue数据对象
    v-bind 可以简写成 ":"  如 : v-bind:title="time" 简写成 :title='time'
 ```html
<p v-bind:title="time">鼠标悬停显示页面渲染时间</p>
<a v-bind:href="hrefUrl">{{hrefUrl}}</a>

<p class="static" v-bind:class="{box:isGold}">绑定class</p>
<p v-bind:class="{box:isGold, box2:isRed}">绑定class</p>
<li v-bind:class="{active: 1 === activeCount}"></li>

<p v-bind:class="[activeClass, errorClass]">绑定class</p>
<p v-bind:class="[errorClass ,isActive ? activeClass : '']">绑定class</p>

<p v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }">绑定style</p>
<p v-bind:style="styleObject">绑定style</p>

```
```javascript
var app = new Vue({
            el:'#app',
            data:{
                time:new Date().toLocaleString(),
                hrefUrl:'www.baidu.com',
                isGold:false,
                isRed:true,
                isActive:false,
                activeClass: 'active',
                errorClass: 'text-danger',
                activeCount:1
            }
        })
```
### v-on
    v-on 指令监听 DOM 事件，并在触发时运行一些 JavaScript 代码。 
    v-on 的所有事件函数必须定义在vue实例中的 "methods" 中
    v-on 可以缩写成“@”，  如 v-on:click="" 写成 @click=''
1. 实例:点击按钮更换class
```css
.box1{
            background-color: gold;
}
.box2{
    background-color: red;
}

.box{
    width: 50px;
    height: 50px;
}
```
```html
<div id="app">
    <div class="box" v-bind:class="[isActive ? box1 : box2]"></div>
    <button v-on:click="update">切换样式</button>
    <!--<button @click="update">切换样式</button>-->
</div>
```
```javascript
var app = new Vue({
    el:'#app',
    data:{
        isActive:false,
        box1: 'box1',
        box2: 'box2'
    },
    // methods， 用来写
    methods:{
        update(){
            this.isActive = !this.isActive;
        }
    }
})
```
### v-for
    v-for 指令需要以 site in sites 形式的特殊语法， sites 是源数据数组并且 site 是数组元素迭代的别名。
>- 语法
```html
// 类似Python的for循环
1. 循环一个数组 : array: [{id:1, name:'张三'}, {id:2, name:'李四'}, {id:3, name:"王5"}]
<li v-for = "item in array">{{ item.name }}</li>
2. 循环一个对象： objects:{name:'小杰', age:18, love:"篮球"}
<ul>
              //(值，建，索引)
    <li v-for="(value, key, index) in objects">{{index}}-{{key}}:{{value}}</li>
</ul>
2. 迭代一个整数
<li v-for="n in 10">{{ n }}</li>
```
>- 实例：轮播图
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Vue</title>
    <style>
        * {
            padding: 0;
            margin: 0;
        }

        .lunbo {
            width: 900px;
            margin: 50px auto;
        }

        .lunbo img {
            width: 900px;
            margin: 50px auto;
        }

        .lunbo ul {
            list-style: none;
            width: 230px;
            height: 10px;
            margin: 0 auto;
        }

        .lunbo ul li {
            float: left;
            width: 10px;
            height: 10px;
            border: 1px solid;
            margin-left: 10px;
            border-radius: 50%;
        }

        .active {
            background-color: gold;
        }

        .lunbo ul .next {
            border: 0 solid;
            width: 50px;
            margin-top: -5px;
        }

    </style>
    <script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
    <div class="lunbo">
        <!--轮播图图片-->
        <img :src="loopImgSrc" :alt="loopImgAlt" @mouseenter="mouseenterImg" @mouseleave="mouseleaveImg">
        <ul>
            <li class="next" @click="preImg"><button>上一张</button></li>
            <li v-for="(item, index) in loopImgArr" :class="{active: index+1 === isActive}" @click="updateImg(item)"></li>
            <li class="next" @click="nextImg"><button>下一张</button></li>
        </ul>
    </div>
</div>
<script>
    var app = new Vue({
        el: '#app',
        data: {
            // 保存所有的幻灯片
            loopImgArr: [
                {id: 1, src: "https://m.360buyimg.com/babel/jfs/t1/6282/39/2198/86668/5bd00ebbE84d598a6/adf5ebcb5ee9703c.jpg", alt: 'hdp01'},
                {id: 2, src: "https://img1.360buyimg.com/pop/jfs/t1/909/16/12522/99056/5bd2dd80E0b394a1f/48e57dd8b7c1755a.jpg", alt: 'hdp02'},
                {id: 3, src: "https://img1.360buyimg.com/da/jfs/t1/5832/9/9363/82996/5bacc80dEf5e73193/39ec749d5be65c84.jpg", alt: 'hdp03'},
                {id: 4, src: "https://m.360buyimg.com/babel/jfs/t1/7714/5/2568/83235/5bd2dc32Edcdbe568/bf424d6b636ee186.jpg", alt: 'hdp04'},
                {id: 5, src: "https://img11.360buyimg.com/da/jfs/t16663/131/1424030023/110119/f5bc9082/5acb3b19Nc3efd8d0.jpg", alt: 'hdp05'}
            ],
            // 当前显示的图片
            loopImgSrc: 'https://m.360buyimg.com/babel/jfs/t1/6282/39/2198/86668/5bd00ebbE84d598a6/adf5ebcb5ee9703c.jpg',
            // 当前显示图片的标题
            loopImgAlt: "hdp01",
            // 当前激活第几张图片
            isActive: 1,
            // 图片轮播的定时器
            loopInterval: null
        },
        // Vue声明周期在初始化阶段执行的函数
        created(){
            this.loopInterval = setInterval(this.nextImg, 2000)
        },
        methods: {
            // 小圆点点击切换图片事件
            updateImg(item) {
                this.loopImgSrc = item.src;
                this.loopImgAlt = item.alt;
                this.isActive = item.id;
            },
            // 下一张按钮点击事件
            nextImg() {
                if (this.isActive === this.loopImgArr.length) {
                    this.isActive = 0;
                }
                var imgObj = this.loopImgArr[this.isActive];
                this.isActive++;
                this.loopImgSrc = imgObj.src;
                this.loopImgAlt = imgObj.alt;
            },
            // 上一张按钮点击事件
            preImg() {
                if (this.isActive === 1) {
                    this.isActive = this.loopImgArr.length;
                }else {
                    this.isActive --;
                }
                var imgObj = this.loopImgArr[this.isActive-1];
                this.loopImgSrc = imgObj.src;
                this.loopImgAlt = imgObj.alt;
            },
            // 光标移动到图片事件
            mouseenterImg(){
                clearInterval(this.loopInterval)
            },
            // 光标移动上去事件
            mouseleaveImg(){
                this.loopInterval = setInterval(this.nextImg, 2000)
            }

        }
    })

</script>
</body>
</html>
```
# Vue 计算属性
    计算属性好比监听数据数据， 当数据属性发生改变的时候出发计算属性的get和set方法，
    主要目的在于简化模板中的语法， 计算属性定义在 computed对象中
1. 实例
```html
<div id="app">
    {{msg.split('').reverse().join('')}}
    <br>
    {{reverseSre}}
</div>
```
```javascript
var app = new Vue({
        el: "#app",
        data: {
            msg:'Hello Vue'
        },
        // 计算属性，主要目的在于监听数据属性的变动
        computed:{
            // 计算属性默认只有get方法
            //reverseSre(){
             //   return this.msg.split('').reverse().join('');
            //}
            reverseSre:{
                // reverseSre SET方法， 判断如果字符串开头是Hello则改变数据属性的值
                set(newValue){
                    if(newValue.indexOf('Hello') === 0){
                        this.msg = newValue;
                    }
                },
                // 反转字符串
                get(){
                    return this.msg.split('').reverse().join('');
                }
            }
        }
    })
```
# 表单输入绑定 v-model
    你可以用 v-model 指令在表单 <input>、<textarea> 及 <select> 元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素。
## v-model使用
![](http://www.runoob.com/wp-content/uploads/2017/01/20151109171527_549.png)
### 绑定输入框
```html
<input type="text" v-model="msg">
<p>{{msg}}</p>

```
### 单选框，复选框的绑定
```html
<div id="app">
  <p>单个复选框：</p>
  <input type="checkbox" id="checkbox" v-model="checked">
  <label for="checkbox">{{ checked }}</label>
    
  <p>多个复选框：</p>
  <input type="checkbox" id="runoob" value="Runoob" v-model="checkedNames">
  <label for="runoob">Runoob</label>
  <input type="checkbox" id="google" value="Google" v-model="checkedNames">
  <label for="google">Google</label>
  <input type="checkbox" id="taobao" value="Taobao" v-model="checkedNames">
  <label for="taobao">taobao</label>
  <br>
  <span>选择的值为: {{ checkedNames }}</span>
</div>
 
<script>
new Vue({
  el: '#app',
  data: {
    checked : false,
    checkedNames: []
  }
})
</script>
```
### 单选按钮
```html
<div id="app">
  <input type="radio" id="runoob" value="Runoob" v-model="picked">
  <label for="runoob">Runoob</label>
  <br>
  <input type="radio" id="google" value="Google" v-model="picked">
  <label for="google">Google</label>
  <br>
  <span>选中值为: {{ picked }}</span>
</div>
 
<script>
new Vue({
  el: '#app',
  data: {
    picked : 'Runoob'
  }
})
</script>
```
### Select下拉框
```html
<div id="app">
  <select v-model="selected" name="fruit">
    <option value="">选择一个网站</option>
    <option value="www.runoob.com">Runoob</option>
    <option value="www.google.com">Google</option>
  </select>
 
  <div id="output">
      选择的网站是: {{selected}}
  </div>
</div>
 
<script>
new Vue({
  el: '#app',
  data: {
    selected: '' 
  }
})
</script>
```
## v-model修饰符
>- .lazy
>>- 默认情况下，v-model 在每次 input 事件触发后将输入框的值与数据进行同步 (除了上述输入法组合文字时)。你可以添加 lazy 修饰符，从而转变为使用 change 事件进行同步：
```html
<!-- 在“change”时而非“input”时更新 -->
<input v-model.lazy="msg" >
```
>- .number
>>- 如果想自动将用户的输入值转为数值类型，可以给 v-model 添加 number 修饰符
```html
<input v-model.number="age" type="number">
```
>- .trim
>>- 如果要自动过滤用户输入的首尾空白字符，可以给 v-model 添加 trim 修饰符
```html
<input v-model.trim="msg">
```

## 计算属性实现v-model功能(双向数据绑定)
```html
<div id="app">
    <input type="text" :value="isMsg" @input="inputMsg">
    <p>{{isMsg}}</p>
</div>
```
```javascript
var app = new Vue({
        el: "#app",
        data: {
            msg: ''
        },
        methods:{
            inputMsg(e){
                this.isMsg = e.target.value;
            }
        },
        computed: {
            isMsg:{
                set(newValue){
                    this.msg = newValue;
                },
                get(){
                    return this.msg
                }
            }

        }
    })
```
# Vue 组件
    组件（Component）是 Vue.js 最强大的功能之一。
    组件可以扩展 HTML 元素，封装可重用的代码。
    组件系统让我们可以用独立可复用的小组件来构建大型应用，几乎任意类型的应用的界面都可以抽象为一个组件树：
![](http://www.runoob.com/wp-content/uploads/2017/01/components.png)
## 组建的使用-全局组件
```html
<div id="app">
    <!--使用组件-->
     <href></href>
     <href></href>
</div>
<script >
// 组件的创建
    Vue.component(
        'href',
        {
            // 组件中必须包含data方法，而且必须return一个对象
            data(){
                return {
                    // 数据属性定义的地方
                    src:'https://www.baidu.com'
                }
            },
            // 存放模板内容s
            template:'<a :src="src"></a>'
        }
    );
    new Vue({el: "#app"});
</script>
```
## 组件的使用-局部组件
```html
<div id="app">
    <runoob></runoob>
</div>
 
<script>
var Child = {
  template: '<h1>自定义组件!</h1>'
};
 
// 创建根实例
new Vue({
  el: '#app',
  components: {
    // <runoob> 将只在父模板里可用
    'runoob': Child
  }
})
</script>
```
## 模块中使用组件
1. 一个“.vue”文件则是一个组件，在每个组件中有三部分组成
>- template
>>- 写HTML代码, 注意template中的标签最后要使用一个标签包裹，不然会出错
```html
<template>
  <div id="app">
    <h3>{{msg}}</h3>
  </div>
</template>
```
>- script
>>- 写JS代码
```javascript
export default {
  name:"App",
  data(){
    return {
      msg:"HELLO 组件"
    }
  }
}
```
>- style
>>- 写css代码
```html
<style>
#app{
background-color: gold;
}
</style>
```
2. 使用组件

2.1 创建组件
>- projactName/src/components/Vheader.vue
```html
<template>
  <div class="app">
    {{msg}}
  </div>
</template>

<script>
  export default {
    name: "Vheader",
    data() {
      return {
        msg: "Header组件"
      }
    }
  }
</script>

<style scoped>
  .app{
    width: 100%;
    height: 40px;
    background-color: aliceblue;
  }
</style>

```
2.2 使用组件
>- projactName/src/App.vue
>>- 使用组件流程
>>>- 1. 导入Vheader组件 （import Vheader from "./components/Vheader"）
>>>- 2. 注册组件 （components: {Vheader}）
>>>- 3. 使用组件 （\<Vheader\>\</Vheader\>）
```html
<template>
  <div id="app">
    <!--3. 使用组件-->
    <Vheader></Vheader>
    <h3>{{msg}}</h3>

  </div>
</template>

<script>
  //1. 导入Vheader组件
  import Vheader from "./components/Vheader"

  export default {
    name: "App",
    data() {
      return {
        msg: "HELLO 组件"
      }
    },
    components: {
      // 2. 注册组件
      Vheader
    }
  }
</script>

<style>

</style>

```
## 父子组件传值-prop
    在Vue中是单项数据流，所有的 prop 都使得其父子 prop 之间形成了一个单向下行绑定：父级 prop 的更新会向下流动到子组件中，但是反过来则不行。这样会防止从子组件意外改变父级组件的状态，从而导致你的应用的数据流向难以理解。
### 父组件传递数值到子组件
>- 步骤
1. 父组件中定义数据
```html
headerList:['首页', '我的笔记', '在线模板', 'Vue']
```
2. 给子组件自定义属性的形式传递给子组件
```html
<!--绑定自定义属性 “hh” 并把 headerList 赋值给hh-->
<Vheader :hh="headerList"></Vheader>
```
3. 在子组件中接受父组件传递过来的数值
```html
<!--接受过来数据需要先在props进行数据验证-->
props:{
      hh:{
        type:Array
      }
```
4. 在子组件中验证完毕后使用传递过来的数据
```html
<ul>
  <!--遍历父组件传递过来的内容-->
  <li v-for="header in hh">{{ header }}</li>
</ul>
```
>- 子组件 Vhandel.vue
```html
<template>
  <div class="app">
    <ul>
      <!--遍历父组件传递过来的内容-->
      <li v-for="header in hh">{{ header }}</li>
    </ul>
    <button @click="clickHandel">添加菜单</button>
  </div>
</template>

<script>
  export default {
    name: "Vheader",
    data() {
      return {
        msg: "Header组件"
      }
    },
    methods:{
      clickHandel(){
        this.hh.push('大神')
      }
    },
    // props用来做数据验证， 看父组件传递过来的数据是否是指定的类型
    props:{
      hh:{
        type:Array
      }
    }
  }
</script>

<style scoped>
  .app{
    width: 100%;
    height: 40px;
    background-color: aliceblue;
    margin: 0 auto;
  }
  ul li{
    list-style: none;
  }
  li{
    text-align: center;
    line-height: 40px;
    border: 1px solid #000;
    width: 100px;
    height: 40px;
    float: left;
  }
</style>

```
>- 父组件 App.vue 
```html
<template>
  <div id="app">
    <!--绑定自定义属性传递给子组件-->
    <Vheader :hh="headerList"></Vheader>
    {{headerList}}
    <button @click="getHeader">获取handel</button>
  </div>
</template>

<script>
  import Vheader from "./components/Vheader"
  export default {
    name: "App",
    data() {
      return {
        msg: "HELLO 组件",
        // 此值需要传递给子组件
        headerList:['首页', '我的笔记', '在线模板', 'Vue']
      }
    },
    methods:{
      getHeader(){
        alert(this.headerList)
      }
    },
    components: {
      Vheader
    }
  }
</script>

<style>
  #app{
    width: 100%;
  }
</style>

```
## Prop 验证
    我们可以为组件的 prop 指定验证要求，例如你知道的这些类型。如果有一个需求没有被满足，则 Vue 会在浏览器控制台中警告你。这在开发一个会被别人用到的组件时尤其有帮助。
    为了定制 prop 的验证方式，你可以为 props 中的值提供一个带有验证需求的对象，而不是一个字符串数组
```javascript
{
  props: {
    // 基础的类型检查 (`null` 匹配任何类型)
    propA: Number,
    // 多个可能的类型
    propB: [String, Number],
    // 必填的字符串
    propC: {
      type: String,
      required: true
    },
    // 带有默认值的数字
    propD: {
      type: Number,
      default: 100
    },
    // 带有默认值的对象
    propE: {
      type: Object,
      // 对象或数组默认值必须从一个工厂函数获取
      default: function () {
        return { message: 'hello' }
      }
    },
    // 自定义验证函数
    propF: {
      validator: function (value) {
        // 这个值必须匹配下列字符串中的一个
        return ['success', 'warning', 'danger'].indexOf(value) !== -1
      }
    }
  }
}
```
>- type 可以是下列原生构造函数中的一个：
>>- String
>>- Number
>>- Boolean
>>- Array
>>- Object
>>- Date
>>- Function
>>- Symbol
### 子组件传值到父组件中- 自定义事件
    父组件是使用 props 传递数据给子组件，但如果子组件要把数据传递回去，就需要使用自定义事件！
>- 子组件传值到父组件中需要自定义事件，让子组件来出发, 步骤
1. 在父组件中给子组件增加自定义事件，并绑定自定义事件触发的函数
```html
<!--updateLogoText: 自定义事件-->
<Vheader :logotext="handelLogoText" @updateLogoText="handelLogoTextFunc"></Vheader>
<script>
  import Vheader from "./components/Vheader"

  export default {
    name: "App",
    data() {
      return {
          // 主要为了让在子组件中修改此函数
        handelLogoText: 'VueDemo'
      }
    },
    methods: {
        // 自定义事件触发的函数
      handelLogoTextFunc(newText){
        this.handelLogoText = newText;
      }
    },
    components: {
      Vheader
    }
  }
</script>
```
2. 在子组件中进行触发自定义事件
```httm
<p>{{logotext}}</p>
<!--此函数为了触发父组件自定义的函数-->
<button @click="updataLogoTextFunc">添加菜单</button>

<script>
  export default {
    name: "Vheader",
    data() {
      return {
      }
    },
    methods: {
      updataLogoTextFunc(){
        // 重头戏为了出发父组件自定义的事件，并传递新参数
        this.$emit('updateLogoText', 'NewVueLogoText')
      }
    },
    // props用来做数据验证， 看父组件传递过来的数据是否是指定的类型
    props: {
      logotext: String
    }
  }
</script>
```

# Vue路由-vue-router.js

>- Vue Router 是 Vue.js 官方的路由管理器。它和 Vue.js 的核心深度集成，让构建单页面应用变得易如反掌。包含的功能有：
>>- 嵌套的路由/视图表
>>- 模块化的、基于组件的路由配置
>>- 路由参数、查询、通配符
>>- 基于 Vue.js 过渡系统的视图过渡效果
>>- 细粒度的导航控制
>>- 带有自动激活的 CSS class 的链接
>>- HTML5 历史模式或 hash 模式，在 IE9 中自动降级
>>- 自定义的滚动条行为
## 安装
    npm install vue-router --save
## vue-router 使用步骤
1. 先导入让Vue使能vue-router
```html
import Vue from 'vue'
import VueRouter from 'vue-router'
Vue.use(VueRouter)
```
2. 创建router实例并绑定到组件
```html
import Vamin from './components/Vamin'
import Vmaekdown from './components/Vmarkdown'

const router = new VueRouter({
  routes: [
    {
        path:'/', 
        name:'index'
        component: Vamin
    },
    {
        path:'/mark', 
        component: Vmaekdown
    },
  ]
})
```
3. 挂载路由实例到Vue实例中，从而让整个应用拥有路由功能
```html
new Vue({
  el: '#app',
  router,  
  render: h => h(App)
});
```
4. 在模板中使用
```html
<div id="app">
  <h1>Hello App!</h1>
  <p>
    <!-- 使用 router-link 组件来导航. -->
    <!-- 通过传入 `to` 属性指定链接. -->
    <!-- <router-link> 默认会被渲染成一个 `<a>` 标签 -->
    <!--<router-link> 对应的路由匹配成功，将自动设置 class 属性值 .router-link-active-->
    <router-link to="/">Go to Foo</router-link>
    <router-link to="/maek">Go to Bar</router-link>
  </p>
  <!-- 路由出口 -->
  <!-- 路由匹配到的组件将渲染在这里 -->
  <router-view></router-view>
</div>
```
5. 实例-刷新页面保持当前菜单的激活状态
```

<template>
  <ul class="nav navbar-nav">
    <li v-for="(item, index) in routes" :class="{active:currindex === index}" @click="reloadRouterCurrindex(index)">
      <router-link :to="item.url">{{item.title}}</router-link>
    </li>
  </ul>
</template>

<script>
  export default {
    name: 'Vheader',
    data() {
      return {
        // 所有菜单
        routes: [
          {url: '/', title: '我的首页'},
          {url: '/mark', title: '我的笔记'}
        ],
        // 标识当前菜单
        currindex: 0
      }
    },
    methods: {
      // 切换菜单的事件
      reloadRouterCurrindex(index){
        this.currindex = index
      }
    },
    // 刷新后保持路由状态
    created(){
      for(var i = 0; i < this.routes.length; i++){
        if (this.routes[i].url === this.$route.path){
          this.currindex = i;
          return
        }

      }
    }
  }
</script>

<style>

</style>

```
6. 路由懒加载
```
// import Vamin from './components/Vamin'
// import Vmaekdown from './components/Vmarkdown'

const router = new VueRouter({
  routes: [
    {
        path:'/', 
        name:'index'
        // 路由懒加载，只有用到组件的时候才会加载
        // /*webpackChunkName:'index'*/ webpack标识， 在打包的时候会打包成(index.[hash].js)
        component: () => import(/*webpackChunkName:'index'*/ './components/Vamin')
    },
    {
    path:'/mark', 
    // 路由懒加载，只有用到组件的时候才会加载
    component: () => import('./components/Vmarkdown')
    },
  ]
})
```
## 动态路由
1. 定义动态路由
```html
{
  path: '/article/:articleID',
  name: 'article',
  component: () => import(/*webpackChunkName:'index'*/ '@/components/showArticle')
}
```
2. 动态路由的跳转
```html
<router-link to="/article/1">最新文章1</router-link>
<router-link :to="{ name: 'article', params:{articleID: '2'}}">最新文章2</router-link>
```
3. 获取动态路由的参数
```html
<template>
  <h1>
    {{$route.params.articleID}}
  </h1>
</template>
```
## 嵌套路由
1. 定义嵌套路由
```html
{
  path: '/user',
  name: 'UserInfo',
  component: () => import(/*webpackChunkName:'sserindex'*/  '@/components/User/UserIndex'),
  children: [
    {
      path: 'permit',
      name: 'UserPermit',
      component: () => import(/*webpackChunkName:'sserpermit'*/  '@/components/User/UserPermit')
    }
  ]
}
```
2. 嵌套路由跳转
```html
<template>
  <div class="app">
    <router-link to="/user/permit">查看权限</router-link>
    <router-view/>
  </div>
</template>
```
## 重定向路由
```html
    {
      path: '/to_index',
      // 直接重定向只路径
      // redirect: '/'
      // 重定向到 路由别名对应的页面
      // redirect: {
      //   name: 'Vmain'
      // }
      redirect: to => {
        console.log(to);
        // return '/'
        return {
          name: 'Vmain'
        }
      }
    }
```
## 命名视图
## JS控制路由
>- 返回上一页
```
this.$router.back()
this.$router.go(-1)
```
>- 跳转到指定页面
```
this.$router.push('/login?userid=2')
this.$router.push(
                    {
                        name:'login'，
                        query: {
                            userid: 2
                        }
                    }
                    )
```
>- 替换的方式跳转到指定路由
```
this.$router.replace('/login')
this.$router.replace({name:'login'})
```
## 路由组件传参
### 路由动态参数传参
1. 定义动态路由
```
{
      path: '/article/:articleID',
      name: 'article',
      component: () => import(/*webpackChunkName:'showarticle'*/ '@/components/showArticle'),
      props:true //路由的参数作为组件的属性
}
```
2. 组件中接受路由传递的参数
```
<script>
  export default {
    name:'showArticle',
    data(){
      return{
      }
    }，
    props:{
      articleID: {
        type: Number,
        default: 1
      }
    }
  }
</script>
```
2. 组件中定义参数

## 路由导航守卫
    vue-router 提供的导航守卫主要用来通过跳转或取消的方式守卫导航。有多种机会植入路由导航过程中：全局的, 单个路由独享的, 或者组件级的。
### 全局守卫
    router.beforeEach 注册一个全局前置守卫
>- 例子： 拦截路由判断用户是否登陆
```
const LOGIN_PAGE_NAME = 'Login';
router.beforeEach((to, from, next) => {
  const token = getToken();
  if (!token && to.name !== LOGIN_PAGE_NAME) {
    console.log(to);
    console.log(from);
    // 未登录且要跳转的页面不是登录页
    next({
      name: LOGIN_PAGE_NAME, // 跳转到登录页
      query:{
        'next': to.fullPath
      }
    })
  } else if (!token && to.name === LOGIN_PAGE_NAME) {
    // 未登陆且要跳转的页面是登录页
    next() // 跳转
  } else if (token && to.name === LOGIN_PAGE_NAME) {
    // 已登录且要跳转的页面是登录页
    next({
      name: 'Home', // 跳转到homeName页

    })
  } else {
   // 如果登陆了，跳转到用户之前过来的页面
    next()
  }
});
```
    
# Vuex
    Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。
    Vuex 相当于所有组件的数据中转站， Vuex链接所有组件进行数据共享
    1、vuex就是在vue中创建全局变量的东西
    2、vuex提供了一些优雅的方法，可以让我们改变全局变量的值
![](https://vuex.vuejs.org/vuex.png)
## Vuex使用流程
>- 安装
>>- npm install vuex --save

>- 创建Vuex实例
```javascript
import Vue from 'vue'
Vue.use(Vuex);
const store = new Vuex.Vuex.Store(
    {
        state:{
            // statey用来保存状态
            appName:'Markdown'
        },
        mutations:{},
        actions:{},
        modules:{
            user:{
                state:{
                    username: '小杰'
                },
                mutations:{},
                actions:{}
            }
        }    

    }
)
```
>- 在组建中调用
```
<script>
  // 导入vuex
  import { mapState} from 'vuex'
  export default {
    name: 'Vheader',
    data() {
      return {
      }
    },
    computed: {
      ...mapState({
        appName: state => state.appName,
        userName: state => state.user.username
      }),
      
      // 下列写法效果与 mapState 效果相同
      // appName() {
      //   return this.$store.state.appName;
      // },
      // username() {
      //   return this.$store.state.user.username;
      // }
    }
  }
</script>
```


## state
       Vuex 使用单一状态树——是的，用一个对象就包含了全部的应用层级状态。至此它便作为一个“唯一数据源 (SSOT)”而存在。这也意味着，每个应用将仅仅包含一个 store 实例。单一状态树让我们能够直接地定位任一特定的状态片段，在调试的过程中也能轻易地取得整个当前应用状态的快照。
>- 定义state中的数据
```
state:{
    // statey用来保存状态
    appName:'Markdown'
},

```
>- 在组建中获取state中的数据
```javascript
export default {
    name: 'Vheader',
    data() {
      return {
      }
    },
    computed: {
      appName() {
        return this.$store.state.appName;
      },
    }
  }
```
## Getter
    有时候我们需要从 store 中的 state 中派生出一些状态，此时使用getter，相当于组件中的计算属性（computed）
>- 创建getter,
```
// 创建getter，例如反悔app的名称和版本号
const store = new Vuex.Store({
  state: {
    appName: 'Markdown',
    appVersion: 'v1.0'
  },
  // 创建getters
  getters: {
    AppVersionName (state) => {
        return '${state.apppName}-${state.appVersion}'
    }
  },
  modules:{
    user:{
    state: {
        firstname: '小',
        lasrname: '杰'
      },
    // 模块中创建getters
    getters: {
        username (state) => {
            return state.firstname + state.lastname;
        }
    }
  }
})
```
>>- 在组建中使用getter
```
import {mapGetters} from 'vuex'
export default {
  name: 'Vheader',
  data() {
    return {
    }
  },
  computed: {
    //调用getters中的数据
    // appVersion() {
    //   return this.$store.getters.appVersion;
    // },
    ...mapGetters({
      appVersion: getters.appVersion
    }),
    username(){
        return this.$store.user.getters.username;
    }
  }
}
```
## mutations
    用来定义事件在组建中触发从而改变state，更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。Vuex 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数
>- 定义mutations中的事件来操作state中的状态
```
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state, n) {
      // 变更状态
      state.count += n
    }
  }
})
```
>- 在组建中触发mutations
```
import { mapMutations } from 'vuex'
  export default {
    name: 'HelloWorld',
    data() {return {}},
    methods: {
       // 触发方式1
      increment(){
        this.$store.commit('increment'， 12)
      }
      
      // 触发方式2
      increment(){
        this.$store.commit({
            type:'increment',
            count: 12 // 此时mutations赋值得时候应该是: state.count += parames.count
        })
       
      // 触发方式3
       ...mapMutations([
          'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`
    
          // `mapMutations` 也支持载荷：
          'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
        ]),
        ...mapMutations({
          add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
        })
      }
    }
  }
```






# Vue-cli工具的使用
>- 安装
>>- npm install -g vue-cli
>- 查看可用模板
>>- vue list
```
Available official templates:
★  browserify - A full-featured Browserify + vueify setup with hot-reload, linting & unit testing.
★  browserify-simple - A simple Browserify + vueify setup for quick prototyping.
★  pwa - PWA template for vue-cli based on the webpack template
★  simple - The simplest possible Vue setup in a single HTML file
★  webpack - A full-featured Webpack + vue-loader setup with hot reload, linting, testing & css extraction.
★  webpack-simple - A simple Webpack + vue-loader setup for quick prototyping.
  ```
>- 创建模板
>>- vue init "templates名" "包名"

>- 全局使用jQuery
>>- webpack.base.conf.js
```
//新增
var webpack = require("webpack")

plugins: [
new webpack.ProvidePlugin({
  // 将jquery及其别名注册到全局
  $: "jquery",
  jQuery: "jquery",
  'window.jQuery': 'jquery',
  // 将Popper及其别名注册到全局
  Popper: ['popper.js', 'default']
})
],
```

 





