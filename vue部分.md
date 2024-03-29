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



