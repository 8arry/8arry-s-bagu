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

