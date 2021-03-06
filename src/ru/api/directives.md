# Директивы

## v-text

- **Ожидает:** `string`

- **Подробности:**

  Updates the element's [textContent](https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent). If you need to update the part of `textContent`, you should use [mustache interpolations](/guide/template-syntax.md#text) instead

- **Пример:**

  ```html
  <span v-text="msg"></span>
  <!-- same as -->
  <span>{{msg}}</span>
  ```

- **См. также:** [Data Binding Syntax - Interpolations](../guide/template-syntax.md#text)

## v-html

- **Ожидает:** `string`

- **Подробности:**

  Updates the element's [innerHTML](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML). **Note that the contents are inserted as plain HTML - they will not be compiled as Vue templates**. If you find yourself trying to compose templates using `v-html`, try to rethink the solution by using components instead.

  :::warning ВНИМАНИЕ
  Dynamically rendering arbitrary HTML on your website can be very dangerous because it can easily lead to [XSS attacks](https://en.wikipedia.org/wiki/Cross-site_scripting). Only use `v-html` on trusted content and **never** on user-provided content.
  :::

  In [single-file components](../guide/single-file-component.md), `scoped` styles will not apply to content inside `v-html`, because that HTML is not processed by Vue's template compiler. If you want to target `v-html` content with scoped CSS, you can instead use [CSS modules](https://vue-loader.vuejs.org/ru/features/css-modules.html) or an additional, global `<style>` element with a manual scoping strategy such as BEM.

- **Пример:**

  ```html
  <div v-html="html"></div>
  ```

- **См. также:** [Data Binding Syntax - Interpolations](../guide/template-syntax.md#raw-html)

## v-show

- **Ожидает:** `any`

- **Использование:**

  Toggles the element's `display` CSS property based on the truthy-ness of the expression value.

  This directive triggers transitions when its condition changes.

- **См. также:** [Conditional Rendering - v-show](../guide/conditional.md#v-show)

## v-if

- **Ожидает:** `any`

- **Использование:**

  Conditionally render the element based on the truthy-ness of the expression value. The element and its contained directives / components are destroyed and re-constructed during toggles. If the element is a `<template>` element, its content will be extracted as the conditional block.

  This directive triggers transitions when its condition changes.

  When used together with `v-if`, `v-for` has a higher priority than v-if. See the [list rendering guide](../guide/list.md#v-for-with-v-if) for details.

- **См. также:** [Conditional Rendering - v-if](../guide/conditional.md#v-if)

## v-else

- **Does not expect expression**

- **Restriction:** previous sibling element must have `v-if` or `v-else-if`.

- **Использование:**

  Denote the "else block" for `v-if` or a `v-if`/`v-else-if` chain.

  ```html
  <div v-if="Math.random() > 0.5">
    Now you see me
  </div>
  <div v-else>
    Now you don't
  </div>
  ```

- **См. также:** [Conditional Rendering - v-else](../guide/conditional.md#v-else)

## v-else-if

- **Ожидает:** `any`

- **Restriction:** previous sibling element must have `v-if` or `v-else-if`.

- **Использование:**

  Denote the "else if block" for `v-if`. Can be chained.

  ```html
  <div v-if="type === 'A'">
    A
  </div>
  <div v-else-if="type === 'B'">
    B
  </div>
  <div v-else-if="type === 'C'">
    C
  </div>
  <div v-else>
    Not A/B/C
  </div>
  ```

- **См. также:** [Conditional Rendering - v-else-if](../guide/conditional.md#v-else-if)

## v-for

- **Ожидает:** `Array | Object | number | string | Iterable`

- **Использование:**

  Render the element or template block multiple times based on the source data. The directive's value must use the special syntax `alias in expression` to provide an alias for the current element being iterated on:

  ```html
  <div v-for="item in items">
    {{ item.text }}
  </div>
  ```

  Alternatively, you can also specify an alias for the index (or the key if used on an Object):

  ```html
  <div v-for="(item, index) in items"></div>
  <div v-for="(value, key) in object"></div>
  <div v-for="(value, name, index) in object"></div>
  ```

  The default behavior of `v-for` will try to patch the elements in-place without moving them. To force it to reorder elements, you should provide an ordering hint with the `key` special attribute:

  ```html
  <div v-for="item in items" :key="item.id">
    {{ item.text }}
  </div>
  ```

  `v-for` can also work on values that implement the [Iterable Protocol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#The_iterable_protocol), including native `Map` and `Set`.

  The detailed usage for `v-for` is explained in the guide section linked below.

- **См. также:**
  - [List Rendering](../guide/list.md)

## v-on

- **Shorthand:** `@`

- **Ожидает:** `Function | Inline Statement | Object`

- **Argument:** `event`

- **Modifiers:**

  - `.stop` - call `event.stopPropagation()`.
  - `.prevent` - call `event.preventDefault()`.
  - `.capture` - add event listener in capture mode.
  - `.self` - only trigger handler if event was dispatched from this element.
  - `.{keyAlias}` - only trigger handler on certain keys.
  - `.once` - trigger handler at most once.
  - `.left` - only trigger handler for left button mouse events.
  - `.right` - only trigger handler for right button mouse events.
  - `.middle` - only trigger handler for middle button mouse events.
  - `.passive` - attaches a DOM event with `{ passive: true }`.

- **Использование:**

  Attaches an event listener to the element. The event type is denoted by the argument. The expression can be a method name, an inline statement, or omitted if there are modifiers present.

  When used on a normal element, it listens to [**native DOM events**](https://developer.mozilla.org/en-US/docs/Web/Events) only. When used on a custom element component, it listens to **custom events** emitted on that child component.

  When listening to native DOM events, the method receives the native event as the only argument. If using inline statement, the statement has access to the special `$event` property: `v-on:click="handle('ok', $event)"`.

  `v-on` also supports binding to an object of event/listener pairs without an argument. Note when using the object syntax, it does not support any modifiers.

- **Пример:**

  ```html
  <!-- method handler -->
  <button v-on:click="doThis"></button>

  <!-- dynamic event -->
  <button v-on:[event]="doThis"></button>

  <!-- inline statement -->
  <button v-on:click="doThat('hello', $event)"></button>

  <!-- shorthand -->
  <button @click="doThis"></button>

  <!-- shorthand dynamic event (2.6.0+) -->
  <button @[event]="doThis"></button>

  <!-- stop propagation -->
  <button @click.stop="doThis"></button>

  <!-- prevent default -->
  <button @click.prevent="doThis"></button>

  <!-- prevent default without expression -->
  <form @submit.prevent></form>

  <!-- chain modifiers -->
  <button @click.stop.prevent="doThis"></button>

  <!-- key modifier using keyAlias -->
  <input @keyup.enter="onEnter" />

  <!-- the click event will be triggered at most once -->
  <button v-on:click.once="doThis"></button>

  <!-- object syntax -->
  <button v-on="{ mousedown: doThis, mouseup: doThat }"></button>
  ```

  Listening to custom events on a child component (the handler is called when "my-event" is emitted on the child):

  ```html
  <my-component @my-event="handleThis"></my-component>

  <!-- inline statement -->
  <my-component @my-event="handleThis(123, $event)"></my-component>
  ```

- **См. также:**
  - [Event Handling](../guide/events.md)
  - [Components - Custom Events](../guide/component-basics.md#listening-to-child-components-events)

## v-bind

- **Shorthand:** `:`

- **Ожидает:** `any (with argument) | Object (without argument)`

- **Argument:** `attrOrProp (optional)`

- **Modifiers:**

  - `.camel` - transform the kebab-case attribute name into camelCase.

- **Использование:**

  Dynamically bind one or more attributes, or a component prop to an expression.

  When used to bind the `class` or `style` attribute, it supports additional value types such as Array or Objects. See linked guide section below for more details.

  When used for prop binding, the prop must be properly declared in the child component.

  When used without an argument, can be used to bind an object containing attribute name-value pairs. Note in this mode `class` and `style` does not support Array or Objects.

- **Пример:**

  ```html
  <!-- bind an attribute -->
  <img v-bind:src="imageSrc" />

  <!-- dynamic attribute name -->
  <button v-bind:[key]="value"></button>

  <!-- shorthand -->
  <img :src="imageSrc" />

  <!-- shorthand dynamic attribute name -->
  <button :[key]="value"></button>

  <!-- with inline string concatenation -->
  <img :src="'/path/to/images/' + fileName" />

  <!-- class binding -->
  <div :class="{ red: isRed }"></div>
  <div :class="[classA, classB]"></div>
  <div :class="[classA, { classB: isB, classC: isC }]">
    <!-- style binding -->
    <div :style="{ fontSize: size + 'px' }"></div>
    <div :style="[styleObjectA, styleObjectB]"></div>

    <!-- binding an object of attributes -->
    <div v-bind="{ id: someProp, 'other-attr': otherProp }"></div>

    <!-- prop binding. "prop" must be declared in my-component. -->
    <my-component :prop="someThing"></my-component>

    <!-- pass down parent props in common with a child component -->
    <child-component v-bind="$props"></child-component>

    <!-- XLink -->
    <svg><a :xlink:special="foo"></a></svg>
  </div>
  ```

  The `.camel` modifier allows camelizing a `v-bind` attribute name when using in-DOM templates, e.g. the SVG `viewBox` attribute:

  ```html
  <svg :view-box.camel="viewBox"></svg>
  ```

  `.camel` is not needed if you are using string templates, or compiling with `vue-loader`/`vueify`.

- **См. также:**
  - [Class and Style Bindings](../guide/class-and-style.md)
  - [Components - Props](../guide/component-basics.md#passing-data-to-child-components-with-props)

## v-model

- **Ожидает:** varies based on value of form inputs element or output of components

- **Limited to:**

  - `<input>`
  - `<select>`
  - `<textarea>`
  - components

- **Modifiers:**

  - [`.lazy`](../guide/forms.md#lazy) - listen to `change` events instead of `input`
  - [`.number`](../guide/forms.md#number) - cast valid input string to numbers
  - [`.trim`](../guide/forms.md#trim) - trim input

- **Использование:**

  Create a two-way binding on a form input element or a component. For detailed usage and other notes, see the Guide section linked below.

- **См. также:**
  - [Form Input Bindings](../guide/forms.md)
  - [Components - Form Input Components using Custom Events](../guide/component-custom-events.md#v-model-arguments)

## v-slot

- **Shorthand:** `#`

- **Ожидает:** JavaScript expression that is valid in a function argument position (supports destructuring in [supported environments](../guide/component-slots.md#destructuring-slot-props)). Optional - only needed if expecting props to be passed to the slot.

- **Argument:** slot name (optional, defaults to `default`)

- **Limited to:**

  - `<template>`
  - [components](../guide/component-slots.md#abbreviated-syntax-for-lone-default-slots) (for a lone default slot with props)

- **Использование:**

  Denote named slots or slots that expect to receive props.

- **Пример:**

  ```html
  <!-- Named slots -->
  <base-layout>
    <template v-slot:header>
      Header content
    </template>

    <template v-slot:default>
      Default slot content
    </template>

    <template v-slot:footer>
      Footer content
    </template>
  </base-layout>

  <!-- Named slot that receives props -->
  <infinite-scroll>
    <template v-slot:item="slotProps">
      <div class="item">
        {{ slotProps.item.text }}
      </div>
    </template>
  </infinite-scroll>

  <!-- Default slot that receive props, with destructuring -->
  <mouse-position v-slot="{ x, y }">
    Mouse position: {{ x }}, {{ y }}
  </mouse-position>
  ```

  For more details, see the links below.

- **См. также:**
  - [Components - Slots](../guide/component-slots.md)

## v-pre

- **Does not expect expression**

- **Использование:**

  Skip compilation for this element and all its children. You can use this for displaying raw mustache tags. Skipping large numbers of nodes with no directives on them can also speed up compilation.

- **Пример:**

  ```html
  <span v-pre>{{ this will not be compiled }}</span>
  ```

## v-cloak

- **Does not expect expression**

- **Использование:**

  This directive will remain on the element until the associated component instance finishes compilation. Combined with CSS rules such as `[v-cloak] { display: none }`, this directive can be used to hide un-compiled mustache bindings until the component instance is ready.

- **Пример:**

  ```css
  [v-cloak] {
    display: none;
  }
  ```

  ```html
  <div v-cloak>
    {{ message }}
  </div>
  ```

  The `<div>` will not be visible until the compilation is done.

## v-once

- **Does not expect expression**

- **Подробности:**

  Render the element and component **once** only. On subsequent re-renders, the element/component and all its children will be treated as static content and skipped. This can be used to optimize update performance.

  ```html
  <!-- single element -->
  <span v-once>This will never change: {{msg}}</span>
  <!-- the element have children -->
  <div v-once>
    <h1>comment</h1>
    <p>{{msg}}</p>
  </div>
  <!-- component -->
  <my-component v-once :comment="msg"></my-component>
  <!-- `v-for` directive -->
  <ul>
    <li v-for="i in list" v-once>{{i}}</li>
  </ul>
  ```

- **См. также:**
  - [Data Binding Syntax - interpolations](../guide/template-syntax.md#text)

## v-is

> Note: this section only affects cases where Vue templates are directly written in the page's HTML.

- **Ожидает:** string literal

- **Limited to:** native HTML elements

- **Использование:** When using in-DOM templates, the template is subject to native HTML parsing rules. Some HTML elements, such as `<ul>`, `<ol>`, `<table>` and `<select>` have restrictions on what elements can appear inside them, and some elements such as `<li>`, `<tr>`, and `<option>` can only appear inside certain other elements. As a workaround, we can use `v-is` directive on these elements:

```html
<table>
  <tr v-is="'blog-post-row'"></tr>
</table>
```

:::warning ВНИМАНИЕ
`v-is` functions like a dynamic 2.x `:is` binding - so to render a component by its registered name, its value should be a JavaScript string literal:

```html
<!-- Incorrect, nothing will be rendered -->
<tr v-is="blog-post-row"></tr>

<!-- Correct -->
<tr v-is="'blog-post-row'"></tr>
```

:::
