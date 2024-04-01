# 一、Vue基础

##  1.Vue的基本原理

Vue.js 是一个构建用户界面的渐进式框架，特别是用来创建单页应用（SPA）时。Vue 被设计为能够自下而上逐层应用，Vue 的核心库只关注视图层。以下是 Vue 的一些基本原理和特点：

1.响应式系统:

- Vue使用响应式系统来确保视图的更新能够及时反映出模型的状态。当Vue实例的数据对象的属性发生变化时，视图会自动更新
- 这是通过使用Object.defineProperty()(vue3.0使用proxy)方法实现的，Vue会递归地变力所有对象的属性并将它们转换成getters/setters，以跟踪变化

2.组件系统
- Vue应用由多个组件树组成，每个组件实例都有自己的数据和方法，并且可以有自己的视图和子组件
- 组件可以重用，它们提供了一种抽象，允许我们构建大型应用中包含的小块独立和可复用的自定义元素

3.虚拟DOM
- Vue使用虚拟DOM(V-DOM)，这是一个编程概念，在其中UI的一个理想化或者"虚拟"的表示在内存中以Javascript数据结构的形式被保存并同步到"真实"的DOM中
- 当状态变化时，Vue会生成一个新的虚拟DOM树并将其与旧树进行比较。算法会找出实际DOM需要变动的最小差异，然后进行必要的DOM更新，从而提高性能

4.声明式渲染
- Vue使用基于模板的声明式渲染语法，允许开发者以一种简洁的方式来声明式地将数据渲染进DOM系统

5.双向数据绑定
- Vue支持表单输入和应用状态之间的双向数据绑定，这通过v-model指令实现。它确保了input、select、textarea等表单元素的值可以与vue实例的数据保持同步

6.指令(Directives)
- Vue提供了一系列内置的指令，如v-bind，v-model，v-for，v-if,v-else，v-on等，方便开发者进行数据渲染、条件渲染、列表渲染、事件处理等

7.生命周期钩子
- Vue实例有一系列的生命周期钩子(如created，mounted，updated，destroed等)，允许用户在某个特定的时点进行自定义的逻辑处理


##  2.双向数据绑定的原理
双向数据绑定是一个常见的应用程序模式，它使得用户界面(UI)中的数据和后端数据模型之间的同步变得自动化。当数据改变时，UI自动更新；同时当UI改变（例如，用户输入）时，数据模型也会自动更新。

在Vue.js这样的前端框架中，双向数据绑定的工作原理通常涉及以下几个方面：

1.响应式数据对象：
- Vue.js 使用一个响应式系统来监测数据对象的属性变化。每个组件实例中的data对象中的属性都被转换成了getter和setter。
- Vue内部使用了依赖跟踪系统，它会在组件渲染期间自动跟踪哪些属性被访问，从而知道当这些属性变化时需要重新渲染。

2.数据代理：
- Vue组件的实例代理了data对象中的所有属性，这让开发者可以直接用this.propertyName访问这些属性，而不是this.data.propertyName。

3.观察者模式：
- 当数据变化时，所谓的观察者模式能让系统知道，并作出反应。在Vue中，响应式数据变化会触发渲染函数重新计算，并更新DOM。

4.指令：
- Vue的模板语法允许开发者使用指令（如 v-model）来创建数据绑定。v-model 在内部实现了自动数据绑定，即模型到视图和视图到模型的自动同步。
- 对于表单元素，v-model会监听用户的输入事件以更新数据，并在数据变化时更新表单元素的值。

实现双向数据绑定的核心是数据的响应式系统和绑定到DOM元素的事件监听器。在Vue中，这种机制是透明的，开发者只需要使用v-model这样的语法糖即可轻松实现表单元素和数据模型之间的双向绑定。这大大减少了需要编写的样板代码，让表单处理变得更加直观和容易。

##  3.使用 Object.defineProperty() 来进行数据劫持有什么缺点？

1.深度监听问题：Object.defineProperty()只能对属性进行劫持，因此需要递归遍历对象的每一个属性，如果对象层级很深，这个过程会很消耗性能

2.数组更新问题：Object.defineProperty()不能直接监听数组的变化，例如通过索引设置元素或改变数组长度等操作。Vue.js通过变异方法(push,pop,shift,unshift,splice,sort,reverse)来实现数组的响应式，但依然有局限性

3.添加和删除属性：使用Object.defineProperty()对已经创建的对象进行数据劫持时，如果后续有新的属性被添加到此对象，需要手动再调用Object.defineProperty()来监测新属性。同样的，它也不能劫持删除属性的操作

4.性能开销：劫持大量的属性会创建很多getter和setter，这本身会有一个性能开销，并且每次访问或修改属性都会通过这些getter和setter，带来额外的计算成本

5.兼容性问题：IE8及一下版本的浏览器不支持Object.defineProperty()，因此再不支持此API的环境中无法实现数据劫持

因为这些缺点，Vue3替换了数据劫持的实现方式，转而使用ES6的Proxy来代替Object.defineProperty()。Proxy可以监听整个对象而不是对象的属性，也能够监听数组索引和长度的变化，同时也能够解决添加和删除属性的问题，而且有更好的性能和兼容性处理

##  4.Computed和Watch的区别
在Vue.js中，Computed和Watch都是响应式的方式来依赖于数据变化执行逻辑，但它们的使用场景和行为方式有所不同

Computed(计算属性)

- 计算结果缓存：Computed属性是基于它们的响应式依赖进行缓存的。只有在它的相关依赖发生改变时才会重新计算。如果依赖没有变化，多次访问计算属性会立即返回之前的计算结果，而无需再次执行函数
- 声明式逻辑：Computed属性通常用于声明式地描述一个数据依赖于其他数据时如何变化的情况，它是基于现有数据派生出一些状态
- 无副作用：通常，Computed属性应该用于计算值而不是执行副作用操作，例如更改数据或执行异步操作
例子：
```
var vm = new Vue({
  data: { a: 1 },
  computed: {
    // 一个计算属性的 getter
    b: function() {
      // `this` 指向 vm 实例
      return this.a + 1;
    }
  }
});
```

Watch(侦听器)
- 不缓存：Watch用于执行任何数据变动时的响应式操作，当所指定监听的数据变化时会执行回调函数
- 命令式逻辑：它更适合执行与数据变化相关的异步或昂贵的操作
- 副作用：Watch允许执行副作用操作，你可以在侦听器的函数内部执行任何代码，例如：发起网络请求、更改其他数据属性等

例子
```
var vm = new Vue({
  data: { a: 1 },
  watch: {
    // 这个函数将在 `vm.a` 改变后调用
    a: function(newVal, oldVal) {
      console.log('new: %s, old: %s', newVal, oldVal);
    }
  }
});
```

使用场景：
- Computed：当你需要根据其他数据变化生成新数据时。比如一个属性是由其他几个属性决定的
- Watch：当你需要在数据变化后执行更多的业务逻辑时。比如一个数据变化需要发起Ajax请求获取新的数据更新视图


简单来说，Computed更适合用于计算同步操作，而Watch更适合应对数据变化的响应式操作，尤其是异步操作

##  5.Computed 和 Methods 的区别
主要区别：

1.Computed计算属性会缓存结果，只有在依赖数据发生变化时才会重新计算。而Methods中的方法则不会缓存结果，每次调用都会执行

2.Computed属性通常用于计算值，并且最好不包含副作用。Methods方法用于执行可能会有副作用的操作，例如修改数据或进行异步请求

3.Computed属性通常用在模板绑定中，当数据变化需要更新DOM时。Methods方法更多用于响应时间(如用户交互)

在实践中，如果需要一个基于现有数据计算得到的值，并且这个计算值在以来数据没变化时应当保持不变，那么应当使用Computed；如果有一个操作时需要多次猪东调用且每次都要执行函数的，则应该使用Methods

##  6. slot是什么？有什么作用？原理是什么？
在Vue.js中，slot(插槽)是一种组件模板的内容分发机制，它允许我们从父组件插入内容到子组件的指定位置。这使得子组件能够定义一个可插入内容的模板布局，而由父组件来决定最终的内容是什么

**作用**：
1. **内容复用和分发**：使用插槽可以创建一个包含预定义布局的"容器"组件，然后根据需要将不同的内容注入到组件的不同部分，这增加了组件的复用性和灵活性
2. **布局抽象**：插槽让组件的作者能够抽象出一个模板布局，而不需要关心使用该组件的开发者会放入什么内容
3. **结构清晰**：保持了父子组件间清晰的分离，使得组件结构更加清晰和利于理解

**原理**：

slot的运作原理基于Vue的编译系统，当Vue的模板被编译时，带有slot 的元素会作为分发内容保留下来。在子组件创建过程中，子组件的$slot对象会被填充上对应的内容，父组件传递的内容会被插入到子组件的DOM中响应slot的位置

**示例**：

子组件(ChildComponent.vue):
```
<div>
  <h2>我是子组件的标题</h2>
  <!-- 定义插槽 -->
  <slot name="content"></slot>
</div>
```

父组件：
```
<child-component>
  <!-- 提供插槽的内容 -->
  <template v-slot:content>
    <p>这段内容将会出现在子组件的插槽中。</p>
  </template>
</child-component>
```
在这个例子中，父组件提供了\<template v-slot:content>标签来指定要插入到子组件中名为content的slot上的内容。结果,\<p>这段...\</p>会出现在子组件的渲染结果里

总之，slot使得组件的设计更加灵活，可以轻松定义可复用且可由父组件部分定制内容的组件


##  7.过滤器的作用，如何实现一个过滤器
在Vue.js中，过滤器(Filters)用于对文本进行格式化处理。它可以在模板表达式中使用管道字符 | 后跟过滤器函数来应用。过滤器可以用在两个地方：双花括号插值和v-bind表达式(用于绑定属性)

**过滤器的作用**：

 1. **格式化文本**：最常见的用例是对文本格式化，例如日期格式化、货币格式化、文字转大写等
 2. **可复用性**：在许多组件之间共享常见的文本格式化库
 3. **易读性**：在模板中通过管道操作符进行链式调用，代码易于阅读和理解

**如何实现一个过滤器**：

定义过滤去可以通过全局方法Vue.filter()或者组件内的filters选项

1. **全局过滤器**：使用Vue.filter()创建的过滤器可在任何组件内使用
```
   // 定义一个全局过滤器 `capitalize`
   Vue.filter('capitalize', function (value) {
       if (!value) return '';
       value = value.toString();
       return value.charAt(0).toUpperCase() + value.slice(1);
   });

   // 使用过滤器
   <p>{{ message | capitalize }}</p>
```
在这个例子中，capitalize过滤器会将文本的第一个字符转换为大写

2. **局部过滤器**：在组件内定义过滤器只能在该组件的模板中使用。
```
   // 在组件内定义
   new Vue({
     // ...
     filters: {
       capitalize: function (value) {
         if (!value) return '';
         value = value.toString();
         return value.charAt(0).toUpperCase() + value.slice(1);
       }
     }
   });

   // 使用过滤器
   <p>{{ message | capitalize }}</p>
```
3. **链式调用**:可以在表达式中链式调用多个过滤器
```
   <p>{{ message | filterA | filterB }}</p>
```
先将message处理filterA的逻辑然后再经过filterB的处理

需要注意的是，从Vue.js.3.0开始，过滤器功能已经被移除。Vue团队推荐使用方法调用或计算属性来替代过滤器的功能。可以像下面这样实现：
```
// 在组件内定义一个方法
methods: {
  capitalize(value) {
    if (!value) return '';
    value = value.toString();
    return value.charAt(0).toUpperCase() + value.slice(1);
  }
}

// 在模板中使用这个方法
<p>{{ capitalize(message) }}</p>
```
或者用计算属性来实现：
```
computed: {
  capitalizedMessage() {
    if (!this.message) return '';
    const value = this.message.toString();
    return value.charAt(0).toUpperCase() + value.slice(1);
  }
}

// 在模板中使用计算属性
<p>{{ capitalizedMessage }}</p>
```

##  8.如何保存页面的当前的状态
保存页面的当前状态可以通过多种方法实现，具体取决于你希望保存哪些状态以及你希望在什么时候和如何恢复这些状态。以下是几种常用的状态保存技巧：

1. **使用Web Storage(localStorage或sessionStorage)**：

Web Storage API提供了在浏览器中存储键值对的方法。localStorage持久保存数据直到明确的删除，而sessionStorage在页面会话结束时删除(如关闭标签页)

```
   // 保存状态
   localStorage.setItem('myState', JSON.stringify(stateObject));

   // 读取状态
   const savedState = JSON.parse(localStorage.getItem('myState'));
```

2.**使用Cookies**：

Cookies可以用来在用户的浏览器上保存数据，在随后的请求中这些数据会被发送到服务器。它们经常被用于保存用户会话信息

```
   // 设置 cookie
   document.cookie = "pageState=" + JSON.stringify(stateObject) + ";path=/;max-age=3600";

   // 读取 cookie
   const value = `; ${document.cookie}`;
   const parts = value.split(`; pageState=`);
   if (parts.length === 2) {
       const savedState = JSON.parse(parts.pop().split(';').shift());
   }
   ```

3. **使用URL查询参数**：

如果要保存的状态可以简化为一个较短的字符串，你可以将状态信息保存在URL的查询参数中。

```
   // 保存状态
   window.history.pushState({}, '', '?page=2');

   // 读取状态
   const queryParams = new URLSearchParams(window.location.search);
   const page = queryParams.get('page'); // 假设 URL 是 "?page=2"
   ```

4. **使用浏览器的history state**：

HTML5增加了一些控制浏览器历史记录的功能，你可以使用history.pushState和history.replaceState在不重新加载页面的情况下更新地址栏

```
   // 保存状态
   history.pushState({ page: 1 }, 'title 1', '?page=1');

   // 恢复状态
   window.onpopstate = function(event) {
     console.log(event.state.page); // event.state 保存了之前传递给 pushState 的对象
   };
   ```

5. **使用Vue.js的数据持久化库**：

如果你使用Vue.js，可能会有一些库来帮助你更轻松地持久化你的数据，比如vuex-persistedstate,它可以帮助你将Vuex的状态保存到localStorage

```
   // 配置 Vuex store
   const store = new Vuex.Store({
     // Vuex state
     plugins: [createPersistedState({
       storage: window.localStorage, // 可以配置 sessionStorage
     })],
   });
```

种方法适用于不同的情景，你的选择取决于你的具体需求，例如你需要保存的状态大小、用户是否需要跨会话存储状态、是否有服务器端渲染等因素。对于客户端渲染的 SPA（单页面应用），使用 Web Storage 或 Vuex 持久化通常是较好的选择。对于传统的多页面应用（MPA），可能使用 Cookies 或 URL 查询参数更合适

##  9.常见的事件修饰符及其作用
在Vue.js中，事件修饰符是一些由点前缀的指令后缀，用于表示v-on指令应该以某种特别方式绑定事件监听器。事件修饰符可以实现对原生DOM事件行为的更细致控制

1. **.stop**-调用event.stopPropagation(),阻止事件冒泡

```
   <!-- 阻止冒泡 -->
   <div @click.stop="divClicked">
     Click me
   </div>
```

2. **.prevent**-调用event.preventDefault(),阻止事件默认行为

```
   <!-- 阻止默认行为 -->
   <form @submit.prevent="onSubmit">
     ...
   </form>
```

3. **.capture**-使用事件捕获模式，即内部元素触发的事件先在此被处理，再逐级向下传递

```
   <!-- 使用捕获模式 -->
   <div @click.capture="divClicked">
     ...
   </div>
```

4. **self**-只当在event.target是当前元素自身时触发处理函数

```
   <!-- 只有当点击的是div元素本身时触发 -->
   <div @click.self="divClicked">
     ...
   </div>
```

5. **.once**-事件将只会触发一次

```
   <!-- 只触发一次 -->
   <button @click.once="doThisOnce">
     Click me
   </button>
```

6. **.passive**-表示不会调用event.preventDefault()。在移动端可以用来提高滚动性能

```
   <!-- 滚动事件的监听器将是被动的 -->
   <div @scroll.passive="onScroll">
     ...
   </div>
```

7. **按键修饰符**-如.enter、.tab、.delete(捕获"删除"和"退格"键)、.esc、.space等，只有在按下特定键时才触发键盘事件

```
   <!-- 只有在按下回车时才触发 -->
   <input @keyup.enter="onEnter">
```

8. **系统修饰键**-使用如.ctrl、.alt、.shift、.meta(对应Command键或Windows键)修饰符，只有在按下特定系统修饰键时才触发事件

```
   <!-- Ctrl + Click -->
   <button @click.ctrl="onCtrlClick">
     Click me
   </button>
```

事件修饰符可以被链式使用，从而在同一个事件上实现多种行为的组合。

```
<!-- 阻止默认行为，停止冒泡，并只触发一次 -->
<button @click.prevent.stop.once="doThis">
  Click me
</button>
```

##  10. v-if、v-show、v-html 的原理
 v-if、v-show、v-html 是Vue.js提供的指令，分别用于条件渲染、切换元素的显示状态，以及输出真实的HTML内容

 **v-if**
 - **原理**：v-if指令用于条件性地渲染一块内容。这块内容只会在指令的表达式返回true时被渲染
 - **DOM级别操作**：当条件为false时，v-if控制的元素以及内部的所有内容不会被渲染，即它们不会出现在DOM中。相反，当条件变为true时，元素和其内容会被渲染到DOM中。
 - **适用场景**：v-if适用于条件不经常改变时。由于它在条件变化时进行真实的DOM创建和销毁，频繁的切换可能会导致性能开销更大

用法示例：
```
<div v-if="isVisible">This is visible only if `isVisible` is true.</div>
```

**v-show**
- **原理**：与v-if不同，v-show指令不管其表达式真假，元素总会被渲染到DOM中，v-show只是简单地切换元素的CSS属性display
- **CSS级别操作**：如果表达式返回false，v-show会给元素添加display：none样式，使其不可见。当条件变为true时，移除这个样式，元素重新可见
- **适用场景**：适用于需要频繁切换显示状态的情况，因为CSS属性的切换比DOM元素的创建和销毁开销要小

用法示例：
```
<div v-show="isVisible">This element's display is toggled by the `isVisible` state.</div>
```

**v-html**
- **原理**：v-html指令用于更新元素的innerHTML。你可以用它来动态渲染HTML内容
- **直接操作innerHTML**：v-html会根据表达式的值，将元素的innerHTML设置为相同的HTML内容。注意，这可能会导致跨站脚本攻击(XSS)。因此应该只用于信任内容的渲染
- **不是绑定到数据的更新**:v-html不会将HTML字符串里的Vue模板编译为Vue组件。它仅仅是将HTML当作普通的HTML代码插入到元素中

用法示例：
```
<div v-html="rawHtml"></div>
```

在这个例子中，rawHtml 属性包含的HTML内容将直接渲染到 div 的内部，覆盖原有内容。


##  11.v-if和v-show的区别

v-if 和 v-show 都是 Vue.js 中用于控制元素显示的指令，但它们具有很大的区别，主要体现在如何渲染和条件改变时的处理上：

**v-if**

1. **条件渲染**：v-if是条件性的渲染，只有当表达式为true时，元素才会被渲染到DOM中
2. **DOM元素的创建和销毁**：如果v-if的条件从false变为true，Vue将创造新的元素并渲染；如果条件从true变为false，Vue将销毁元素及其所有的事件监听器和子组件，释放内存空间
3. **惰性加载**：如果在渲染时条件为false，那么任何包含在v-if块内的指令(比如子组件)都不会被执行，这种情况下有助于节省性能
4. **适用于运行条件不常变化的情况**：由于它会进行深入的DOM操作，所以频繁地切换v-if的条件可能会导致性能损耗

**v-show**
1. **始终被渲染**：和v-if不同，v-show控制的元素无论条件真假，总是会被渲染到DOM中，只是简单地切换元素的CSS属性display
2. **元素始终保留在DOM中**：当v-show的表达式返回false时，元素不会显示(display:none)，但依旧存在于DOM树里
3. **频繁切换显示**：适合条件经常改变的场景，因为它只涉及到CSS的变更，没有重的DOM操作开销

**适用场景比较**
1. **v-if**：
   - 当元素条件不经常变更时
   - 当初始渲染条件为false，可以延迟内容的加载，适合于初始隐藏的大量DOM节点以节省性能
2. **v-show**：
   - 当需要频繁切换某个元素的显示状态时
   - 即使在初始条件下不显示，元素也是种在DOM中。这样切换显示时不会重新渲染元素(只改变CSS的显示与否)

简而言之，如果你需要频繁切换一个元素的显示状态，v-show 是更好的选择；而如果元素仅在某些条件下才显示，并且这些条件不那么频粹变更，那么v-if可能会是一个更适合的选择。

##  12. v-model 是如何实现的，语法糖实际是什么？

v-model是Vue.js中用于创建双向数据绑定的一个指令，通常用在表单元素上(如input, select, textarea等), 它能够将表单元素的值与Vue实例的数据保持同步

**如何实现的**

v-model在底层主要是结合了以下两个方面的功能：

1. **数据到视图的绑定**：当Vue实例的数据变化时，v-model保证了这些变化能够更新到绑定的表单元素上，即视图的更新

2. **视图到数据的绑定**：当表单元素的值被用户更改时，v-model保证了变更能够更新回Vue实例的数据中，即数据的更新

实际上，v-model相当于以下代码的语法糖

对于input(text类型)元素：

```
<input v-model="someData">

<!-- 等价于 -->

<input v-bind:value="someData" v-on:input="someData = $event.target.value">
```

对于checkbox和radio类型的input元素，实现细节略有不同，因为这些元素的值通常是boolean或一些已定义好的静态值：

```
<!-- Checkbox -->
<input type="checkbox" v-model="checked">

<!-- 等价于 -->

<input
  type="checkbox"
  v-bind:checked="checked"
  v-on:change="checked = $event.target.checked">
  ```

  ```
  <!-- Radio -->
<input type="radio" v-model="picked" v-bind:value="someValue">

<!-- 等价于 -->

<input
  type="radio"
  v-bind:checked="picked === someValue"
  v-on:change="picked = $event.target.value">
  ```

对于select元素

```
<select v-model="selected">

<!-- 等价于 -->

<select v-bind:value="selected" v-on:change="selected = $event.target.value">
  <option>A</option>
  <option>B</option>
</select>
```

**语法糖实际是什么**

语法糖(Syntactic sugar)是一个编程术语，指的是在编程语言中添加的某种语法，这种语法对语言的功能没有影响，但却使代码更加简洁易读。v-model就是一个典型的语法糖，它使得开发者能够使用简短的指令来替代可能更加繁琐的v-bind和v-on组合

在Vue.js 2.x中，v-model的默认行为可以通过以上方式理解。而在Vue。js 3.x之中，v-model变得更加通用和可配置，增加了对自定义组件的v-model的支持，通过modelValue作为prop和update:modelValue作为事件实现了更加自由的双向数据绑定

总之，v-model使得表单数据绑定在Vue.js中变得直观和易于管理，它背后的具体实现是通过适当的输入事件监听和属性绑定来完成的


##  13.data为什么是一个函数而不是对象
在Vue.js中，使用函数返回一个对象作为组件的data的主要原因是为了确保每个组件实例都可以维护一份被返回对象的独立的拷贝。

如果data是个对象，那么它将在所有组件实例之间共享。这意味着，如果一个组件实例改变了这个数据对象的任何属性，那么所有其他使用这个数据对象的组件实例都会受到影响，这可能会引入许多难以追踪的状态相关的问题

通过使用一个函数来返回数据对象，每次创建一个新的组件实例时，它都会调用这个data函数来创建一个新的数据对象。这样，每个组件实例都有一个属于自己的数据副本，修改其中一个实例的数据不会影响到其他实例

举个例子，对于组件的定义方法：
```
Vue.component('my-component', {
  data: function () {
    return {
      counter: 0
    }
  },
  template: '<button @click="counter++">{{ counter }}</button>'
});

```
每次使用\<my-component>\</my-component>时，Vue都会调用data函数来得到新的数据对象counter初始值为0。每个\<my-component>实例操作自己的counter不会影响到其它\<my-component>实例的counter值

这种方式时组件数据隔离的一个重要手段，确保了组件的独立性和可重用性。对于根实例(或单实例组件)我们一般不会有多个实例的情况，所以直接使用对象也没关系


##  14.对keep-alive的理解，它是如何实现的，具体缓存的是什么？

keep-alive是Vue.js中一个内置的抽象组件，它可以用来包裹动态组件或者有条件渲染的组件，从而使得其状态在渲染时可以被保留，即使它们在DOM中被切换或者移除。通过这种方式，keep-alive可以保持组件的状态或避免重新渲染，从而提升了性能

**如何实现的**

keep-alive实际上通过以下两个核心步骤实现

1. **缓存渲染VNode(虚拟节点)**：当组件第一次渲染后，keep-alive会将它的virtual DOM和组件实例等缓存下来。如果组件命中缓存，则会重用这些缓存的实例而非重新渲染。这意味着所有的组件状态会被保存下来
2. **控制组件的生命周期钩子函数**：当组件被包裹在keep-alive中时，它的activated和deactivated这两个生命周期钩子函数会被触发。activated时当组件被激活时(即从缓存中挂载到DOM)时调用，而deactivated是当组件被停用(即从DOM中移除但是保存在缓存中)时调用

**具体缓存的是什么**

keep-alive缓存的是组件的实例以及它对应的虚拟DOM节点，这包括了以下部分：

1. **组件实例**：组件的状态、局部数据、计算属性和组件方法被保存在实例中
2. **组件树(包括子组件)**：不仅是被keep-alive直接包裹的组件，其子组件树也会被缓存
3. **组件的虚拟DOM**：DOM元素并未在缓存中保存，保存的是虚拟节点数，即Vue在内存中保存的对应的数据结构

**用法示例**

```
<keep-alive>
  <component :is="currentView">
    <!-- 这里是会被缓存的组件 -->
  </component>
</keep-alive>
```

在本例中，currentView是一个动态的组件(如可以是'home'、'about'等)。当currentView的值来回切换时，之前已经渲染过的组件实例会被keep-alive缓存起来，当切换回来的时候，可以迅速恢复之前的状态

##  15.$nextTick 原理及作用
Vue的$nextTick方法是一个非常有用的工具，它允许你将回调延迟到下次DOM更新循环之后执行。在修改了某些数据以后使用$nextTick,可以保证在回调执行时，DOM已经完成了更新

**$nextTick的原理**：

Vue在更新DOM时是异步执行的。当侦测到数据变化，Vue并不会立即更新DOM，而是开启一个队列，并缓冲在同一事件循环中发生的所有数据改变。在缓冲期间，同一个watcher被多次触发只会被推入队列一次。这种在缓冲时去重和合并的策略能够避免不必要的计算和DOM操作。然后，在下一个事件循环"tick"中，Vue刷新队列并执行实际的(已去重的)工作

这种异步更新队列的机制，是利用了事件循环和微任务(microtask)。Vue在内部尝试对一系列不同的策略进行了探究，以获得一个异步延迟，这包括Promise.then、MutationObserver和setlmmediate，如果上述都不可用，则会使用setTimeout(fn, 0)。

**$nextTick的作用**

如前所述，Vue的DOM更新是异步的。这意味着当你更改了某些数据之后，立即读取对应的DOM可能还没有得到更新。$nextTick允许你在DOM更新完成后再执行某些操作，这是非常有用的，尤其是在执行DOM依赖的操作时，例如获取操作新渲染的DOM元素的尺寸或位置

**使用实例**：

```
new Vue({
  // ...
  methods: {
    // ...
    exampleMethod() {
      // 修改数据
      this.message = 'changed';
      
      // 立刻打印消息，可能不会得到更新后的DOM
      console.log(this.$el.textContent); // 'old message'
      
      // 使用$nextTick等待DOM更新
      this.$nextTick(() => {
        console.log(this.$el.textContent); // 'changed'
      });
    }
  }
})
```

在上面的例子中，如果我们试图在数据变化后立即访问DOM，打印的内容将会是更新前的。而$nextTick则确保我们的回调函数在DOM更新后执行

##  16.Vue 中给 data 中的对象属性添加一个新的属性时会发生什么？如何解决？

在Vue中，如果在组件的初始化阶段之后向data中的对象添加一个新的属性，这个新添加的属性将不是响应式的。这是因为Vue在初始化组件时，会使用Object.defineProperty对data中的属性进行处理，使得它们变成响应式的。但对于后来新增的属性，Vue就无法自动转换为响应式的了。

例如：
```
export default {
  data() {
    return {
      user: {
        name: 'Alice'
      }
    }
  },
  created() {
    this.user.age = 25; // 'age' 不是响应式的，改变它的值不会引起视图更新
  }
}
```

1. **使用Vue.set()或this**

Vue提供了一个全局方法Vue.set()以及实例方法this。set()来向响应式对象添加一个属性并确保这个新属性也是响应式的，同时触发视图更新

```
this.$set(this.user, 'age', 25);
```
或者使用全局方法
```
Vue.set(this.user, 'age', 25);
```

2. **提前在data初始化时声明属性**：

另一个解决方法是在初始化组件的data返回的对象里面预先声明所有需要的属性，即使开始时你可能还不知道确切的值，也可以先设置为null或者其他占位置

```
   data() {
     return {
       user: {
         name: 'Alice',
         age: null // 预先声明属性，即使值为null
       }
     }
   }
```
通过这些方法，你可以保证后来添加的属性也是响应式的，当其值变化时能够触发视图更新。

##  17.Vue中封装的数组方法有哪些，其如何实现页面更新

Vue中封装了一系列数组方法，以便当你使用这些方法时，能够自动确保响应式系统能够侦测到数组内容的变化，并更新页面。这些方法是对JavaScript数组原型中响应方法的一种增强。以下是Vue封装并修改为响应式的数组方法

1. push()
2. pop()
3. shift()
4. unshift()
5. splice()
6. sort()
7. reverse()

Vue通过拦截这些方法的调用来实现响应式更新。在内部，Vue会用这些被封装过的方法替换数组实例的原始方法。当你调用上述方法之一时，Vue不仅会执行原始的数组操作，还会执行依赖收集和视图的更新操作。

例如，当你使用push()方法向响应式数组添加新元素时，Vue会做两件事：

1. 向数组添加新元素
2. 侦测到数组发生了变化，触发更新，重新运行数组相关的渲染函数，将改动反映在DOM上

由于JavaScript的限制，Vue不能检测到以下数组的变动：

- 直接通过索引设置元素，如vm.items[indexOfItem] = newValue
- 直接修改数组的长度，如vm.items.length = newLength

为了解决这类问题，可以使用以下方法：

- 使用Vue.set()或vm.$set()来设置项，例如this.$set(this.items, indexOfItem, newValue)
- 使用splice()方法修改数组，例如this.items.splice(indexOfItem, 1, newValue)

总的来说，Vue内部封装的数组方法能够使得改变数组时自动更新视图，从而提供了一种有效的方式来处理数组响应式更新

##  18.Vue 单页应用与多页应用的区别
Vue可以用于构建单页应用(Single Page Application, SPA)和多页应用(Multi-Page Application, MPA)。以下是他们的主要区别：

**单页应用(SPA)**:

- **定义**：在SPA中，仅在初始加载时向服务器请求一次HTML文件，之后所有的页面更新都是通过Ajax请求获取数据并使用Vue渲染，页面不会进行完全的重新加载
- **用户体验**：页面切换快速且平滑，因为资源(如JavaScript和CSS)只需要加载一次，用户体验更加接近原生应用
- **前后端分离**：一般来说，SPA更倾向于前后端分离模式，后端主要提供API供前端使用。
- **SEO难度**：SPA相对于MPA来说，SEO优化更加困难，虽然现代化的搜索引擎已经可以执行Javascript，但是仍存在一些限制
- **性能和缓存**：首次加载需要更多时间，因为要加载所有必要的脚本和资源。但是，应用一旦加载完成后，页面的响应速度很快
- **开发和维护**：使用Vue Router可以轻松实现前端路由，管理不同页面的显示，并且整个应用的状态可以通过Vuex进行统一管理，这使得开发和维护都较为简单

**多页应用(MPA)**:

- **定义**：MPA是传统的网站架构，对每个页面的访问都会请求新的HTML文档，整个页面会重新加载
- **用户体验**：页面切换涉及到向服务器请求新页面，用户可能会感受到短暂的延迟和页面闪动
- **后端集成**：每个页面可能都是通过后端模板渲染而来，在某些应用场景下，后端渲染可能更加适合
- **SEO优化**：由于每个页面都能够直接由服务器送达，这对搜索引擎友好，使得MPA在SEO优化上占有优势
- **性能和缓存**：MPA的首次加载速度可能更快，因为只需要加载当前页面的资源。但是页面间的跳转可能就没有SPA那么快
- **开发和维护**：MPA可能需要更多的开发和维护工作，因为每个页面都可能有自己的逻辑和模板，这可能导致代码的冗余


选择SPA或者MPA取决于项目的具体需求，比如应用的大小、复杂性、SEO要求、开发资源等多方面因素。SPA提供了流畅的用户体验和灵活的前端架构，而MPA则在SEO和初始渲染速度方面表现更好

##  19.Vue template 到 render 的过程
在Vue.js中，将模板(template)转换为渲染函数(render function)的过程涉及到Vue的编译器。以下是整个过程的简述

1. **模板编写**：开发者在Vue组件中编写模板，这些模板定义了HTML结构，并且可以包含一些特殊的语法，如指令(比如v-if、v-for)和插值({{ }})
2. **模板解析**：当进行构建过程(如webpack打包时)，或通过\<script type="text/x-template">标签直接在浏览器中使用时，Vue会利用内置的编译器对模板进行解析，将模板字符串转换为JavaScript代码。这个解析过程涉及对模板语法的解析，将其转换为AST(抽象语法树)。
3. **优化**： Vue编译器会对AST进行遍历，标记静态节点。这一步骤是为了在后续的重新渲染过程中跳过静态节点，提高渲染性能
4. **生成渲染函数**：Vue编译器将AST转换为渲染函数。渲染函数是一段返回虚拟DOM节点(VNode)的JavaScript代码。Vue使用虚拟DOM作为更新DOM的中间层，以便高效地更新只有变化的地方
5. **挂载和渲染**：当渲染函数被调用，它返回虚拟DOM，Vue接着会将虚拟DOM与实际的DOM进行比较，并根据需要更新DOM。这个过程通常被称为"挂载"(当组件第一次创建时)或"重新渲染"(当组件的响应式数据发生改变时)

需要注意的是，当使用Vue CLI或其他构建工具时，模板到渲染函数的编译过程一般发生载构建阶段，不会再浏览器运行时发生，这可以提高应用的性能。另一方面，Vue也提供了运行时构建版本，其中包括编译器，允许你再客户端进行模板的即使编译，这在某些场景下(如需动态编译模板的情况)非常有用。不过，推荐在大多数情况下使用预编译模板来获取更好的性能和更小的构建体积

##  20.Vue data 中某一个属性的值发生改变后，视图会立即同步执行重新渲染吗？

在Vue中，当data中的某个属性值发生改变时，视图不会立即重新渲染。Vue实现了一套异步更新队列，这意味着它会将数据变化所触发的所有视图更新任务缓存起来，并不会立刻执行这些任务。

这里是改变发生后，视图更新的具体过程：

1. **响应性系统检测到数据变化**：当你修改了组件的data中的响应式属性，Vue的响应性系统会检测到这个变动
2. **更新加入队列**：Vue会将引起变化的更新作为一个任务加入到它的异步队列中。此时，它并不会直接对DOM进行操作
3. **异步执行更新**：Vue有一个'nextTick'队列，用于处理DOM更新。在当前事件循环结束后，在下一个事件循环"tick"中，Vue会刷新队列，执行更新DOM的实际工作
4. **批量更新**：如果在同一事件循环中对同一数据多次进行修改，Vue会将这些修改合并为一次更新。这个去重操作可以防止重复和不必要的计算和DOM操作，从而优化性能
5. **重新渲染完成**：在'nextTick'之后，DOM已经是最新的状态了。如果你想在DOM更新后执行一些额外的操作，你可以使用this.$nextTick(callback)方法，Vue将确保在DOM更新完毕之后执行你的回调函数。

举个例子：
```
this.message = 'updated';
this.$nextTick(() => {
  // 这里的代码将在DOM更新完成后执行
});
```
这种异步批量更新策略，允许Vue集中处理所有的数据变更并进行必要的DOM更新，这种更新机制可以显著地提高应用的性能


##  21.简述 mixin、extends 的覆盖逻辑
在Vue中，mixins和extends是用来复用组件逻辑的特性。它们都允许你将一组属性、方法、生命周期钩子函数等混合到一个组件中，但他们有一些不同之处。下面是他们的覆盖逻辑

**Mixins**：
- **使用方式**：可以将多个mixins混入到一个组件中
- **合并策略**：大多数情况下，如果组件和mixin对象含有同名选项，这些选项将以适当的方式合并。例如：
  - **数据对象**：在mixin和组件中的data被合并后，它们将形成一个对象。如果有冲突，组件数据将优先级更高
  - **钩子函数**：相同的生命周期钩子函数将被合并为一个个数组，因此都将被调用。混入对象的钩子将在组件自身钩子之前调用。
  - **方法**：如果方法名冲突，组件中的方法将覆盖mixin中的方法。
  - **计算属性**：如果计算属性冲突，组件中的计算属性将覆盖mixin中的计算属性
  - **组件/指令/过滤器**：如果有冲突，组件中的选项将覆盖mixin中的选项

**Extends**:
- **使用方式**：extends可以是一个对象或构造函数，它用于单个组件。
- **合并策略**：extends的合并策略与mixins非常相似。如果同名的选项出现在extends和组件定义中，组件内部的选项将会取代extends内部的选项。

简单来说，无论是mixins还是extends中，组件自身定义的选项会覆盖mixin或extends中定义的同名选项。不过，生命周期钩子则是合并成一个数组并且mixin/extends中的钩子会先执行。

##  22.描述下Vue自定义指令

Vue自定义指令是Vue.js提供的一个功能，允许你定义自己的指令并附加到DOM元素上，用于执行一些底层DOM操作。自定义指令的使用场景通常包括，但不限于焦点管理、监听滚动事件、动画等。

自定义指令的基本语法如下：

```
// 注册一个全局自定义指令 `v-my-directive`
Vue.directive('my-directive', {
  // 钩子函数，指令在绑定到元素时调用，只调用一次
  bind(el, binding, vnode, oldVnode) {
    // 一些初始化的操作
  },

  // 当绑定元素插入到 DOM 中时调用
  inserted(el, binding, vnode, oldVnode) {
    // 需要访问父节点的操作可以放在这里
  },

  // 当绑定元素所在的VNode更新时调用
  update(el, binding, vnode, oldVnode) {
    // 根据更新的数据进行操作
  },

  // 当绑定元素所在的VNode及其子VNode全部更新后调用
  componentUpdated(el, binding, vnode, oldVnode) {
    // 执行依赖于子节点的更新操作
  },

  // 指令与元素解绑时调用，只调用一次
  unbind(el, binding, vnode, oldVnode) {
    // 清理工作
  }
});
```

以下是每个钩子函数的作用简述：

- **bind**:在指令第一次绑定到元素时调用，可用于一次性的初始化设置
- **inserted**：在被绑定的元素插入父节点时调用(此时尚未挂载到DOM树)
- **update**：在包含指令的组件的VNode更新时调用，但是可能在其子VNode更新之前调用
- **componentUpdated**：在包含指令的组件的VNode及其子VNode全部更新后调用
- **componentUpdated**：在包含指令的组件的VNode及其子VNode全部更新后调用
- **unbind**：在指令与元素解绑时调用，只调用一次，用于执行清理工作

在指令定义对象中，el参数表示指令所绑定的元素，binding参数提供了一些关于指令的详细信息：

- **name**：指令名，不包含v-前缀
- **value**：指定的绑定值，例如：v-my-directive="1 + 1"中，binding.value的值为2
- **oldValue**：指令绑定的前一个值，仅在update和componentUpdated钩子中可用。无论值是否改变都可用
- **expression**：字符串形式的指令表达式。例如v-my-directive="1 + 1"中，expression的值为"1 + 1"
- **arg**：传给指令的参数，可选。例如v-my-directive:foo中，arg的值是"foo"
- **modifiers**：一个包含修饰符的对象。例如v-my-directive.prevent中，modifiers的值是{prevent: true}

使用自定义指令时可以在组件中局部注册，也可以全局注册。自定义指令提供了极大的灵活性，让你可以对DOM元素进行底层操作

##  23.子组件可以直接改变父组件的数据吗？
子组件不可以直接改变父组件的数据。这样做主要是为了维护父子组件的**单项数据流**。每次父级组件发生更新时，子组件中所有的prop都将会刷新为最新的值。如果这样做了，Vue会在浏览器的控制台中发出警告。

Vue提倡单向数据流，即父级props的更新会流向子组件，但是反过来则不行。这是为了防止意外的改变父组件状态，使得应用的数据流变得难以理解，导致数据流混乱。如果破坏了单向数据流，当应用复杂时，debug的成本会非常高

只能通过$emit派发一个自定义事件，父组件收到后，由父组件修改

##  24.Vue是如何收集依赖的
在Vue.js中，依赖收集是响应式系统的核心部分。这个机制允许Vue检测到数据变化并自动执行与数据相关的视图更新。以下是Vue如何收集依赖的步骤：

1. **响应式数据对象**：在组件初始化时，Vue会遍历组件的data对象中的所有属性，并使用Object.defineProperty()将它们转换为getter/setter。这些getter/setter用于依赖收集和触发更新
2. **侦听器(Watcher)**：对于组件的每一个数据依赖(例如，模板中使用的属性)，Vue会创建一个侦听器实例(Watcher)。这个侦听器会记录哪些组件依赖于这些数据属性
3. **依赖收集**：在组件渲染(或重新渲染)过程中，会触发数据属性的getter函数。在getter函数内部，会将当前的侦听器(Watcher)添加到这个数据属性的依赖列表中。这意味着当前的组件依赖于这个数据属性
4. **依赖通知**：当相关数据发生变化时，setter函数会被触发。Setter会通知所有注册在这个数据属性上的侦听器(Watcher)，告诉他们数据已经变化
5. **更新视图**：一旦侦听器(Watcher)接收到通知，它们会执行回调函数，这通常会引起组件视图的重新渲染。如果是计算属性的侦听器，它会重新计算计算属性的值

依赖收集的目的时建立一个观察者模式框架，使得每个数据属性都可以被视作一个主题(Subject)，而每个组件或计算属性等则是观察者(Observer)。当数据变化时，数据属性可以通知所有依赖与它的观察者，触发它们响应的行为

这种涉及允许Vue应用以高效的方式跟踪状态变化和更新视图，因为只有真正依赖于改变数据的组件才会被更新，这大大提高了应用性能，特别是在复杂数据状态下

##  25.Vue的优点

1. **轻量级框架**：只关注图层， 是一个构建数据的视图集合，大小只有几十kb；
2. **简单易学**：国人开发，中文文档，不存在语言障碍，易于理解和学习
3. **双向数据绑定**：保留了angular的特点，在数据操作方面更为简单
4. **组件化**：保留了react的优点，实现了html的封装和重用，在构建单页面应用方面有着独特的优势
5. **视图，数据，结构分离**：使数据的更改更为简单，不需要进行逻辑代码的修改，只需要操作数据就能完成相关操作
6. **虚拟DOM**：dom操作是非常耗费性能的，不再使用原生的dom操作节点，极大解放dom操作，但具体操作的还是dom不过是换了另一种方式；
7. **运行速度更快**：相较于react而言，同样是操作虚拟dom，就性能而言，vue存在很大的优势

##  26.delete和Vue.delete删除数组的区别

- delete只是被删除的元素变成了empty/undefined其他的元素键值还是不变
- Vue.delete直接删除了数组 改变了数组的键值

##  27.vue如何监听对象或者数组某个属性的变化
挡在项目中直接设置数组的某一项的值，或者直接设置对象的某个属性值，这个时候，你会发现页面并没有更新。这是因为Object.defineProperty()限制，监听不到变化

解决方式：
1. this.$set(你要改变的数组/对象，你要改变的位置/key，你要改成什么value)

```
this.$set(this.arr, 0, "OBKoro1"); // 改变数组this.$set(this.obj, "c", "OBKoro1"); // 改变对象

```

2. 调用以下几个数组的方法
```
splice()、 push()、pop()、shift()、unshift()、sort()、reverse()
```
vue源码里缓存了array的原型链，然后重写了这几个方法，触发这几个方法的时候会observer数据，意思是使用这些方法不用再进行额外的操作，视图自动进行更新。推荐使用splice方法会比较好自定义，因为splice可以在数组的任何位置进行删除/添加操作

vm.$set的实现原理是：

- 如果目标是数组，直接使用数组的splice方法触发响应式
- 如果目标是对象，会先判读属性是否存在、对象是否是响应式，最终如果要对属性进行响应式处理，则是通过调用defineReactive方法进行响应式处理(defineReactive方法就是Vue在初始化对象是，给对象属性采用Object.defineProperty动态添加getter和setter的功能所调用的方法)


##  28.对SSR的理解
SSR就是服务端渲染，就是Vue在客户端把标签渲染成HTML的工作放在服务端完成，然后再把html直接返回给客户端

SSR的优势：
- 更好的SEO
- 首屏加载速度更快

SSR的缺点：
- 开发条件会受到限制，服务器端渲染只支持beforeCreate和created两个钩子；
- 当需要一些外部扩展库时需要特殊处理，服务端渲染应用程序也需要处于Node.js的运行环境
- 更多的服务端荷载

##  29.Vue的性能优化有哪些

1. **编码阶段**
   - 尽量减少data中的数据，data中的数据都会增加getter和setter，会收集对应的watcher
   - v-if和v-for不能连用
   - 如果需要使用v-for给每项元素绑定事件时使用事件代理
   - SPA页面采用keep-alive缓存组件
   - 在更多的情况下，使用v-if替代v-show
   - key保证唯一
   - 使用路由懒加载、异步组件
   - 防抖、节流
   - 第三方模块按需导入
   - 长列表滚动到可视区域动态加载
   - 图片懒加载

2. **SEO优化**
   - 预渲染
   - 服务端渲染SSR

3. **打包优化**
   - 压缩代码
   - Tree Shaking/Scope Hoisting
   - 使用cdn加载第三方模块
   - 多线程打包happypack
   - splitChunks抽离公共文件
   - sourceMap优化

4. **用户体验**
   - 骨架屏
   - PWA
   - 还可以使用缓存(客户端缓存、服务端缓存)优化、服务端开启gzip压缩等


##  30.MVVM的优缺点

**优点**

1. **低耦合性**：视图(UI)和业务逻辑(Model)通过ViewModel进行解耦，改进一个层次不必影响到其他层次
2. **可复用性**：由于解耦，业务逻辑(Model)部分更容易复用在不同的系统中
3. **可测试性**：可以独立对ViewModel和Model层进行单元测试，提高测试的方便性和覆盖率
4. **声明性编程**：视图层使用声明式绑定(例如：在Vue.js中式模板绑定)描述界面外观与行为，增强可读性和易维护性
5. **自动UI更新**：ViewModel中的状态改变可自动反映到View上，无需手动操作DOM，这在前端框架如Angular、React、Vue.js等通过数据绑定提供支持的情况下尤其强大
6. **改进分工**：分层可以帮助更好地分配工作，设计人员可以专注在View上，开发人员可以专注于数据Model和ViewModel层


**MVVM的缺点包括**：

1. **学习曲线**：相比于传统的MVC等架构，MVVM引入了数据绑定和ViewModel这样的概念，可能需要一定的学习和理解事件

2. **过度工程化**：对于简单的应用来说，MVVM可能会让架构变得没必要地复杂
3. **性能问题**：在一些情况下，尤其是当数据绑定异常复杂或不当时，可能会引起性能问题，如内存消耗过大或界面反应迟缓
4. **调试困难**：自动的数据绑定和UI更新可能让调试变得更加困难，尤其是在数据绑定出错时
5. **抽象层的增加**：ViewModel增加了一个抽象层，对于一些开发者来说，抽象层可能会增加理解和维护的复杂度
6. **结构设计的挑战**：适当地设计ViewModel和组织数据流动需要开发者有一定的经验和对业务的清晰理解

总的来说，MVVM架构模式适用于中大型项目，它能够显著提高项目的可维护性和可扩展性。不过对于小型项目或简单的页面，使用MVVM可能会带来不必要的开销和复杂度。选择合适的架构模式应当基于项目的具体需求和团队的技术栈

#  二、生命周期

##  1.说一下Vue的生命周期
在Vue.js中，每个Vue实例在被创建时都会经历一系列的初始化过程-例如，它需要设置数据监听、编译模板、将实例挂载到DOM并在数据变化时更新DOM等。这些过程都会触发一系列的生命周期钩子，允许用户在特定的时间点添加他们自己的代码

以下时Vue实例的主要生命周期钩子：

1. **beforeCreate**:

在实例初始化之后、数据观测(data observation)和事件/侦听器的配置之前被调用

2. **created**

在实例创建完成后被立即调用。在这一步中，实例已经完成了数据观测(数据响应式)、属性和方法的运算、watch/event事件回调。然而，挂载阶段还没开始，$el属性目前不可见

3. **beforeMount**

在挂载开始之前被调用：相关的render函数首次被调用。该钩子在服务器端渲染期间不被调用

4. **mounted**

在el被新创建的vm.$el替换，并挂载到实例上去之后调用该钩子。如果根实例挂在了一个文档内元素，当mounted被调用时vm.$el也在文档内

5. **beforeUpdate**

在数据更新之前调用，发生在虚拟DOM打补丁之前。这里适合在更新前访问现有的DOM，比如手动移除已添加的事件监听器

6. **updated**

在由于数据变化导致的虚拟DOM重新渲染和打补丁之后调用。在这个钩子被调用时，组件DOM已经更新，所以现在可以执行依赖于DOM的操作。然而在大多数情况下，你应该避免在此期间更改状态，因为这可能会导致无限循环的更新

7. **beforeUnmount**(在Vue3.x中新引入)

在卸载组件实例之前调用。这个钩子在服务器端渲染期间不被调用

8. **unmounted**

在调用了destroy钩子后，组件被卸载后调用。这时组件的所有指令都被卸载，所有的事件监听器被移除，所有的子实例也都被卸载

9.  **beforeDestroy**(在Vue2.x中，对应Vue3.x中的beforeUnmount)

在实例销毁之前调用。在这一步，实例仍然完全可用

10. **destroyed**(在Vue2.x中，对应Vue3.x中的unmounted)

在实例销毁之后调用。调用后，Vue实例指示的所有东西都会解绑定，所有的事件监听器被移除，所有的子实例也都会被销毁


##  2.Vue子组件和父组件执行顺寻

**加载渲染过程**：

1. 父组件beforeCreate
2. 父组件created
3. 父组件beforeMount
4. 子组件beforeCreate
5. 子组件created
6. 子组件beforeMount
7. 子组件mounted
8. 父组件mounted


**更新过程**：

1. 父组件beforeUpdate
2. 子组件beforeUpdate
3. 子组件updated
4. 父组件updated


**销毁过程**：

1. 父组件beforeDestroy
2. 子组件beforeDestroy
3. 子组件destroyed
4. 父组件destroyed

##  3.created和mounted的区别

在Vue.js中，created和mounted都是组件生命周期钩子，但它们在组件初始化和渲染过程中的作用和触发时机不同

**created**

created钩子在组件实例被创建并且数据观测、事件监听、属性计算等初始化工作完成后被调用，但此时组件尚未挂载，因此它的模板和虚拟DOM还没有编译和渲染们同时$el属性还不存在

created是非常适合进行一些初始化的数据请求的地方，例如从后端API获取数据来填充组件的数据模型(data)，因为此时的数据已经被响应式系统处理，但组件还没有渲染到DOM中

**mounted**

mounted钩子在组件的模板和虚拟DOM已经编译并挂载到实际的DOM节点上之后被调用。这意味着当mounted钩子被执行时，你可以获取和操作DOM元素，执行如DOM操作或在挂载完成后才能执行的客户端的第三方库初始化等操作

简单地说，如果你需要在DOM真实存在后执行某些操作，就应该在mounted生命周期钩子中执行这些操作。但需要注意，如果你是在服务器端渲染(SSR)的上下文中，mounted钩子只会在客户端执行

**总结**
- **created**：组件实例已创建，数据已经准备好，模板尚未开始编译，真实DOM尚未生成
- **mounted**：模板编程成真实DOM，DOM已经挂载到页面上，可执行相关的DOM操作和优化


由于mounted只确保组件自己的模板被渲染了，如果组件有子组件的话，mounted不保证子组件已经被挂载。因此，如果你需要等待整个视图都渲染完毕，你可能需要使用\$nextTick或vm.$children来辅助确保所有子组件也已经渲染完毕


##  4.一般在哪个生命周期请求异步数据
在Vue.js中，异步数据请求通常在created生命周期钩子中进行。调用时机是因为在created钩子中，组件实例已经完成了初始化，数据已经被设置，计算属性已经可用，方法已经绑定，但是组件尚未挂载，意味着DOM还未生成

在created钩子中发出异步请求有以下几个好处：

1. **数据响应式**：由于数据已经被设置，你可以将异步返回的数据直接赋值给数据模型(data)，Vue会自动将其转换为响应式数据
2. **更快的用户体验**：由于在组件刚创建时就发出请求，可以让用户看到数据更快地被渲染，这样可以减少页面空白的事件
3. **服务端渲染(SSR)兼容性**：如果你再使用服务器端渲染，将数据请求放在created钩子中可以保证，无论是在客户端还是服务器端实例化组件，数据请求都能被正常执行
   
然而在某些场景下，如果你的数据请求需要访问或操作DOM，或依赖于DOM的计算(比如获取元素的尺寸来请求相应大小的数据)，那么应该在mounted生命周期钩子中进行。

简而言之，如果不需要与DOM交互进行数据请求，则推荐在created生命周期钩子中进行；如果你的数据请求依赖于DOM，或需要在数据请求完成后立即进行DOM操作，那么应该在mounted生命周期钩子中执行


##  5.keep-alive 中的生命周期哪些？

在Vue中使用keep-alive组件可以使被包裹的组件保持状态，避免重新渲染，从而提升性能。keep-alive特有的生命周期钩子主要有两个：

1. **activated**：当组件被keep-alive缓存的内容再次激活(即插入到文档DOM中)时调用
2. **deactivated**：当组件被移除(即从文档DOM中除去)时调用

使用keep-alive包裹组件时，组件不会经历正常的挂载和销毁过程，其正常的mounted和destroyed钩子不会被调用。相反，每次激活(重新插入DOM)或停用(从DOM中移除)这个组件时，activated和deactivated钩子将被触发

例如，如果你有一个列表页面和详情页面，从列表页面导航到详情页面时，你可能不希望列表状态丢失，以保持滚动位置或之前的搜索状态。你可以使用keep-alive来缓存列表页面组件，这样用户从详情页面返回列表页面时，列表页面不会被重新渲染，用户会看到之前的状态，而是直接从缓存中恢复

在这种情况下，当你从列表页面(被keep-alive缓存)导航到详情页面时，列表页组件会触发deactivated钩子。当用户从详情页返回列表页面时，组件会触发activated钩子

需要注意的是，keep-alive仅适用于组件(带有模板的Vue实例)，不适用于纯粹的JavaScript对象


# 三、组件通信

##  1. props / $emit

**props**

props是组件对外的接口，用于接收来自父组件的数据。当需要将数据从父组件传递到子组件时，可以通过props来实现。父组件通过标签上的属性传递数据给子组件的props，子组件中通过声明props的方式来接收这些数据

父组件中的用法示例如下：
```
<!-- 父组件模板 -->
<child-component :prop-name="value"></child-component>
```

子组件中的用法实例如下：
```
// 子组件定义
Vue.component('child-component', {
  props: ['propName'],
  // ...
})
```
子组件中定义了props选项，这里的propName就是父组件传递下来的数据，子组件可以像使用本地数据一样使用这些props

**$emit**
$emit是用于子组件向父组件发送消息(触发事件)的方法。当子组件需要通知父组件发生了某些事件，或者需要传递一些数据给父组件时，可以使用$emit来发出一个自定义事件，并且可以附带参数传递数据

子组件中的用法示例如下
```
// 子组件中触发事件
this.$emit('custom-event', data);
```

父组件中需要监听这个事件并定义相应的处理函数
```
<!-- 父组件模板 -->
<child-component @custom-event="handleEvent"></child-component>
```
在父组件的方法中接收事件参数：
```
// 父组件的方法
methods: {
  handleEvent(data) {
    // 处理子组件传递来的数据
  }
}
```

通过在子组件中使用$emit发出事件，在父组件中监听这个事件，我们可以实现子到父的通信。这种方式在实践中非常常见，尤其是在实现自定义组件时，$emit能够让父组件知道何时以及如何相应子组件发出的事件

总的来说，props是父传子的单项数据流，而$emit则用于子反馈给父的事件通信机制


##  2.eventBus事件总线（$emit / $on）

eventBus(事件总线)是Vue中另一种组件间通信的方式，它能够帮助不相关的组件之间进行通信。在Vue中使用事件总线的方式就是利用Vue实例的$emit和$on方法

**实现eventBus**

通常情况下，我们可以创建一个新的Vue实例作为事件总线，并利用它来触发事件和监听事件
```
const eventBus = new Vue();
```

**使用$emit触发事件**

任何组件都可以通过调用事件总线上的$emit方法来触发一个事件，并传递数据：
```
// 组件A发出事件
eventBus.$emit('eventName', data);
```

**使用$on监听事件**

其他组件可以通过$on方法在事件总线上监听这些事件，并定义相应的行为：
```
// 组件B监听事件
eventBus.$on('eventName', (data) => {
  // 响应事件，处理数据
});
```


**示例**

以下是一个简单的事件总线使用的例子：
```javascript
// main.js
import Vue from 'vue';
export const eventBus = new Vue();

// ComponentA.vue
<template>
  <!-- ... -->
</template>

<script>
import { eventBus } from './main.js';

export default {
  methods: {
    sendData() {
      eventBus.$emit('send-data', 'Hello from Component A!');
    }
  }
};
</script>

// ComponentB.vue
<template>
  <!-- ... -->
</template>

<script>
import { eventBus } from './main.js';

export default {
  created() {
    eventBus.$on('send-data', (message) => {
      console.log(message); // Outputs: 'Hello from Component A!'
    });
  },
  beforeDestroy() {
    eventBus.$off('send-data'); // it is important to clean up the event listeners when the component is destroyed
  }
};
</script>
```

在这个例子中，ComponentA组件发送数据时触发了事件总线上的send-data事件，ComponentB组件监听了这个事件并在控制台上打印消息

**注意**
- 一定要在组件的beforeDestroy生命周期钩子中用$off注销监听器，以防止内存泄漏
- 对于简单的场景或少量的跨组件通信，事件总线是一个实用的解决方案。但是如果项目较大或者通信较复杂，使用Vuex这样的状态管理库通常会更合适，因为它提供了更好的状态管理和更加结构化的通信机制

##  3.依赖注入（provide / inject）

在Vue中，依赖注入是一种特别的通信和状态共享方式，允许祖先组件向其所有子孙组件注入依赖，而无需通过每个层级逐层传递props

依赖注入是通过两个选项实现的：provide和inject

**provide**

provide选项允许你指定希望向下注入到组件树中的数据或方法。它可以是一个对象，或者返回一个对象的函数。祖先组件中使用provide来提供数据
```javascript
export default {
  //...
  provide() {
    return {
      sharedData: 'this is some shared data'
    };
  }
};
```
在上面的代码中，祖先组件提供了一个名为sharedData的数据属性

**inject**

injdect选项在接收组件中使用，用来指明要接收哪些来自祖先组件的数据。子孙组件可以使用inject来接收祖先组件中provide的数据：
```javascript
export default {
  //...
  inject: ['sharedData'],
  mounted() {
    console.log(this.sharedData);  // 输出：this is some shared data
  }
};
```
在上面的代码中，任何子孙组件都可以使用inject来引入sharedData数据

**注意**

- 被inject的属性是响应式的，如果provide的是一个可观察的状态(例如Vue.observable创建的对象或在data中的数据)，那么当这个状态改变时，所有注入了此状态的组件也会更新
- provide和inject绑定并不是可相应的，除非provide的值时响应式对象。理想情况下，你应该避免更改provide传递的对象，以防止不可预测的组件行为
- 依赖注入通常用于高级插件或组件库的开发中，其中一些组件需要访问共享资源，尤其适用于跨多个层级的组件通信，因为它避免了层层传递props的麻烦
- provide/inject在设计上并不是用来作为组件之间传递属性的主要方式，不应该替代props和事件。当组件间的关系更加明确，或需要更好的组件封装和复用时，应优先使用props和事件

总的来说依赖注入provide/inject为组件树内部的通信提供了一种灵活的解决方案，特别适合于开发深度嵌套的组件，同时要避免props的层层传递


##  4.$parent / $children

$parent / $children是Vue实例的属性，它们提供了一种在组件之间直接访问父组件实例和子组件实例的方法，它们可以被用来进行组件间通信，尽管这种方式不是最推荐的做法

**$parent**

\$parent属性提供了对父组件实例的引用。这意味着在任意子组件中，你可以使用this.$parent来访问它的直接父组件的数据、属性、方法等。举个例子：
```javascript
// 子组件中
export default {
  mounted() {
    console.log(this.$parent.someData); // 可以访问父组件的数据
    this.$parent.someMethod();          // 可以调用父组件的方法
  }
};
```
但是，频繁使用$parent可能会导致组件间的耦合过于紧密，这与Vue推荐的数据下行、事件上行的通信模式相违背

**$children**

相对地，\$children属性包含了父组件模板中所有子组件的实例数组。父组件可以通过this.$children来访问这些子组件的实例。例如：
```javascript
// 父组件中
export default {
  mounted() {
    this.$children.forEach((child, index) => {
      console.log(child.someData);      // 访问子组件的数据
    });
  }
};
```

不过，需要注意的是$children并不保证子组件的顺序，也不是响应式的，因此它主要用于访问子组件实例，而不是用于依赖其顺序的数据绑定

**注意事项**
- 使用\$parent和$children可能会导致代码难以维护，并不利于组件的解耦。依赖这些引用会使得组件间有隐式的依赖关系，使得组件的可重用性和可测试性下降
- 如果可能，应避免使用\$parent和\$children，而是使用props和事件(用\$emit传递自定义事件)，或者通过依赖注入(provide/inject)来进行组件间通信
- 某些情况下，如在动态生成的子组件或由于特定实现而无法使用props传递数据时，您可能不得不使用\$parent或\$children，但应将其用途最小化并严格控制


总的来说尽管\$parent和$children在某些场景下可以用于组件间通信，但它们违背了Vue推崇的组件通信原则，因此在组件设计中应该谨慎使用，优先考虑其他通信方式


##  5.$attrs / $listeners

在Vue中，\$attrs和$listeners是实例属性，它们通常在组件通信中，特别是在构建高阶组件或组件库时使用

**$attrs**

\$attrs包含了父作用域中(不是props属性的)绑定到子组件上的属性(即非prop的attribute)，这些属性是继承属性。这意味着它们不在子组件的props配置中声明，也没有再子组件的模板中用作局部变量。\$attrs在Vue2.4.0+版本引入，并自动将任何不在子组件的porps配置中声明的属性添加到子组件的根元素上

举个例子，如果你又一个包裹组件和一个按钮组件，你想要任何非prop属性能够传递给按钮组件，你可以通过$attrs来实现

```javascript
// 包裹组件
Vue.component('wrapper-component', {
  template: '<button-component v-bind="$attrs" />'
});

// 按钮组件
Vue.component('button-component', {
  props: ['disabled'], // 预期的prop
  template: '<button :disabled="disabled">Click me!</button>'
});
```

在这个例子中，绑定到\<wrapper-component>上的所有非prop属性会通过$attrs传递给\<button-component>

**$listeners**
\$listeners属性包含了父作用域中绑定在子组件上的非.native修饰的事件。它是一个对象，里面包含了作为子组件事件监听器的属性。在Vue3中，$listeners被移除，因为所有事件监听器被合并到了v-on="$attrs"中

在Vue2中，可以使用$listeners来将父组件中绑定的事件监听器转发到子组件中的其他元素：
```javascript
// 父组件
Vue.component('parent-component', {
  template: '<child-component @click="handleClick" />',
  methods: {
    handleClick() {
      console.log('Clicked!');
    }
  }
});

// 子组件
Vue.component('child-component', {
  template: '<div v-on="$listeners">Click Me</div>'
});
```
在上述代码中，子组件\<child-component>不会直接对点击事件作出反应。$listeners对象将点击事件监听器传递给子组件模板内的\<div>元素


**使用注意**
- 使用\$attrs和$listners时要注意，它们并不是响应式的，所以如果它们的属性或事件监听器在负组件中发生变化，可能不会引发子组件中的更新
- Vue3中移除了$listners，事件监听器自动包含在\$attrs中，这样可以简化转发事件监听器的操作
- 使用这些通信方式时要明确组件之间的界限和责任范围，确保组件的清晰度和可维护性

\$attrs和\$listeners提供了一种机制，可以在构建可复用和高阶组件时更灵活地传递数据和事件，但通常不用于简单的父子组件通信。基于这些特征，开发者可以创建出具有更好封装和更广泛适用性的组件


# 四、路由

##  1.Vue-Router的懒加载如何实现


Vue Router的懒加载功能允许您将组件分割成不同的代码块，然后在路由被访问的时候才加载对应组件的代码块。这样可以提高应用的初始加载速度，因为用户并不需要一开始就加载所有的页面。代码分割(或懒加载)在Vue应用中是通过webpack的代码分割(和动态import)或Rollup的动态导入功能来实现的

以下是如何实现Vue Router的懒加载步骤：

1. **定义路由时使用动态导入**：

通常，在使用Vue Router时，你会在路由配置中直接导入组件。例如

```javascript
import HomePage from './components/HomePage.vue';

const router = new VueRouter({
  routes: [
    { path: '/', component: HomePage }
  ]
});
```

为了实现懒加载，不要直接导入组件，而是使用动态导入的方式来定义组件：

```javascript
const HomePage = () => import('./components/HomePage.vue');

const router = new VueRouter({
  routes: [
    { path: '/', component: HomePage }
  ]
});
```

2. **配置webpack的chunk**

你可以更进一步地通过给导入语句添加webpack的特殊注释来命名你的chunks，使它们更容易在输出的打包文件中被识别。例如：

```javascript
const HomePage = () => import(/* webpackChunkName: "home" */ './components/HomePage.vue');
```

使用 webpackChunkName 注释可以将所有用到该注释的组件打包在同一个 chunk 中。这意味着如果有多个路由使用了相同的 chunk 名称，它们将共享同一个异步 chunk。

3. **配置babel插件**

如果你使用了Babel，确保安装了babel-plugin-syntax-dynamic-import，它允许Babel正确解析这些动态导入语句

请注意，Vue CLI创建的新项目默认配置了所有必须的webpack和Babel配置，所以你可能不需要手动进行任何额外的配置

通过这种方式，当用户访问某个路由时，Vue Router将动态地加载定义在那个路由配置中的组件。这种按需加载的方式可以大幅提高你应用的性能，特别是在有很多路由和每个路由下都有大量组件时

##  2.路由的hash和history模式的区别

在Vue Router中，路由的hash模式和history模式是控制URL如何显示和工作的两种不同方式。它们之间的主要区别在于它们如何利用浏览器的历史API和URL的锚点(或哈希)来工作

**Hash模式**

hash模式使用URL的hash(即#标记后面的部分)来模拟一个完整的URL，使页面的路径跟随在hash后面。当hash改变时，页面不会重新加载

例如，一个使用hash模式的URL可能会是：
```
http://www.example.com/#/user/id
```

**特点**：

- 使用window.location.hash来实现无需重新加载页面即可改变URL
- 页面跳转时，只改变#后面的内容，因此不会发送请求到服务器
- 兼容性好，可以在所有浏览器中工作，包括IE9及更早版本
- 因为没有实际向服务器发送新的请求去获取页面，所以只有前端知道如何处理路由


**History模式**

history模式利用了HTML5 History API(主要是pushState 和 replaceState方法)，它允许改变URL而不重新加载页面

例如，一个使用history模式的URL可能会是
```
http://www.example.com/user/id
```

**特点**
- 提供了一个更"干净"的URL，没有#
- 当用户刷新页面，或直接通过URL访问时，浏览器会向服务器发送请求。因此，服务器需要配置以支持SPA的单页面应用，通常是返回应用的主入口文件(例如index.html)，否则会出现404错误
- 让URL看起来就像传统的服务器渲染的应用路径
- 不支持老旧的浏览器


**对比**
- **URL美观程度**：history模式能够让URL看起来像普通的网址，不带有#；而hash模式则会包含#
- **服务器配置**：使用history模式时，任何URL都会要求服务器正确响应，需要后端配置支持；hash模式在浏览器端完成，不需要服务器特别配置
- **SEO优化**：虽然Google可以抓取某些AJAX内容，但history模式提供的URL格式对于搜索引擎更友好
- **兼容性**：hash模式几乎在所有浏览器中都可以运行，而history模式需要支持HTML5 History API的浏览器


##  3. 如何获取页面的hash变化

在前端开发中，获取页面URL中哈希(hash)值的变化通常是为了响应用户的导航操作，可以通过一些方法来监听和获取哈希变化

**使用window对象的hashchange**事件

可以通过监听hashchange事件来响应URL哈希部分的变化。这个事件在哈希变化时会被触发。你可以像这样添加一个事件监听器：

```javascript
window.addEventListener('hashchange', function(event) {
  console.log('The hash has changed!');
  console.log('Old hash:', event.oldURL.split('#')[1]); // 获取旧的哈希值
  console.log('New hash:', window.location.hash); // 新的哈希值
});
```

在hashchange事件的事件对象中，oldURL和newURL属性分别提供更改前后的完整URL。可以通过分割字符串的方式来获取实际的哈希值

**直接访问window.location.hash**

如果你只是想获取当前页面的哈希值，而不是监听它的变化，可以直接访问window.location.hash属性

```javascript
let currentHash = window.location.hash;
console.log(currentHash); // 输出当前的哈希值（带有"#"符号）
```

这将返回当前URL中的哈希字符串，包括"#"字符

**使用Vue Router**

如果你使用的时Vue.js并且集成了Vue Router，Vue Router会自动处理哈希的变化，并基于它来导航到相应的组件。在Vue Router中，你可以这样监听路由变化：

```javascript
router.afterEach((to, from) => {
  console.log('Route has changed!');
  console.log('Old hash:', from.hash); // 使用from.hash获取旧的哈希值
  console.log('New hash:', to.hash); // 使用to.hash获取新的哈希值
});
```

在上面的代码中，router是Vue Router的实例，afterEach是路由钩子，可以在路由发生变化后执行。

无论是使用原生方法还是Vue Router，监听和获取页面的哈希值都是非常简单直接的


##  4.\$route 和$router 的区别

在Vue.js中，当使用Vue Router为应用提供路由功能时，会遇到两个非常重要的对象：\$route和\$router，它们在Vue实例中作为属性存在，并且具有不同的用途和功能。

**\$route**

\$route是一个"路由信息对象"(Route Information Object)，它包含当前激活的路由信息。它是响应式的，当路由发生变化时，\$route对象也会随之更新。它包括以下信息：

- path：字符串，对应当前路由的路径，总是解析为绝对路径，如"/foo/bar"
- params：一个对象，包含路由中的动态片段和全匹配片段的键值对
- query：一个对象，表示URL查询参数。例如，对于路径/foo?user=1，则有\$route.query.user == 1
- hash:URL的哈希部分(带#符号)
- fullPath：完成解析后的URL包含查询参数和哈希的字符串
- matched：一个数组，包含当前路由的所有嵌套路径片段的路由记录
- name：当前路由的名称

你可以通过this.$route来访问这个对象，并获取当前路由的详细信息

**$router**

\$router是"路由器实例"(Router Instance)，指向了包含了整个路由的Vue Router实例。它通过编程式的接口来控制路由，如执行路由跳转、导航等操作。主要包含以下功能：

- push：导航到不同的URL，会往history栈添加一个新的记录，用户点击后退会返回到上一个记录的URL
- replace：导航到不同的URL，但不会向history添加新记录，替换掉当前的history记录
- go：这个方法会让内部的history堆栈向前或向后跳转相应的次数
- back：后退历史记录
- forward：前进历史记录

要导航到不同的URL，你不会修改\$route，而是使用\$router的方法来让路由实例进行相应的操作

**使用场景**

- 当你需要获取当前路由的路径或参数时，你会用到$route
- 当你需要改变URL，或者执行某种路由导航的时候，你会使用$router

**例子**

```javascript
// 获取当前路由的path
console.log(this.$route.path);

// 编程式地导航到一个新的路由
this.$router.push('/new-path');

// 将当前页面替换为一个新的路由
this.$router.replace('/new-path');

// 后退一步
this.$router.go(-1);
```

##  5.如何定义动态路由？如何获取传过来的动态参数？

在Vue.js中，动态路由是指路由中包含动态片段(例如参数)的部分。定义动态路由时，需要在路径中的参数前加上冒号：。当一个url匹配到动态路由时，可以在组件内用来获取动态参数

**定义动态路由**

在Vue Router的路由配置中，你可以如下定义动态路由：

```javascript
// 引入Vue Router
import VueRouter from 'vue-router';
import User from './components/User.vue';

// 创建router实例
const router = new VueRouter({
  routes: [
    // 动态路径参数使用冒号标记
    { path: '/user/:id', component: User }
  ]
});
```

在这个示例中，：id是一个动态片段，当你访问/user/123时，123会是id的值

**获取传递来的动态参数**

定义了动态路由后，可以在组件内用this.$route.params获取动态路径参数
```javascript
// User.vue
<template>
  <div>
    <h1>User ID: {{ userId }}</h1>
  </div>
</template>

<script>
export default {
  computed: {
    // 使用计算属性来从$route中获取动态参数
    userId() {
      return this.$route.params.id;
    }
  }
};
</script>
```

在上面的例子中，我们定义了一个计算属性userID来获取当前路由参数id的值。注意，当路由变化时(例如从/user/123导航到/user/456)，当前组件实例会被复用。由于路由参数的变化，通常不会触发组件的生命周期钩子，如果你依赖于参数变化来刷新页面或执行逻辑，你可能需要使用watch来观察\$route对象或使用beforeRouteUpdate导航守卫

```javascript
export default {
  watch: {
    // 监听$route对象，特别是其中的params
    '$route' (to, from) {
      // 响应路由参数的变化
      if (to.params.id !== from.params.id) {
        // 执行某些操作，如获取新的用户数据
      }
    }
  }
};
```

或者使用导航守卫：

```javascript
export default {
  beforeRouteUpdate(to, from, next) {
    // 路由变化前的逻辑
    // 在这里可以访问 this，因为这不是一个箭头函数
    this.fetchUserData(to.params.id);
    next();
  },
  methods: {
    fetchUserData(id) {
      // 根据id获取用户数据
    }
  }
};
```


##  6.Vue-router 路由钩子在生命周期的体现

Vue-router的路由钩子(也称为导航守卫)和Vue组件的生命周期钩子是Vue应用中非常重要的概念。它们允许开发者在特定的时机点介入应用的运行过程中，执行所需的代码

路由钩子可以分为全局守卫、路由独享的守卫和组件内的守卫。这里会重点解释组件内守卫是如何体现在组件声明周期中的

**组件生命周期与路由钩子**

当组件与路由相关联时，组件内的路由钩子将在不同的生命周期事件中被调用。以下是一个典型的组件生命周期与路由钩子结合时的顺序：

1. **导航被触发**：当你通过\<router-link>或者编程式路由(this.\$router.push或this.\$router.replace)导航到一个路由时，导航过程开始
2. **调用全局beforeEach守卫**：如果定义了全局前置守卫，首先会调用这个守卫
3. **调用路由独享的守卫**：如果目标路由配置了beforeEnter守卫，接下来会调用这个守卫
4. **解析异步路由组件**：如果目标组件是异步组件，会先等待组件解析完成
5. **在被激活的组件里调用beforeRouteLeave守卫**：当从一个路由导航到另一个路由，并且两个路由使用不同的组件时，当前活跃的组件将调用beforeRouteLeave守卫
6. **调暗勇全局beforeResolve守卫**：在导航被确认之前，即所有守卫和异步组件被解析之后，调用全局beforeResolve守卫
7. **导航被确认**：目标路由的进入守卫和组件的守卫解析完成后，导航就被确认了
8. **调用全局afterEach守卫**：确认导航后，调用全局后置守卫afterEach
9. **触发DOM更新**：Vue的响应式系统会根据路由的变化来更新DOM
10. **创建新的组件实例**：如果路由进入一个新的组件，此时组件实例会被创建
11. **beforeCreate和created生命周期钩子被调用**：在新的组件实例创建后，会依次调用其beforeCreate和created生命周期钩子
12. **调用beforeRouteEnter守卫**：在新组件被挂载之前，会调用beforeRouteEnter守卫
13. **beforeMount生命周期钩子被调用**：然后组件的beforeMount钩子将被调用
14. **组件被挂载到DOM**：组件挂载到DOM后，mounted钩子被调用
15. **调用beforeRouteUpdate守卫**：(对于重用的同意组件导航)，如果路由导航是在同一个组件内部(只是参数或查询变化)，则会调用beforeRouteUpdate守卫
16. **updated生命周期钩子被调用**：当组件的数据变化导致更新之后，updated钩子被调用


##  7.Vue-router跳转和location.href有什么区别？

Vue-router的跳转和直接设置location.href在改变浏览器URL以及页面导航方面具有本质的区别，这些差异主要体现在以下方面：

**Vue-router跳转**

Vue-router提供了一系列的方法和功能，允许开发者以编程方式导航到不同的路由，例如使用\$router.push、\$router.replace和\$router.go等方法。这些方法提供了SPA(单页应用)中路由变化的控制，并且可以无刷新地在组件间切换，维护了应用状态和生命周期，支持更丰富的导航控制和过渡效果

**特点**：

- **无刷新导航**：在不重新加载整个页面的情况下更新视图，提供了流畅的用户体验
- **路由钩子**：可以使用Vue-router提供的路由钩子(例如：beforeEach、beforeEnter、afterEach)来控制导航行为、例如进行权限校验、数据获取等操作
- **状态维护**：应用的状态(包括Vue组件的状态)在路由切换时得以保持，例如不会丢失已经存在的组件状态或者Vuex中的状态
- **动画/过渡**：可以结合Vue的过度系统实现在路由切换时的动画效果


**location.href**

设置location.href会导致浏览器加载新的页面，相当于在浏览器地址栏中输入一个新的URL并回车。这种方式属于传统的多页应用(MPA)页面跳转方式，将重新加载整个页面

**特点：**

- **页面全刷新**：导航会触发新页面的请求，浏览器将重新加载页面，包括HTML、CSS和Javascript等资源文件，从而导致页面闪烁
- **状态丢失：**当前页面的状态将丢失，所有在内存中的状态(如JavaScript变量)和当前页面的JavaScript执行环境都会被重置
- **无过渡效果：**标准页面跳转过程没有应用内的动画和过渡效果


**对比**

- **性能：**Vue-router跳转通常比location.href更快，因为它避免了重新加载整个页面，提供了更快速的导航体验
- **SPA与MPA：**Vue-router跳转是为单页应用设计的，而location.href跳转则常用于传统的多页应用。
- **控制：**Vue-router跳转提供更精细的导航控制，例如它允许你根据用户的操作或应用的状态来预防导航或更改导航行为
- **历史记录：**Vue-router的某些跳转方法(如\$router.replace)不会向浏览器的历史记录栈中添加新的记录，而location.href总是会添加新的历史记录
- **SEO：**由于location.href触发的是全新的页面加载，它更适合SEO，并且搜索引擎更易于抓取整页内容

总的来说，location.href更像是传统的页面全量跳转方式，而Vue-router提供了现代单页应用中无刷新导航的能力，两者适用于不同的场景与需求

##  8.params和query的区别

在Vue Router中，params和query是两种不同的路由参数，它们用于传递信息给路由的目标页面。这两者在URL结构、使用方式和特点上都有所不同

**params(路径参数)**

- **URL结构**：params参数作为URL的一部分，通常位于基本的URL路径之中。例如：/user/123，其中123是一个参数
- **定义方式**：在路由配置中，需要在路径中使用：来指定参数的名称，如/user/:id
- **获取方式**：在组件中可以通过this.\$route.params来获取这些参数，如this.\route,params.id
- **特点**：params在使用\<router-link>标签或\$router.push()、\$router.replace()方法跳转时必须提供，如果没有提供必须的params参数，将会导致无法正确跳转到目标路由

**query(查询参数)**

- **URL结构**：query参数通常称为查询字符串，位于URL的?符号后面，并以键值对的形式出现。例如：/search?keyword=vue，其中keyword=vue是查询参数
- **定义方式**：与params不同，query参数不需要在Vue Router的路由路径中预先定义
- **获取方式**：在组件中可以通过this.\$route.query来获取这些参数，如this.\$route.query.keyword
- **特点**:query参数在使用\<router-link>标签或\$router.push()、\$router.replace()方法跳转时，不是必须的，可选提供。且query参数不会导致路由匹配失败


**示例对比**

举一个具体的例子，假设我们有一个路由配置和两个链接：
```javascript
// 路由配置
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User }
  ]
})

// 跳转 (假设当前的路由为 /user/123)
<router-link :to="{ path: '/user/123', params: { id: 123 }}">User Profile</router-link>
<router-link :to="{ path: '/user/123', query: { plan: 'private' }}">User Profile with Plan</router-link>

// 编程式导航
this.$router.push({ path: `/user/123`, params: { id: 123 } }) // 导航到 /user/123
this.$router.push({ path: `/user/123`, query: { plan: 'private' } }) // 导航到 /user/123?plan=private
```


在上述代码中，当使用params时，id的值123成为了URL的一部分，形如/user/123.而使用query时，参数plan以查询字符串的形式附加在URL的后面，形如/user/123?plan=private

**小结**

- params映射到URL的路径部分，query映射到URL的查询字符串部分
- params参数需要在路由定义时指定，而query参数可以随时添加到任何路由
- query参数更类似于传统网址中GET请求中的查询参数；params则是URL路径的一部分，更类似于具体的资源路径
- params参数在不同的路由之间需要保持结构的一致性；query参数更灵活，不受路由结构的约束

##  9.对前端路由的理解

前端路由是现代前端框架和库(如Vue.js、React、Angular等)的一个核心概念，它允许开发者在单页应用(SPA)中定义视图之间的导航规则。前端路由使得用户在不重新加载整个页面的情况下，就能够在不同的视图(或称为页面、组件)间切换，从而提供更加流畅和快速的用户体验。以下是对前端路由的一些关键理解

**前端路由的工作原理：**

- **SPA**：在单页应用中，用户与应用的交互是通过单个页面实现的，这个页面在整个生命周期中只会加载一次。前端路由控制在这个页面内部加载不同的内容
- **History API/Hash**：前端路由通常利用HTML5的Histroy API或者URL的hash(即#后面的部分)来在单页应用内部实现路由管理。History API提供了一种更加现代和优雅的路由实现方式，而hash则被用于向后兼容就浏览器
- **无刷新交互**：用户在点击链接或者导航时，前端路由会捕获这些事件并组织浏览器向服务器发起新的页面请求。然后根据浏览器的URL变化来决定加载或展示什么样的视图


**前端路由的实现**：

- **路由表/配置**：定义一系列路径与组件的映射关系，当用户访问某路径时，前端路由就会加载与之对应的组件
- **路由链接**：\<a href="...">标签在SPA中通常会被替换成路由链接如(Vue.js的\<router-link>)，这些链接用于触发路由跳转
- **编程式导航**：除了通过链接点击实现路由跳转外，还可以使用代码(例如router.push或router.navigate方式)来进行导航

# 五、Vuex

##  1.Vuex的原理

Vuex是一个专为Vue.js应用程序开发的状态管理模式。它用一个集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。Vuex的核心概念和原理包括以下几点：

**单一状态树**

Vuex使用单一状态树-即这个对象包含全部应用层级状态的对象。该对象就是每个Vuex存储的基本数据结构。单一状态树使得我们可以直接定位任一特定的状态片段，在调试过程中也能够更容易地进行状态持久化或状态快照

**组件树的数据来源**

传统的Vue组件通讯多使用props向下传递、用事件向上传递(也有provide/inject等高级特性)，这对少量且嵌套层次不深的组件来说是有效和便捷的。然而，对于大型应用来说，Vuex提供了一种更好的解决方案，任何组件都可以从Vuex store中读取全局状态，也可以执行全局状态变更的行为

**状态管理模式**

Vuex把应用的状态放在一个地方管理，但会有以下几个不同的概念

- **State**：定义了应用状态的数据结构，可以在这里设置默认的初始状态
- **Getters**：允许组件从Store中获取状态，可认为式store的计算属性
- **Mutations**:是唯一更改store中状态的方法，它包含同步操作
- **Actions**：与mutations类似，不同之处在于它们提交的是mutation，而不是直接变更状态，可以包含任意异步操作
- **Modules**：当应用变得非常复杂时，store对象就可能变得相当臃肿。Modules可以让我们将store分割成模块(module)。每个模块拥有自己的state、mutation、action、getter甚至是嵌套子模块

**响应式原理**

Vuex的状态存储是响应式的，当Vue组件从store中读取状态的时候，若store中的状态发生变化，相应的组件也会相应地得到高效更新

**严格模式**

在严格模式下，Vuex确保状态变更只能在mutation的事件回调中进行，这样有利于我们更好地理解所有的状态变更都是可预测的

**插件功能**

Vuex允许我们在Store上应用插件，例如当状态变化时进行日志记录等。开发者也可以自己编写新的插件应用于Vuex

**开发工具集成**

Vuex支持开发过程中时间旅行式的状态管理调试。通过Vue开发者工具，可以直观的查看到store中的状态变更，甚至可以在测试过程中旅行至状态变更的截点

总结Vuex的原理，其本质时通过集中式存储管理应用的所有组件的状态，并且以一种符合规则的方式确保状态以可预测的形式发生变化。这种模式借助Vue的数据响应机制，保证了状态变更的一致性，并且适合于大型复杂应用的状态管理


##  2.Vuex中action和mutation的区别

在Vuex中，mutations和actions是两个专门用于处理状态和逻辑的概念，它们在职责和适用场景上有明显的区别

**Mutations**
mutations负责执行同步操作，它们是唯一可以直接更改状态(state)的方法。每个mutation都有一个字符串的事件类型(type)和一个会点函数(handler)。这个回调函数就是我们实际进行状态更改的地方，并且它会接受state作为第一个参数

**特点**：

- 只能执行同步操作
- 通过commit方法触发mutation
- 便于跟踪状态变化，因为每次commit都会留下记录

```javascript
// Mutation 定义
const mutations = {
  INCREMENT (state) {
    state.counter++
  }
}

// 在组件中触发 Mutation
this.$store.commit('INCREMENT');
```

**Action**

actions则负责处理异步操作，它们不能直接改变状态，但可以提交(commit)mutation。actions也可以执行同步操作，但通常用来执行异步逻辑，如从服务器获取数据。每个action也有类型和处理器，处理器可以接收一个与store实例具有相同方法和属性的context对象，从而可以执行提交commit、分发dispatch等操作

**特点**：

- 可以包含任意异步操作
- 通过dispatch方法触发action
- 不能直接更改状态-它们必须通过mutation来实现状态改变

```javascript
// Action 定义
const actions = {
  incrementAsync ({ commit }) {
    setTimeout(() => {
      commit('INCREMENT')
    }, 1000)
  }
}

// 在组件中触发 Action
this.$store.dispatch('incrementAsync');
```

**总结区别**

- **更改方式**：mutation用于更改状态，必须是同步的；action用于提交mutation，可以是异步的
- **提交方式**：mutation通过commit提交；action通过dispatch分发
- **执行操作**：mutation中不应该包含异步逻辑；action可以包含异步逻辑和异步操作

因此，在Vuex中，当你需要异步或批量提交多个mutation时，应该使用action；而在你需要直接更改状态时，你应该通过mutation来做到这一点。这样的规则确保了我们能够更好地跟踪和记录状态的变化，因为所有的状态改变都是显示的和可追踪的

##  3.Vuex 和 localStorage 的区别

Vuex和localStorage是两个前端开发中用于存储数据的不同技术，它们有各自的特点和应用场景

**Vuex**

Vuex是一个专为Vue.js应用程序开发的状态管理库，它通过集中式存储管理所有组件的状态，并以一种可预测的方式来保证状态以一致的规则发生变化

**主要特点**：

- **响应式状态管理**：Vuex的状态存储是响应式的，对state的修改会立即反映到视图组件中
- **组件间共享状态**：方便在多个组件间共享状态，通过getters还可以派生状态
- **开发工具集成**：提供时间旅行式的状态管理调试
- **支持插件和中间件**：可以使用插件监听每一个状态变更
- **严格模式**：在严格模式下，所有的状态变化必须通过mutations，这使状态变更可追溯，易于调试


**适用场景**：主要用于大型单页面应用，利于状态的维护和管理


**localStorage**

localStorage是Web Storage API提供的一个接口，能让网站在用户的浏览器上保存键值对数据

**主要特点**

- **持久性存储**：数据存储在客户端，持久性保存，即使关闭浏览器或重启电脑后数据依旧存在，直到被手动清除
- **容量较大**：比cookies提供更大的存储空间
- **简单API**：提供简单的getItem、setItem、removeItem等API
- **同步操作**：localStorage的操作是同步的，可能会阻塞主线程

**使用场景**：适用于少量数据的长期保存，例如用户的登录状态、用户偏好设置或其他不需要经常更新的数据


**区别总结**

- **数据响应性**：Vuex状态是响应式的，当状态发生变化时，绑定的视图会自动更新；而localStorage不是响应式的，数据更新后需要手动读取最新的数据更新视图
- **生命周期**：Vuex中的数据只在页面会话期间有效，页面刷新后状态会丢失(除非配合持久化插件)；localStorage中的数据则会一直保存在本地，直到明确的删除操作被执行
- **目的**：Vuex主要是用来管理和维护应用的状态，而localStorage主要用于持久化地存储数据
- **大小限制**：localStorage有大小限制(大约5MB)，而Vuex受限于JavaScript引擎和页面的内存限制
- **数据访问**：Vuex存储在内存中，访问速度块；localStorage存储在硬盘上，数据读写相比内存慢
- **API复杂度**：Vuex提供了一套复杂的API，如支持mutations、actions、modules等，而localStorage API较简单直接

从上面的比较可以看出，虽然两者都可以用于数据存储，但应用的目的、存储方式和范围有很大区别。合理使用Vuex和localStorage可以使得数据更有效地在Vue.js应用中流转和持久化

##  4.为什么要用 Vuex 或者 Redux

Vuex和Redux这两个状态管理库都是为了解决在复杂应用中，随着组件数量的增加，管理共享状态的复杂性日益增加的问题。它们通过提供一个集中的状态管理机制，帮助开发者在大型应用中维护状态的一致性和可预测性。以下是使用Vuex或Redux的一些主要原因

**组件状态共享**

在多个组件需要共享某些状态时，如果没有集中管理机制，状态的同步和更新将变得复杂和易错。Vuex/Redux提供一个集中的存储，所有需要共享的状态都放在这里，通过同一的接口进行访问和修改

**维护状态的可预测性**

Vuex/redux强制使用明确定义的方法去改变状态，这样可以很容易地追踪每一个状态的变化，这些状态的变化可以通过工具跟踪，记录甚至"时光旅行"进行调试

**状态的持久性和回溯性**

由于所有的状态变化都是通过集中的方式来进行操作的，这使得实现撤销/重做、状态持久化等功能变得容易。开发者可以通过记录或者保存状态历史的快照来回溯状态

**逻辑复用和代码组织**

Vuex/Redux的模式鼓励将业务逻辑提取到独立于组件的地方(如Vuex的actions/mutations或Redux的reducers)，这样可以更容易地复用和测试这些逻辑，以及更好的组织代码结构

**插件和中间件**

Redux/Vuex都允许使用插件和中间件来扩展功能。例如，可以使用中间件来处理日志记录、创建异步操作，或者是向本地存储持久化某些状态

**开发工具支持**

Redux和Vuex都有出色的开发者工具支持。例如Redux DevTools和Vue.js devtools支持状态树的实时观察、调试以及状态的时间旅行功能

**应对大型应用的复杂度**

在大型单页面应用(SPA)中，管理组件的状态变得非常繁琐。Vuex/Redux通过规范化状态便跟提供了一种方法，可以协助开发者管理这种复杂性，并确保应用的高效率和可靠性

**结论**

不是每一个Vue或React应用都需要使用Vuex或Redux。对于小型或中型应用，本地状态管理可能就足够了。但在构建大型应用、需要在组件间共享多个状态时，使用Vuex或Redux可以极大地帮助开发者以可维护和可靠的方式管理状态复杂性

##  5.Vuex和单纯的全局对象有什么区别

Vuex和单纯的全局对象用于在Vue.js应用中共享状态，但存在以下重要区别

**Vuex**

Vuex是一个专为Vue.js设计的状态管理库，用于以可预测的方式处理单页应用中的共享状态

**特点**

- **响应式**：Vuex的状态管理是响应式的，当状态发生改变时，依赖这些状态的组件会自动更新
- **规范化状态修改**：通过定义mutations和actions，Vuex规范了状态的修改方式，使得状态的变化变得可追踪和可维护
- **模块化**：Vuex允许将状态分割为模块，方便大型应用的状态管理
- **集成开发工具**：支持如Vuex时间旅行调试器等开发工具
- **插件系统**：支持插件，例如用于表单处理和状态持久化的插件
- **严格模式**：开发模式下，强制通过mutations来更改状态，有助于避免状态修改的不可预测性

**全局对象**

一个简单的全局对象(或单例模式)可以在Vue.js中用来作为全局状态的容器

**特点**：

- **易于创建**：创建一个全局对象只是简单的JavaScript对象编码，不需要专门的库或框架
- **非响应式**：除非你手动使其响应式，否则全局对象的属性变化不会触发视图更新
- **无规范化的状态修改**：没有专门的机制来规定如何修改状态，这可能导致难以跟踪和维护状态的修改
- **缺乏开发工具支持**：不支持Vuex提供的时间旅行调试等待性
- **无插件系统**：没有内置的插件系统来扩展功能
- **无严格模式支持**：没有内置机制来强制状态的修改规范

**区别总结**

虽然Vuex和全局对象都能在Vue.js应用中共享状态，但Vuex提供了一套完整的解决方案，包括响应式数据流、状态修改的规范、开发工具等。相比之下，全局对象是一个更为简单的解决方案，适合小型应用或简单的状态共享需求，但随着应用的复杂度增加，全局对象可能会导致状态管理混乱且难以维护


##  6.为什么 Vuex 的 mutation 中不能做异步操作？

Vuex要求mutation中不得包含异步操作，主要是因为Vuex的状态管理是同步和可追踪的以下是为什么mutation中不能有异步操作的几个关键原因

1. **状态追踪**

Vuex使用一个存储来持有应用的状态，并确保状态只能以可预测的方式更新。如果mutation中允许异步操作，那么我们将无法准确知道状态是何时更新的。这会导致调试困难，并且使得插件或者Vuex自身的功能(如时间旅行调试)不稳定

2. **响应式系统的限制**

Vue.js使用基于依赖跟踪的响应式系统，并且可以在数据变化时同步地自动更新DOM。同步操作可以确保在执行mutation后组件的视图会立刻反映新的状态。如果在mutation中进行异步操作，这个更新时机的保证就会失效

3. **调试工具和插件**

Vuex支持通过插件集成调试工具，比如Vuex的devtools扩展。这些工具假设使用Vuex的代码遵循同步执行。异步操作会使得调试变得复杂，因为它们导致的状态改变不会被正确记录

4. **维持一致性**

在mutation中执行异步操作会使Vuex的数据流变得不可预测，这与它设计原则相违背。Vuex的设计尽可能地简化应用的状态管理，其关键是让状态变更变得可预测且可靠。所以mutation应该保持简单且同步，而异步逻辑应该被放到action中

**结论**

Mutation中的操作需要是同步的，以便追踪状态的变化，确保响应式系统可以更新到最新的状态，并且使得开发者可以利用调试工具进行问题诊断和应用状态的时间旅行。异步逻辑应该通过action并配合mutation使用，在action中处理异步操作后，使用commit方法提交mutation更改状态。这样可以组织和协调异步业务逻辑和状态管理，确保应用状态的清晰度和一致性

##  7.Vuex的严格模式是什么,有什么作用，如何开启？

Vuex的严格模式是一种开发时的辅助特性，用以确保你的状态管理遵循规则，保证状态的变更都是显式且可追踪的。当严格模式开启时，Vuex会在每次状态变化后进行深度检测，以确保状态的变化是由mutation函数触发的

**作用**

严格模式的主要作用是在开发过程中帮助捕捉违规的状态修改。以下是严格模式的具体作用：

- **确保状态变更的明确性**：所有的状态，必须通过mutation函数修改，而不能直接被外部过程修改，确保了修改的唯一性
- **帮助开发调试**：如果状态在mutation函数之外被修改(例如，在组件内部直接修改)，Vuex会抛出错误，提醒开发者注意不规范的操作，这有助于及时发现和修正代码问题
- **保持状态管理的清晰**：保证了改变状态的代码都会集中在mutation函数，有助于代码的维护和管理


**如何开启**

严格模式可以通过在创建Vuex store时传入strict：true选项来开启

```javascript
const store = new Vuex.Store({
  // ...
  strict: true
})
```

在严格模式下，任何未经mutation函数处理的状态变更都会抛出错误，这有助于开发时及时发现问题。但是请注意，由于严格模式会对状态树进行深度检测，这可能会导致性能开销，因此严格模式旨在开发环境下使用，在生产环境下应该关闭：

```javascript
const store = new Vuex.Store({
  // ...
  strict: process.env.NODE_ENV !== 'production'
})
```

如上所示，推荐方式是只在开发环境中启用严格模式，以避免生产环境中因深度检测而引入的性能问题。在构建工具(如Webpack或者Vue CLI)提供的项目模板中，通常会使用环境变量来根据开发还是生产环境来动态决定是否开启严格模式

##  8.如何在组件中批量使用Vuex的getter属性？

在Vue组件中，如果你想要批量使用Vuex的getters属性，可以使用Vuex提供的辅助函数mapGetters。这个辅助函数可以将store中的getter属性映射到本地计算属性，让你可以更方便地在组件模板中访问这些值

以下是如何在组件中使用mapGetters:

首先你需要在你的组件中引入mapGetters：

```javascript
import { mapGetters } from 'vuex';
```

接下来，假设你的store中有几个getter函数：

```javascript
const store = new Vuex.Store({
  getters: {
    getterOne: state => state.valueOne,
    getterTwo: state => state.valueTwo,
    // 其他 getters...
  },
  // ...
});
```
在组件中，你可以使用mapGetters来映射这些getters到计算属性中：
```javascript
export default {
  computed: {
    // 使用对象展开运算符将 getters 映射为计算属性
    ...mapGetters([
      'getterOne', // 将 `this.getterOne` 映射为 `this.$store.getters.getterOne`
      'getterTwo', // 将 `this.getterTwo` 映射为 `this.$store.getters.getterTwo`
      // 其他需要映射的 getters...
    ]),
    // 你也可以有其他自定义的计算属性
    customComputed() {
      // ...
    }
  },
  // ...
};
```

现在你可以像访问本地计算属性一样访问这些getters了，例如在你的组件模板中：

```html
<template>
  <div>
    <p>Value One: {{ getterOne }}</p>
    <p>Value Two: {{ getterTwo }}</p>
  </div>
</template>
```

如果你想要给这些getter属性设定不同的本地名称，也可以将mapGetters用作一个对象形式

```javascript
computed: {
  ...mapGetters({
    localGetterOne: 'getterOne', // 将 `this.localGetterOne` 映射为 `this.$store.getters.getterOne`
    localGetterTwo: 'getterTwo'  // 将 `this.localGetterTwo` 映射为 `this.$store.getters.getterTwo`
  }),
  // ...
}
```

这种方式允许你将Vuex的getter映射到组件中的属性，可以随意命名为你所需的任何名字。这在你需要避免命名冲突或者提高可读性的时候特别有用。使用mapGetters可以有效地在组件中批量使用Vuex的getter属性，并保持代码的整洁和可维护性


##  9.如何在组件中重复使用Vuex的mutation？

为了在Vue组件中重复使用Vuex的mutation，可以利用Vuex提供的另一个辅助函数mapMutations，类似于mapGetters。这个辅助函数会将指定的mutation方法映射到Vue组件的方法中去，这样你就可以直接调用这些方法来提交mutation，而无需使用this.\$store.commit方法

以下是如何在组件中使用mapMutations的方法

首先在你的组件中引入mapMutations:

```javascript
import { mapMutations } from 'vuex';
```

假设你的store中有如下mutation函数

```javascript
const store = new Vuex.Store({
  mutations: {
    increment(state) {
      state.count++;
    },
    decrement(state) {
      state.count--;
    },
    // 其他 mutations...
  },
  // ...
});
```

在组件中，可以这样使用mapMutations:

```javascript
export default {
  methods: {
    // 使用对象展开运算符将 mutation 方法映射到本地方法
    ...mapMutations([
      'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`
      'decrement', // 将 `this.decrement()` 映射为 `this.$store.commit('decrement')`
      // 其他需要映射的 mutations...
    ]),
    // 你也可以在这里放置其他自定义方法
    customMethod() {
      // ...
    }
  },
  // ...
};
```

一旦这样设置后，你就可以在组件内部调用increment和decrement方法，就像调用组件的任何其他方法一样。这些调用会触发对应的Vuex mutation

如果你想要为这些mutation方法制定不同的本地名称，你可以使用对象形式的mapMutations：

```javascript
methods: {
  // 使用对象形式来给映射的方法重命名
  ...mapMutations({
    add: 'increment',     // 将 `this.add()` 映射为 `this.$store.commit('increment')`
    subtract: 'decrement' // 将 `this.subtract()` 映射为 `this.$store.commit('decrement')`
  }),
  // ...
}
```

在组件模板中，你可以像使用本地方法一样使用它们

```html
<template>
  <button @click="add">Increment</button>
  <button @click="subtract">Decrement</button>
</template>
```

使用mapMutations可以大幅简化组件中对mutation的使用，让代码更简洁，也更易于维护。这对构建大型应用时组织和管理Vuex mutation尤其有帮助



# 六、Vue 3.0

##  1.Vue 3.0有什么更新

1. **监测机制的改变**
  - 3.0带来基于代理Proxy的observer实现，提供全语言覆盖的反应性跟踪
  - 消除了Vue2当中基于Object.defineProperty的实现所存在的很多限制

2. **只能监测属性，不能监测对象**
  - 检测属性的添加和删除
  - 检测数组索引和长度的变更
  - 支持Map、Set、WeakMap和WeakSet


3. **模板**
  - 作用域插槽，2.x的机制导致作用域插槽变了，父组件会重新渲染，而3.0把作用域插槽改成了函数的方式，这样只会影响子组件的重新渲染，提升了渲染的性能
  - 同时，对于render函数的方面，vue3.0也会进行一系列更改来方便习惯直接使用api来生成vdom
4. **对象式的组件声明方式**
  - vue2.x中的组件是通过声明的方式传入一系列option，和TypeScript的结合需要通过一些装饰器的方式来做，虽然能实现功能，但是比较麻烦
  - 3.0修改了组件的声明方式，改成了类式的写法，这样使得和TypeScript的结合变得很容易
5. **其它方面的更改**
  - 支持自定义渲染器，从而使得weex可以通过自定义渲染器的方式来扩展，而不是直接fork源码来改的方式
  - 支持Fragment(多个根节点)和Protal(在dom其他部分渲染组件内容)组件，针对一些特殊的场景做了处理
  - 基于tree shaking优化，提供了更多的内置功能


##  2.defineProperty和proxy的区别？

Object.defineProperty和Proxy是Javascript中两种不同的截取对象属性访问和修改的方法

**Object.defineProperty**

Object.defineProperty方法直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回这个对象。它是在ES5中引入的，大大增强了开发者对对象属性的控制能力

用法示例：
```javascript
const obj = {};
Object.defineProperty(obj, 'property1', {
  value: 42,
  writable: false
});
```

**特点**
- 可以准确地控制属性的行为(是否可写、可枚举、可配置等)
- 只能作用于单个属性

**Proxy**

Proxy是在ES6中引入的，代理可以创建一个对象的代理，从而可以拦截和自定义对象的各种操作：属性访问、属性赋值、枚举、函数调用等

用法示例：
```javascript
const target = {};
const handler = {
  get(target, prop, receiver) {
    return 'Intercepted!';
  }
};
const proxy = new Proxy(target, handler);
console.log(proxy.test); // 输出: Intercepted!
```

**特点**
- 可以拦截和自定义更多的对象行为，如属性访问、属性赋值、枚举、函数调用等
- 不限于作用于单个属性，可以管理整个对象的访问


**主要区别**

- **适用范围：** Object.defineProperty只能定义或修改单个属性的行为，而Proxy可以拦截整个对象的多种操作
- **使用灵活性：** Proxy拥有更丰富的拦截行为，使用时更加灵活。Object.defineProperty需要明确指定每个属性的行为。
- **性能：** 对于相同的操作，Proxy可能比使用Object.defineProperty稍慢，因为Proxy的抽象层更高
- **反应能力：** Proxy可以直接监听对象而非属性，所以可以用来监听动态增加或删除的属性。而通过Object.defineProperty实现类似功能需要预先定义属性


在Vue.js场景下，Vue2.x使用了Object.defineProperty来实现响应式系统。它在对象初始化的时候静态地对属性应用定义，这也意味着Vue2.x不能检测到对象属性的添加或删除。Vue3.x则将内部实现改为使用Proxy，这使得Vue3.x能够更自然地处理对象和数组，并且可以检测到属性的添加和删除。Proxy的使用也使得Vue3.x的响应式系统更加强大和灵活


##  3.Vue3.0 为什么要用 proxy？

Vue3.0使用Proxy是因为Proxy提供了一种更强大和灵活的方式来观察和拦截对象的行为。以下是选择使用Proxy的几个理由：

1. **更好的性能**：使用Proxy代替Vue2.x中的Object.defineProperty方法可以提升性能。Proxy直接对整个对象进行操作，而不需要对对象的每个属性单独设置getter和setter
2. **更好的支持集合类型**：Proxy可以劫持整个对象及其方法，包括数组操作和Map、Set等集合类型。Vue2.x需要特殊的处理方法来监听数组和集合类型的变更，而Proxy作为内建功能，可以更简单地支持这些数据结构
3. **更强大的拦截能力**：Proxy可以拦截更多种类的操作，例如对象属性的读取、设置、枚举、遍历等。这为构建响应式系统提供了更多可能性
4. **动态属性添加的检测**：Proxy可以轻松检测到向响应式对象添加新属性的操作，Vue2.x则无法做到这一点。在Vue2.x中，动态添加的属性需要调用Vue.set方法才能编程响应式的
5. **简化内部实现**：由于Proxy的能力，Vue3.0能够使用更简单和更直接的方式来实现响应式系统，降低了框架自身的复杂性

总的来说，Proxy为构建现代JavaScript框架提供了强大的语言级别的能力，Vue3.0正式利用了Proxy的优势来提升框架的整体功能和性能


##  4.Vue 3.0 中的 Vue Composition API？
Vue Composition API 是Vue3.0中引入的一组新的API，它提供了一种灵活的方式来组织和重用逻辑。与Vue2.x中使用的Options API不同，Composition API不是将组件的逻辑放在生命周期钩子或选项(properties, methods, data, computed等)中，而是基于组合函数组织逻辑

主要特点包括：

1. **逻辑重用与组织**：可以更方便地在组件之间共享代码，不仅限于组件的选项，还包括任何可复用的逻辑
2. **更好的类型推断**：使用Composition API，配合TypeScript使用可以得到更好的类型推断体验
3. **响应式变量**：ref和reactive是Composition API核心函数，用于创建响应式的变量

以下是一些基础的Composition API的实例

**setup()**

setup函数是一个新的组件选项，作为编写组件内部逻辑的入口。它在组件创建之前执行，这也就是你可以使用Composition API的地方

```javascript
import { ref, reactive, onMounted } from 'vue';

export default {
  setup() {
    // 创建一个响应式对象
    const state = reactive({
      count: 0
    });

    // 创建一个响应式值
    const count = ref(0);

    // 生命周期
    onMounted(() => {
      console.log('Component is mounted!');
    });

    // 将响应式数据和函数暴露给模板使用
    return {
      state,
      count
    };
  }
}
```

**ref()和reactive()**

ref和reactive用于声明响应式变量。ref用于处理基础类型(primitives)，返回一个可变的响应式引用对象。reactive则是将一个普通对象转换为一个响应式对象。

**watch()和computed()**

watch API允许你对响应式引用或者响应式对象的变化做出反应。computed创建一个响应式的计算值

**生命周期钩子**

Vue3.0提供了新的生命周期钩子函数来使用，在setup函数中使用，如onMounted、onUpdated和onUnmounted等

Composition API通过这些新特性，提高了Vue中的逻辑复用和代码组织能力，尤其适合构建大型和复杂的应用程序

# 七、虚拟DOM

## 1.对虚拟DOM的理解？
虚拟DOM是一个编程概念，其中UI表示形式保持在内存中，并通过某些库(如React、Vue等)的API与"真实"的DOM(Document Object Model)同步。虚拟DOM提供了一个中间层，这样开发人员就可以不直接操作DOM，而是操作更轻量、更快速的虚拟DOM

以下是虚拟DOM的一些关键点

1. **性能优化**：直接操作DOM是昂贵的。虚拟DOM允许我们在内存中进行所有的操作，最小化实际发生在DOM上的改变
2. **批量更新**：在虚拟DOM中，所有的DOM更新可以被收集并批量执行，避免不必要或冗余的DOM操作。
3. **Diff算法**：当状态变化时，新的虚拟DOM树会与旧的虚拟DOM树进行比较，通过Diff算法计算出实际需要更新的内容
4. **跨平台**：虚拟DOM不依赖于浏览器环境，因此可以用于跨平台的应用，例如React Native

在现代前端框架中，虚拟DOM技术已经变得非常流行，因为它可以极大地提升应用性能和开发效率

##  2.虚拟DOM的解析过程
虚拟DOM的解析过程一般涉及以下几个步骤

1. **创建虚拟DOM**：

当应用的状态发生变化时，框架将根据最新状态生成一个新的虚拟DOM树

2. **Diff比较**：

框架使用Diff算法比较新的虚拟DOM树与上一次渲染时保存的虚拟DOM树，计算出它们之间的差异

3. **生成Patch**：

根据Diff算法的比较结果，框架创建一个"补丁"对象(patch)，它描述了如何通过一系列的DOM更新来修改真实DOM使其与虚拟DOM同步

4. **更新真实DOM**：

框架应用这个"补丁"到真实DOM上，实际上是通过执行DOM操作来对真实DOM进行更新。通常，这些操作是批量且最小化的，以提高性能

5. **渲染到屏幕上**：

一旦DOM更新完成，浏览器将重新渲染屏幕，显示出最新的界面


这个过程允许前端框架仅更新DOM中实际改变了的部分，而不是重建整个DOM树，从而提高了应用程序的性能和响应速度。虽然虚拟DOM增加了额外的计算工作(如虚拟DOM树的创建和Diff算法的比较过程)，但这比直接在DOM上进行大量操作要高效得多，尤其是在复杂的UI中


##  3.为什么要用虚拟DOM

使用虚拟DOM的主要原因是为了提高应用程序的性能和效率。以下是使用虚拟DOM的一些优势

1. **优化DOM操作**

真实的DOM操作很昂贵，因为它涉及到浏览器的重新绘制和重排。虚拟DOM允许我们在内存中处理DOM更新，这比直接操作真实DOM快很多

2. **最小化重绘和重排**

虚拟DOM通过Diff算法识别出实际变更的部分，只对这些部分进行更新，从而减少不必要的DOM修改，提高性能

3. **批量的DOM更新**

变更可以积累起来，然后一次性在真实的DOM中应用，这样可以减轻浏览器的负担，避免多次的重绘和重排

4. **更简洁的状态到视图的映射**

我们可以用简单的JavaScript对象来描述视图的状态，当状态更新时，整个界面都可以自动更新，无需手动操作DOM

5. **跨平台兼容性**

虚拟DOM不依赖特定的平台DOM实现，它可以是服务端渲染、在Web Workers中运行或者在移动端应用中使用，如ReactNative

使用虚拟DOM的前端框架可以提供声明式的UI编程模型，简化开发者在编写应用时对于DOM的操作和管理，使得代码更加易于维护


##  4.DIFF算法的原理
DIFF算法时目前前端框架中用于比较两个虚拟DOM树差异的算法。其目的是以最小的代价更新真实DOM树。它基于两个基本假设

1. 两个相同类型的元素将产生相似的结构
2. 同一层级的一组子节点可以通过唯一的id进行区分

DIFF算法的基本步骤如下：

1. **树的比较**

分别对新旧虚拟DOM树进行逐层比较，即值比较同一层级的节点

2. **节点比较**

当遇到节点类型不同的情况时，直接移除旧节点，替换为新节点

3. **节点更新**

当比较的节点类型相同但是属性不同，更新相应的属性

4. **列表对比**

对于子节点的列表，DIFF算法会使用keys(通常是一个ID或者index)来匹配原有树上的节点和新树上的节点

5. **高效的节点操作**

通过上述步骤计算得到最小数量的DOM操作，只对需要变化的部分进行实际的更新处理


DIFF算法尽量减少遍历的节点数和实际DOM操作的数量，从而提高性能。然而，DIFF算法并不是寻找两棵树之间最小差异的最优算法，因为这在计算上是非常昂贵的（具有NP难度）。相反，前端框架实现的DIFF算法是基于启发式的，尽量平衡效率和执行效率，以便在实际应用中快速且合理地更新DOM。


##  5.Vue中key的作用

在Vue中，key是一种特殊属性，主要用于V-for列表渲染中来给每一个界定啊绑定一个唯一的标识。key的作用主要有以下几点：

1. **高效的列表渲染**：

当Vue.js用V-for渲染列表时，key可以帮助Vue识别节点列表中元素的顺序变化，实现高效的列表更新

2. **重用和重新排列元素**

结合Diff算法，Vue会尝试最大化地重用DOM元素。使用key可以确保元素的身份(和它的状态)在不同的渲染过程中得以保持

3. **避免渲染问题**

在使用组件和转换过渡情况下，不适用key或错误地使用key可能导致渲染错误、状态问题或动画过渡失效

4. **提高性能**

正确使用key可以减少不必要的DOM元素的创建和销毁，从而提升应用性能

通过为列表中的每项元素设置独特的key值，Vue可以追踪每个元素的状态，并在DOM更新时进行有效的元素重用和重新排序，这样能够避免不必要的DOM操作，提高应用的性能。在实践中，常常使用列表中元素的唯一id作为key。


##  6.为什么不建议使用index作为key？
虽然V-for迭代中使用index作为key看似简便，但通常不建议这样做，原因包括：

1. **列表变动导致重绘**

如果数据项的顺序发生变化或数据项被插入/移动，用index作为key可能会导致不必要的元素重新渲染。因为Vue会根据key的变化来判断元素是否在新旧VNode之间保持一致。index作为key时，即使数据项的内容没有变化，由于index变了，Vue也会替换掉原来的元素

2. **状态保持问题**

使用idnex作为key可能会引起状态保持问题，特别是列表内部包含子组件状态(如输入框的值)时。因为key与数据项的内容无关，只与位置相关，当列表变化时，组件状态可能会被错误地关联到不同的数据项上

3. **性能问题**

在某些操作(如列表中元素的排序、添加、删除等)时，使用index作为key不会引起Vue高效利用现有元素的预期行为，而是会进行不必要的DOM操作，从而引发性能下降。

因此，推荐使用唯一标识符（如每项数据的id）作为key，这样可以避免以上问题，确保组件状态的准确性，并且提升列表的渲染性能。