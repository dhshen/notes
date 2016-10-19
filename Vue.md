#Vue.js
## 概述##
Vue.js是一个构建数据驱动的web界面的库。目的是通过尽可能简单的API实现响应的数据绑定和组合的视图组件。
Vue不是一个全能的框架，它只聚焦于视图层。
** 指令 **：带有前缀`v-`

##Vue实例##
每个Vue.js应用的起步都是通过构造函数Vue创建一个Vue的根实例：
```
var vm = new Vue({
	//选项
});
```
***
### MVVM模式 ###
MVVM即Model-View-View Model。这个模式提供对View和View Model的双向数据绑定。这使得View Model的状态改变可以自动传递给View。典型的情况是，View Model通过使用observer模式(观察者模式)来将View Model的变化通知给model。

1.Model
Model层代表了描述业务逻辑和数据的一系列类的集合。它也定义了数据修改和操作的业务规则。
2.View
View代表了UI组件，像CSS，jQuery，Html等。它只负责展示从Presenter接收到的数据。也就是把模型转化成UI。
3.View Model
负责暴露方法，命令，其他属性来操作View的状态，组装Model作为View动作的结果，并且触发View自己的事件。
***
一个Vue实例其实就是一个MVVM模式中所描述的ViewModel。
在实例化 Vue 时，需要传入一个选项对象，它可以包含数据、模板、挂载元素、方法、生命周期钩子等选项。
### 属性与方法
每个Vue实例都会代理其`data`对象里所有的属性。
注意：只有这些被代理的属性是响应的。如果在实例创建之后添加新的属性到实例上，它不会触发视图更新。

除了这些数据属性，Vue实例暴露了一些有用的实例属性和方法。这些属性与方法都有前缀`$`，以便与代理的数据属性区分。
```
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})

vm.$data === data // -> true
vm.$el === document.getElementById('example') // -> true

// $watch 是一个实例方法
vm.$watch('a', function (newVal, oldVal) {
  // 这个回调将在 `vm.a`  改变后调用
})
```

## 数据绑定语法 ##
Vue.js的模板是基于DOM实现的。这意味着所有的Vue.js模板都是可解析的有效的HTML。

### 插值 ###
* 文本
数据绑定最基础的形式是文本插值，使用'Mustache'语法(双大括号)：
```
<span>Message: {{ msg }}</span>
```
Mustache 标签会被相应数据对象的 msg 属性的值替换。每当这个属性变化时它也会更新。
**你也可以只处理单次插值，今后的数据变化就不会再引起插值更新了：**
```
<span>This will never change: {{* msg }}</span>
```

* 原始的HTML
双 Mustache 标签将数据解析为纯文本，三 Mustache 标签则将内容以HTML字符串插入，数据绑定将被忽略。

* HTML特性
Mustache 标签也可以用在 HTML 特性 (Attributes) 内：
```
<div id="item-{{ id }}"></div>
```
注意在 Vue.js 指令和特殊特性内不能用插值

### 绑定表达式 ###
* JavaScript表达式
Vue在数据绑定内支持全功能的JavaScript表达式:

	```
	{{ number + 1 }}
	{{ ok ? 'YES' : 'NO' }}
	{{ message.split('').reverse().join('') }}
	```
这些表达式将在所属的 Vue 实例的作用域内计算。一个限制是每个绑定只能包含单个表达式。

* 过滤器
Vue.js允许在表达式后添加可选的"过滤器(Filter)"，以"管道符"指示：
```
{{ message | capitalize }}
```
这里我们将表达式 message 的值“管输（pipe）”到内置的 capitalize 过滤器，这个过滤器其实只是一个 JavaScript 函数，返回大写化的值。

	过滤器可以串联也可以接收参数：
	```
	{{ message | filterA | filterB }}
	{{ message | filterA 'arg1' arg2 }}
	过滤器函数始终以表达式的值作为第一个参数。带引号的参数视为字符串，而不带引号的参数按表达式计算。这里，字符串 'arg1' 将传给过滤器作为第二个参数，表达式 arg2 的值在计算出来之后作为第三个参数。
	```

### 指令 ###
指令 (Directives) 是特殊的带有前缀 v- 的特性。指令的值限定为绑定表达式，因此上面提到的 JavaScript 表达式及过滤器规则在这里也适用。指令的职责就是当其表达式的值改变时把某些特殊的行为应用到 DOM 上。
* 参数
有些指令可以在其名称后面带一个“参数” (Argument)，中间放一个冒号隔开
```
<a v-bind:href="url"></a>
```
这里 href 是参数，它告诉 v-bind 指令将元素的 href 特性跟表达式 url 的值绑定。可能你已注意到可以用特性插值 href="{{url}}" 获得同样的结果：这样没错，并且实际上在内部特性插值会转为 v-bind 绑定。

	另一个例子是`v-on`指令，它用于监听DOM事件：
	```
	<a v-on:click="doSomething">
	```
	这里参数是被监听的事件的名字`click`。

* 缩写
Vue.js 为两个最常用的指令 v-bind 和 v-on 提供特别的缩写：
*v-bind缩写
	```
	<!-- 完整语法 -->
	<a v-bind:href="url"></a>
	
	<!-- 缩写 -->
	<a :href="url"></a>
	```
* v-on缩写
	```
	<!-- 完整语法 -->
	<a v-on:click="doSomething"></a>
	
	<!-- 缩写 -->
	<a @click="doSomething"></a>
	```

## 计算属性 ##
模板是为了描述视图的结构。在模板中放入太多的逻辑会让模板过重且难以维护。这就是为什么 Vue.js 将绑定表达式限制为一个表达式。如果需要多于一个表达式的逻辑，应当使用计算属性。

	```
	基础例子：
	<div id="example">
	  a={{ a }}, b={{ b }}
	</div>
	
	var vm = new Vue({
	  el: '#example',
	  data: {
	    a: 1
	  },
	  computed: {
	    // 一个计算属性的 getter
	    b: function () {
	      // `this` 指向 vm 实例
	      return this.a + 1
	    }
	  }
	})
	```

这里我们声明了一个计算属性 b。
**我们提供的函数将用作属性 vm.b的 getter。**
Vue知道vm.b依赖于vm.a，因此当vm.a发生改变时，依赖于vm.b的绑定也会更新。

* 计算属性 VS  $.watch
Vue.js 提供了一个方法 $watch，它用于观察 Vue 实例上的数据变动。
	```
	<div id="demo">{{fullName}}</div>

	var vm = new Vue({
	  el: '#demo',
	  data: {
	    firstName: 'Foo'
	  }
	})
	
	vm.$watch('firstName', function (val) {
	  this.fullName = val + ' ' + this.lastName
	})
	```
不过通常更好的办法是使用计算属性而不是一个命令式的$watch回调。

* 计算setter
计算属性默认只是 getter，不过在需要时你也可以提供一个 setter：
	```
	// ...
	computed: {
	  fullName: {
	    // getter
	    get: function () {
	      return this.firstName + ' ' + this.lastName
	    },
	    // setter
	    set: function (newValue) {
	      var names = newValue.split(' ')
	      this.firstName = names[0]
	      this.lastName = names[names.length - 1]
	    }
	  }
	}
	// ...
	```
现在在调用 vm.fullName = 'John Doe' 时，setter 会被调用，vm.firstName 和 vm.lastName 也会有相应更新。


## Class与Style绑定 ##
数据绑定一个常见需求是操作元素的 class 列表和它的内联样式。因为它们都是 attribute，我们可以用 v-bind 处理它们：只需要计算出表达式最终的字符串。不过，字符串拼接麻烦又易错。因此，在 v-bind 用于 class 和 style 时，Vue.js 专门增强了它。表达式的结果类型除了字符串之外，还可以是对象或数组。

**注意：**`v-bind:class`指令可与普通的class特性共存。最终会被合并到一个class属性里。

* 对象的语法
	我们可以传给 v-bind:class 一个对象，以动态地切换 class。
	```
	div class="static" v-bind:class="{ 'class-a': isA, 'class-b': isB }"></div>
	```
	```
	data:{
		isA: true,
		isB: false
	}
	```
	最终渲染为`<div class="static class-a"></div>`
	当 isA 和 isB 变化时，class 列表将相应地更新

	也可以直接绑定数据里的一个对象：
	```
	<div v-bind:class="classObject"></div>
	```
	```
	data: {
	  classObject: {
	    'class-a': true,
	    'class-b': false
	  }
	}
	```
	也可以在这里绑定一个返回对象的计算属性

* 数组语法
可以把一个数组传给 v-bind:class，以应用一个 class 列表
	```
	<div v-bind:class="[classA, classB]">
	```
	
	```
	data: {
	  classA: 'class-a',
	  classB: 'class-b'
	}
	```
	渲染为：`<div class="class-a class-b"></div>`

### 绑定内联样式 ###
v-bind:style 的对象语法十分直观——看着非常像 CSS，其实它是一个 JavaScript 对象。CSS 属性名可以用驼峰式（camelCase）或短横分隔命名（kebab-case）：
```
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
data: {
  activeColor: 'red',
  fontSize: 30
}

直接绑定到一个样式对象通常更好，让模板更清晰；
<div v-bind:style="styleObject"></div>
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```
数组语法可以将多个样式对象应用到一个元素上；
当 v-bind:style 使用需要厂商前缀的 CSS 属性时，如 transform，Vue.js 会自动侦测并添加相应的前缀。

## 条件渲染 ##
`v-if`
`template v-if`
`v-show`
`v-else`
v-show 的元素会始终渲染并保持在 DOM 中。v-show 是简单的切换元素的 CSS 属性 display。注意 v-show 不支持 <template> 语法。

## 列表渲染 ##
`v-for`
可以使用 v-for 指令基于一个数组渲染一个列表。这个指令使用特殊的语法，形式为 `item in items`，items 是数据数组，item 是当前数组元素的别名。
在 v-for 块内我们能完全访问父组件作用域内的属性，另有一个特殊变量 `$index`，正如你猜到的，它是当前数组元素的索引。

也可以将`v-for`用在`<template>`标签上,以渲染一个包含多个元素的块。

Vue.js包装了被视察数组的变异方法，故它们能触发视图更新。被包装的方法有：
```
push()
pop()
shift()
unshift()
splice()
sort()
reverse()
```
问题，因为JavaScript的限制，Vue.js不能检测到下面数组的变化：
1.直接用索引设置元素，如 `vm.item[0] = {}`
2.修改数据的长度，如 `vm.items.length = 0`

### 对象v-for
也可以使用v-for遍历对象，除了`$index`之外，作用域内还可以访问另一个特殊的变量：`$key`

### 值域v-for
`v-for`也可以接收一个整数，此时它将重复模板数次。
```
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
结果： 0 1 2 3 4 5 6 7 8 9
```

## 方法与事件处理器
可以用v-on指令监听DOM事件

### 方法处理器
```
<div id="example">
  <button v-on:click="greet">Greet</button>
</div>
```
```
var vm = new Vue({
  el: '#example',
  data: {
    name: 'Vue.js'
  },
  // 在 `methods` 对象中定义方法
  methods: {
    greet: function (event) {
      // 方法内 `this` 指向 vm
      alert('Hello ' + this.name + '!')
      // `event` 是原生 DOM 事件
      alert(event.target.tagName)
    }
  }
})

// 也可以在 JavaScript 代码中调用方法
vm.greet() // -> 'Hello Vue.js!'
```

### 内联语句处理器
```
<div id="example-2">
  <button v-on:click="say('hi')">Say Hi</button>
  <button v-on:click="say('what')">Say What</button>
</div>
```
```
new Vue({
  el: '#example-2',
  methods: {
    say: function (msg) {
      alert(msg)
    }
  }
})
```
类似于内联表达式，事件处理器限制为一个语句。

### 事件修饰符
在事件处理器中经常需要调用 event.preventDefault() 或 event.stopPropagation()。
Vue.js 为 v-on 提供两个 事件修饰符：.prevent 与 .stop。

### 按键修饰符
```
<!-- 只有在 keyCode 是 13 时调用 vm.submit() -->
<input v-on:keyup.13="submit">
```

Vue为按键提供别名：
```
<input v-on:keyup.enter="submit">
```
全部按键别名：
```
enter
tab
delete
esc
space
up
down
left
right
```

## 表单控件绑定
可以用 v-model 指令在表单控件元素上创建双向数据绑定。





export PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin


### template v-if##
我们可以把一个 <template> 元素当做包装元素，并在上面使用 v-if，最终的渲染结果不会包含它。
```
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```


## 使用笔记##
* 在数据完成加载，页面还没有编译之前如何隐藏 Mustache标签`{{ mustache }}`
	使用** v-cloak ** 指令，这个指令保持在元素上直到关联实例结束编译。
	和css规则 `[v-cloak]{display:none;}`配合使用，可以隐藏未编译的 Mustache 标签直到实例准备完毕。
