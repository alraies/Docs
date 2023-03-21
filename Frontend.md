
# Frontend Guide

We have decided to use **Vue 3** as our frontend framework with **TypeScript** due to its many benefits. Vue 3 offers improved performance and smaller bundle size, a Composition API for more flexible and reusable code, and better TypeScript support. By using TypeScript, we can catch errors early, ensure code correctness, and write more maintainable code. With Vue 3 and TypeScript, we can create scalable and maintainable web applications that are easy to develop and maintain over time.
##

In addition to using Vue 3 and TypeScript, we have decided to use the [**Vuexy**](https://demos.pixinvent.com/vuexy-vuejs-admin-template/demo-1/) frontend theme and [**DevExtreme**](https://js.devexpress.com/Overview/Vue/) components with our projects. Vuexy is a highly customizable and feature-rich Vue.js admin dashboard template that provides many pre-built components and layouts for building web applications quickly. It also offers a clean and modern design, making our applications look and feel professional. DevExtreme is a set of responsive and feature-rich UI components for Vue.js, including grids, charts, forms, and more. By using DevExtreme, we can add powerful and responsive UI components to our applications quickly and easily. Together, Vuexy and DevExtreme help us to create powerful and visually appealing web applications that meet our clients' needs.

we have a **Vue Mastery** account so you can check the basic usage of Vue 3, and know the fundamentals throw many courses for intermediate to professional, feel free to use it any time, we will show some most used features and how to use it .

# Vue 3 Developer's Guide
This guide is intended for developers who want to learn Vue 3 and write clean, reusable, and maintainable code. In this guide, we will cover the basics of Vue 3, advanced usage, how to write clean code, how to use reusable components, i18n localization, and how to separate API services from Vue components.

## <script setup>
**<script setup>** is a compile-time syntactic sugar for using Composition API inside Single-File Components (SFCs). It is the recommended syntax if you are using both SFCs and Composition API. It provides a number of advantages over the normal **<script>** syntax:

More succinct code with less boilerplate
Ability to declare props and emitted events using pure TypeScript
Better runtime performance (the template is compiled into a render function in the same scope, without an intermediate proxy)
Better IDE type-inference performance (less work for the language server to extract types from code)
## Composition API: Lifecycle Hooks :

### onMounted() 
Registers a callback to be called after the component has been mounted.
```
function onMounted(callback: () => void): void
```
- Example :
Accessing an element via template ref:
```
<script setup>
import { ref, onMounted } from 'vue'

const el = ref()

onMounted(() => {
  el.value // <div>
})
</script>

<template>
  <div ref="el"></div>
</template>
```
### onUpdated()
Registers a callback to be called after the component has updated its DOM tree due to a reactive state change.

A parent component's updated hook is called after that of its child components.

This hook is called after any DOM update of the component, which can be caused by different state changes. If you need to access the updated DOM after a specific state change, use nextTick() instead.

**This hook is not called during server-side rendering.**
```
WARNING

Do not mutate component state in the updated hook - this will likely lead to an infinite update loop!
```
- Example :
```
<script setup>
import { ref, onUpdated } from 'vue'

const count = ref(0)

onUpdated(() => {
  // text content should be the same as current `count.value`
  console.log(document.getElementById('count').textContent)
})
</script>

<template>
  <button id="count" @click="count++">{{ count }}</button>
</template>

```
other like ``onUnmounted()`` , ``onBeforeMount()``, ``onBeforeUpdate()``, ``onBeforeUnmount()``, ``onErrorCaptured()``, ``onActivated()`` ..etc in [Composition API: Lifecycle Hooks](https://vuejs.org/api/composition-api-lifecycle.html)
## Attribute Binding

The most basic form of data binding is text interpolation using the "Mustache" syntax (double curly braces):
``` html
<script setup>
// variable
const msg = 'Hello!'

// functions
function log() {
  console.log(msg)
}
</script>

<template>
  <button @click="log">{{ msg }}</button>
</template>
```
The mustache tag will be replaced with the value of the msg property from the corresponding component instance. It will also be updated whenever the msg property changes.

#### Mustaches cannot be used inside HTML attributes. Instead, use a v-bind directive:
```
<div :id="dynamicId"></div>
```

Attributes that start with : may look a bit different from normal HTML, but it is in fact a valid character for attribute names and all Vue-supported browsers can parse it correctly. In addition, they do not appear in the final rendered markup. The shorthand syntax is optional, but you will likely appreciate it when you learn more about its usage later.

#### Using JavaScript Expressions:
```
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div :id="`list-${id}`"></div>
```
## Directives:
Directives are special attributes with the v- prefix. Vue provides a number of built-in directives, including v-html and v-bind which we have introduced above.

Directive attribute values are expected to be single JavaScript expressions (with the exception of v-for, v-on and v-slot, which will be discussed in their respective sections later). A directive's job is to reactively apply updates to the DOM when the value of its expression changes. Take v-if as an example:
```
<p v-if="seen">Now you see me</p>
```
Here, the v-if directive would remove / insert the <p> element based on the truthiness of the value of the expression seen.


### Reactivity Fundamentals :
#### Declaring Reactive State
We can create a reactive object or array with the reactive() function:
```js 
import { reactive } from 'vue'

const state = reactive({ count: 0 })

```
Reactive objects are [JavaScript Proxies](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) and behave just like normal objects. The difference is that Vue is able to track the property access and mutations of a reactive object.

## ref() 
Takes an inner value and returns a reactive and mutable ref object, which has a single property .value that points to the inner value.

To use reactive state in a component's template, declare and return them from a component's setup() function:
```
<script setup>
import { ref } from 'vue'

const count = ref(0)
</script>

<template>
  <button @click="count++">{{ count }}</button>
</template>


```

### Using Components :
Values in the scope of <script setup> can also be used directly as custom component tag names:
```
<script setup>
import MyComponent from './MyComponent.vue'
</script>

<template>
  <MyComponent />
</template>
```

### Dynamic Components :

Since components are referenced as variables instead of registered under string keys, we should use dynamic **:is** binding when using dynamic components inside **<script setup>**:
```
<script setup>
import Foo from './Foo.vue'
import Bar from './Bar.vue'
</script>

<template>
  <component :is="Foo" />
  <component :is="someCondition ? Foo : Bar" />
</template>
```

### Namespaced Components :
You can use component tags with dots like <Foo.Bar> to refer to components nested under object properties. This is useful when you import multiple components from a single file:
```
<script setup>
import * as Form from './form-components'
</script>

<template>
  <Form.Input>
    <Form.Label>label</Form.Label>
  </Form.Input>
</template>
```
### defineProps() & defineEmits() :

To declare options like props and emits with full type inference support, we can use the defineProps and defineEmits APIs, which are automatically available inside <script setup>:
```ts
<script setup>
const props = defineProps<{
  foo: string
  bar?: number
}>()

const emit = defineEmits<{
  (e: 'change', id: number): void
  (e: 'update', value: string): void
}>()

</script>
```
- ``defineProps`` and defineEmits are compiler macros only usable inside ``<script setup>``. They do not need to be imported, and are compiled away when <script setup> is processed.

- ``defineProps`` accepts the same value as the props option, while defineEmits accepts the same value as the emits option.

- ``defineProps`` and defineEmits provide proper type inference based on the options passed.

- The options passed to ``defineProps`` and defineEmits will be hoisted out of setup into module scope. Therefore, the options cannot reference local variables declared in setup scope. Doing so will result in a compile error. However, it can reference imported bindings since they are in the module scope as well.

### Default props values when using type declaration:
One drawback of the type-only defineProps declaration is that it doesn't have a way to provide default values for the props. To resolve this problem, a withDefaults compiler macro is also provided:
```ts
export interface Props {
  msg?: string
  labels?: string[]
}

const props = withDefaults(defineProps<Props>(), {
  msg: 'hello',
  labels: () => ['one', 'two']
})
```
This will be compiled to equivalent runtime props ``default`` options. In addition, the ``withDefaults`` helper provides type checks for the default values, and ensures the returned ``props`` type has the optional flags removed for properties that do have default values declared.

### defineExpose() 

Components using ``<script setup>`` are closed **by default** - i.e. the public instance of the component, which is retrieved via template refs or $parent chains, will not expose any of the bindings declared inside <script setup>.

To explicitly expose properties in a <script setup> component, use the ``defineExpose`` compiler macro:
```vue
<script setup>
import { ref } from 'vue'

const a = 1
const b = ref(2)

defineExpose({
  a,
  b
})
</script>
```

### useSlots() & useAttrs() :

Usage of ``slots`` and ``attrs`` inside ``<script setup>`` should be relatively rare, since you can access them directly as ``$slots`` and ``$attrs`` in the template. In the rare case where you do need them, use the useSlots and useAttrs helpers respectively:

```
<script setup>
import { useSlots, useAttrs } from 'vue'

const slots = useSlots()
const attrs = useAttrs()
</script>
```
``useSlots`` and ``useAttrs`` are actual runtime functions that return the equivalent of ``setupContext.slots`` and ``setupContext.attrs``. They can be used in normal composition API functions as well.

### Top-level await :

Top-level await can be used inside <script setup>. The resulting code will be compiled as async setup():
```
<script setup>
onmounted(){
    const post = await fetch(`/api/post/1`).then((r) => r.json())
}
</script>
```
## Scoped CSS :

When a <style> tag has the scoped attribute, its CSS will apply to elements of the current component only. This is similar to the style encapsulation found in Shadow DOM. It comes with some caveats, but doesn't require any polyfills. It is achieved by using PostCSS to transform the following:
```
<style scoped>
.example {
  color: red;
}
</style>

<template>
  <div class="example">hi</div>
</template>
```
# Utility Types :

###  PropType<T> 
Used to annotate a prop with more advanced types when using runtime props declarations.
```
import type { PropType } from 'vue'

interface Book {
  title: string
  author: string
  year: number
}

export default {
  props: {
    book: {
      // provide more specific type to `Object`
      type: Object as PropType<Book>,
      required: true
    }
  }
}
```
## Watchers

### Basic Example :
Computed properties allow us to declaratively compute derived values. However, there are cases where we need to perform "side effects" in reaction to state changes - for example, mutating the DOM, or changing another piece of state based on the result of an async operation.

With Composition API, we can use the ``watch`` function to trigger a callback whenever a piece of reactive state changes:
```
<script setup>
import { ref, watch } from 'vue'

const question = ref('')
const answer = ref('Questions usually contain a question mark. ;-)')

// watch works directly on a ref
watch(question, async (newQuestion, oldQuestion) => {
  if (newQuestion.indexOf('?') > -1) {
    answer.value = 'Thinking...'
    try {
      const res = await fetch('https://yesno.wtf/api')
      answer.value = (await res.json()).answer
    } catch (error) {
      answer.value = 'Error! Could not reach the API. ' + error
    }
  }
})
</script>

<template>
  <p>
    Ask a yes/no question:
    <input v-model="question" />
  </p>
  <p>{{ answer }}</p>
</template>
```
## Template Refs

While Vue's declarative rendering model abstracts away most of the direct DOM operations for you, there may still be cases where we need direct access to the underlying DOM elements. To achieve this, we can use the special ref attribute:

```
<script setup>
import { ref, onMounted } from 'vue'

// declare a ref to hold the element reference
// the name must match template ref value
const input = ref(null)

onMounted(() => {
  input.value.focus()
})
</script>

<template>
  <input ref="input" />
</template>

```
ref is a special attribute, similar to the key attribute discussed in the v-for chapter. It allows us to obtain a direct reference to a specific DOM element or child component instance after it's mounted. This may be useful when you want to, for example, programmatically focus an input on component mount, or initialize a 3rd party library on an element.

## Components Basics

Components allow us to split the UI into independent and reusable pieces, and think about each piece in isolation. It's common for an app to be organized into a tree of nested components:
![Componentes](https://vuejs.org/assets/components.7fbb3771.png)

### Defining a Component
When using a build step, we typically define each Vue component in a dedicated file using the ``.vue`` extension - known as a [Single-File Component](https://vuejs.org/guide/scaling-up/sfc.html) (SFC for short):
```
<script setup>
import { ref } from 'vue'

const count = ref(0)
</script>

<template>
  <button @click="count++">You clicked me {{ count }} times.</button>
</template>
```
### Using a Component

To use a child component, we need to import it in the parent component. Assuming we placed our counter component inside a file called ButtonCounter.vue, the component will be exposed as the file's default export:
```
<script setup>
import ButtonCounter from './ButtonCounter.vue'
</script>

<template>
  <h1>Here is a child component!</h1>
  <ButtonCounter />
</template>
```

rest of document is [here](https://vuejs.org/guide/essentials/component-basics.html)

# Vue.JS Best Practices :

## 1-  Always Use Key ``V-for`` With ``: Key`` Inside:
- While we use the “``key``” attribute in the “``v-for``” directive section, it will always help the Vue app to be constant and the data can be manipulated whenever we want. It can also be used to identify the elements in a list that can be easily updated. An example of this “Keys” is the most used for HTML lists, animations, and Vue transitions.As an example, we can say without the use of the “``:key``”, DOM will not update the UI properly. It will get confused about which record to update when duplicate records exist in the list. So, if we want to update the last item from the list, the code will always update the first item if we don’t use the “``:key``”.

## 2-  Use Pascal Case for Components :

```
# Avoid
itemcomponent.vue || itemComponent.vue || Itemcomponenrt.vue
 
# Good Practice
ItemComponent.vue
```

## 3- Used Suitable Data Type Assign for Variable:
- Continuously use proper data types instead of “any” to minimize the casting time and other casting errors.
- Avoid any casting inside the loops.
- In a case of two data types assigned to the same property, implement type casting using both the types and using conditions.
Example :
```
// Wrong
const age: any = 0; [hard to identify response type at the places this variable used and also hard to track errors on assignments]
 
// Right
const age: number = 0; [easy to track and identify response type]
```
## 4- Data Property Initialization :

- It is recommended to initialize all the data properties that need to be reactive in advance in the data option.
- Vue.js constantly observes the change in data by recursively running through data parts.
- Use the Watch prop to get the latest values depending on another instead of creating multiple getters and setters. (Avoid the use of a watch inside the array of objects).
- Avoid memory leaks – remove custom events, instances, intervals when base components are destroyed.

## 5- Do Not Mix v-if and v-for :
- In Vue, You don’t use v-if on the same element as v-for. While Vue.js compiles the template, it checks all time v-if conditions. It is time-consuming. Instead, we can apply the V-if condition to the parent tag or template to achieve the same.
Let see one example,
```
<ul>
    <li v-if="itemList && itemList.length > 0">	  
	v-for="(item, index) in itemList" 
	:key="item.id"
	@click="deleteItem(index)">
	{ item }}
    </li>
</ul>
```
In the above code, we will be able to check if the condition resides in v-if all time while rendering the list or not.
```
<ul v-if="itemList && itemList.length > 0">
     <li v-if="itemList && itemList.length > 0" v-for="(item, index) in itemList" :key="item.id" @click="deleteItem(index)">
	    {{ item }}
    </li>
 </ul>
 ```
 **Here, v-if applied to ul tag, so the v-if condition will be checked only once before rendering v-for items**

 ## 6- Clean Code and Refactoring :

- Use a shared/separate file for the static functions/properties for re-usability. It will be helpful to keep a shared/static code at the same file in the entire solution.
- Use eslint or tslint analysis tools to maintain code quality.
- In Vue, you can reduce the line of code by narrowing down the UI into smaller components.
- Use keywords (Const/Let) provided by typescript smartly instead of the var keyword of javascript.

## 7- Define and Validate the Props :
Undoubtedly, the most crucial best practices to follow are for Vuejs. If you ask me what its significance is then the answer is there are many reasons for this. It essentially protects the future you from the current you. It’s easy to lose track of the exact format, type, and other conventions you used for a prop when working on a huge project.

If you’re part of a larger development team, then you must communicate well, so as to make sure they understand how to use your components. So, please just provide prop validations. You need to avoid challenges and meticulously track all your components to ascertain a prop’s formatting. Check out this example from the Vue documentation.

```
props: {
  status: {
    type: String,
    required: true,
    validator: function (value) {
      return [
        'syncing',
        'synced',
        'version-conflict',
        'error'
      ].indexOf(value) !== -1
    }
  }
}
```
## 8 -Components Declared and Used ONCE Should Have The Prefix “The”
These are components that are unique to your programme, such as a header, sidebar, or footer. For every component, there should only be one active instance.

```
TheHeader.vue
TheFooter.vue
TheSidebar.vue
ThePopup.vue
```

## 9 -Don’t Call a Method on Created AND Watch:
For no reason, if you call a method that is created and then monitored is a common mistake Vue developers make . The idea is that the watch hook should be called as soon as a component is initialised.

**BAD!**
```
created: () {
  this.handlePropertyChange()
},
methods: {
  handlePropertyChange() {
    // stuff happens
  }
},
watch () {
  property() {
    this.handlePropertyChange()
  }
}
```
Vue, on the other hand, offers a predefined in-built solution for this. It’s a feature of Vue viewers that we often overlook.

All we need to do now is reorganize our watcher and declare two properties:

handler (newVal, oldVal) – here is the actual observer method:
true – when our instance is created, our handler is called.
**GOOD!**
```
methods: {0
  handlePropertyChange() {
    // stuff happens
  }
},
watch () {
  property {
    immediate: true
    handler() {
      this.handlePropertyChange()
    }
  }
}
```

## 10- Multiple V-Condition :
Using numerous v-if conditions to render multiple elements from a Vue component’s render method is not recommended. Wrap the elements with <div > and the extra ones with <template >.
```
< template v-if=“true” >
 < p >Paragraph 1 < / p >
 < p >Paragraph 2 < / p >
 < p >Paragraph 3 < / p >
< / template >
```
## 11- Use of Third-party Packages :
The more you use third-party packages  in your project, there can be security issues rising as vulnerabilities in open-source libraries. This will instigate hackers to do more fraud. While selecting Vue.js components from the package to integrate with the application, the expert Vue.js developer needs to check if the library is open source or not as lack of transparency poses a security risk, security best practices is where the library is well-documented, recommended, meets specific requirements, and is actively supported by the author.

. 

. 

``//TODO``
## Authors

- [@AbdMakktof](https://github.com/alraies)


## Contributing

Contributions are always welcome!

See `contributing.md` for ways to get started. ``//TODO``

Please adhere to this project's `code of conduct`.


## Appendix

Any additional information goes here


## Badges



[![MIT License](https://img.shields.io/badge/@-Muraba-green)](https://choosealicense.com/licenses/mit/)

