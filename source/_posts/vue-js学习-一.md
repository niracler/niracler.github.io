---
title: vue.js学习(一)
date: 2019-03-25 20:02:14
tags:
  - vue
---


# vue-exercise
不定期的vue学习动态


第一天
----

### hello world

关于一个[最小的vue项目的结构](day01/01.html)

1. 导入vue

2. 创建vue实例

3. 使用插值表达式调用数据

<!-- more -->

### vue基础及事件修饰符

1. 使用v-cloak可以解决闪烁问题，或者使用v-text也可以

```vue
<p v-cloak> {{ msg }} </p>
<h4 v-text='msg'></h4>        
```

2. 如何定义一个基本的Vue代码结构

2. 插值表达式 和 v-text 是用来数据的绑定的

4. v-html

5. v-bind  Vue提供的属性绑定机制  缩写是:

6. v-on    Vue提供的事件绑定机制  缩写是@

### 事件修饰符

[代码范例](day01/04.事件修饰符.html)

1. .stop 阻止冒泡

2. .prevent 阻止默认事件

3. .capture 添加事件侦听器时使用事件捕获模式

4. .self 只当事件在该元素本身(比如不是子元素)触发式触发回调

5. .once 事件只触发一次

### 跑马灯

[代码范例](day01/03.跑马灯.html)

步骤:

1. 弄一个时间
2. 截取字符串
3. 定时循环截取


### v-model

[代码范例](day01/05.v-model.html)

1. v-bind 只能实现数据的单向绑定,而v-model能够实现数据双向绑定

2. v-model只能运用在表单元素中

### 简易计算器

[代码范例](day01/05.v-model.html)

1. HTML代码结构  

```html
<input type="text" v-model="n1">

<select v-model="opt">
    <option value="+">+</option>
    <option value="-">-</option>
    <option value="*">*</option>
    <option value="/">/</option>
</select>

<input type="text" v-model="n2">
<input type="button" value="=" @click="calc">
<input type="text" v-model="result">
```

2. vue实例结构

```vue
var vm = new Vue({
    el: "#app",
    data: {
        n1:0,
        n2:0,
        result:0,
        opt: "-",
    },
    methods: {
        calc() {
            switch (this.opt) {
                case '+':
                    this.result = parseInt(this.n1) + parseInt(this.n2);
                    break;
                case '-':
                    this.result = parseInt(this.n1) - parseInt(this.n2);
                    break;
                case '*':
                    this.result = parseInt(this.n1) * parseInt(this.n2);
                    break;
                case '/':
                    this.result = parseInt(this.n1) /  parseInt(this.n2);
                    break;
            }
        }
    },
})
```

### vue里面使用样式

1. 通过属性绑定为元素设置class类样式

2. 通过通过属性绑定为元素绑定style行内样式

### vue指令v-for

1. v-for循环普通数组

```vue
<div id="app">

    <p v-for="(item , i) in list">索引值: {{i}} ,  内容: {{item}}</p>
</div>

<script>
    var vm = new Vue({
        el: "#app",
        data: {
            list:[1,2,3,4,5,6]
        },
        methods: {}
    })

</script>
```

2. v-for循环对象数组
```vue
<div id="app">
    <p v-for="(user , i) in list">Id: {{ user.id }}, 姓名: {{ user.name }}</p>
</div>

<script>
    var vm = new Vue({
        el: "#app",
        data: {
            list:[
                { id: 1, name: 'ls1' },
                { id: 2, name: 'ls2' },
                { id: 3, name: 'ls3' },
                { id: 4, name: 'ls4' },
                { id: 5, name: 'ls5' },
            ]
        },
        methods: {}
    })
</script>
```

3. v-for循环对象
```vue
<div id="app">
    <p v-for="(val, key, i) in user">值是: {{ val }}, 键是: {{ key }}, 索引是: {{i}}</p>
</div>

<script>
    var vm = new Vue({
        el: "#app",
        data: {
            user: {
                id: 5,
                name: "hui",
                gender: "male",
            }
        },
        methods: {}
    })
</script>
```

4. v-for迭代数字

注意:是从1开始的

```vue
<div id="app">
    <p v-for="count in 10000">我们来迭代数字吧: {{ count }}</p>
</div>
```

### vue指令之v-for和v-show

一般来说,v-if有更高的切换消耗而v-show有更高的初始渲染消耗.因此,如果需要频繁切换,应使用v-show, 而运行时不大可能改变的,用v-if更好



参考内容
---

-  [最全的vue.js视频【黑马程序员】](https://www.bilibili.com/video/av36650577/?p=1)
-  [bootstrap-panel-not-working](https://stackoverflow.com/questions/46429727/bootstrap-panel-not-working/46429803)
