# List Transitions

So far, we've managed transitions for:

- Individual nodes
- Multiple nodes where only 1 is rendered at a time

So what about for when we have a whole list of items we want to render simultaneously, for example with `v-for`? In this case, we'll use the `<transition-group>` component. Before we dive into an example though, there are a few things that are important to know about this component:

- Unlike `<transition>`, it renders an actual element: a `<span>` by default. You can change the element that's rendered with the `tag` attribute.
- [Transition modes](/guide/transitions-enterleave#transition-modes) are not available, because we are no longer alternating between mutually exclusive elements.
- Elements inside are **always required** to have a unique `key` attribute.
- CSS transition classes will be applied to inner elements and not to the group/container itself.

### List Entering/Leaving Transitions

Now let's dive into an example, transitioning entering and leaving using the same CSS classes we've used previously:

```html
<div id="list-demo">
  <button @click="add">Add</button>
  <button @click="remove">Remove</button>
  <transition-group name="list" tag="p">
    <span v-for="item in items" :key="item" class="list-item">
      {{ item }}
    </span>
  </transition-group>
</div>
```

```js
const Demo = {
  data() {
    return {
      items: [1, 2, 3, 4, 5, 6, 7, 8, 9],
      nextNum: 10
    }
  },
  methods: {
    randomIndex() {
      return Math.floor(Math.random() * this.items.length)
    },
    add() {
      this.items.splice(this.randomIndex(), 0, this.nextNum++)
    },
    remove() {
      this.items.splice(this.randomIndex(), 1)
    }
  }
}

Vue.createApp(Demo).mount('#list-demo')
```

```css
.list-item {
  display: inline-block;
  margin-right: 10px;
}
.list-enter-active,
.list-leave-active {
  transition: all 1s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateY(30px);
}
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="js,result" data-user="Vue" data-slug-hash="e1cea580e91d6952eb0ae17bfb7c379d" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Transition List">
  <span>See the Pen <a href="https://codepen.io/team/Vue/pen/e1cea580e91d6952eb0ae17bfb7c379d">
  Transition List</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

There's one problem with this example. When you add or remove an item, the ones around it instantly snap into their new place instead of smoothly transitioning. We'll fix that later.

### List Move Transitions

The `<transition-group>` component has another trick up its sleeve. It can not only animate entering and leaving, but also changes in position. The only new concept you need to know to use this feature is the addition of **the `v-move` class**, which is added when items are changing positions. Like the other classes, its prefix will match the value of a provided `name` attribute and you can also manually specify a class with the `move-class` attribute.

This class is mostly useful for specifying the transition timing and easing curve, as you'll see below:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.15/lodash.min.js"></script>

<div id="flip-list-demo">
  <button @click="shuffle">Shuffle</button>
  <transition-group name="flip-list" tag="ul">
    <li v-for="item in items" :key="item">
      {{ item }}
    </li>
  </transition-group>
</div>
```

```js
const Demo = {
  data() {
    return {
      items: [1, 2, 3, 4, 5, 6, 7, 8, 9]
    }
  },
  methods: {
    shuffle() {
      this.items = _.shuffle(this.items)
    }
  }
}

Vue.createApp(Demo).mount('#flip-list-demo')
```

```css
.flip-list-move {
  transition: transform 0.8s ease;
}
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="html,result" data-user="Vue" data-slug-hash="049211673d3c185fde6b6eceb8baebec" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Transition-group example">
  <span>See the Pen <a href="https://codepen.io/team/Vue/pen/049211673d3c185fde6b6eceb8baebec">
  Transition-group example</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

This might seem like magic, but under the hood, Vue is using an animation technique called [FLIP](https://aerotwist.com/blog/flip-your-animations/) to smoothly transition elements from their old position to their new position using transforms.

We can combine this technique with our previous implementation to animate every possible change to our list!

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.14.1/lodash.min.js"></script>

<div id="list-complete-demo" class="demo">
  <button @click="shuffle">Shuffle</button>
  <button @click="add">Add</button>
  <button @click="remove">Remove</button>
  <transition-group name="list-complete" tag="p">
    <span v-for="item in items" :key="item" class="list-complete-item">
      {{ item }}
    </span>
  </transition-group>
</div>
```

```js
const Demo = {
  data() {
    return {
      items: [1, 2, 3, 4, 5, 6, 7, 8, 9],
      nextNum: 10
    }
  },
  methods: {
    randomIndex() {
      return Math.floor(Math.random() * this.items.length)
    },
    add() {
      this.items.splice(this.randomIndex(), 0, this.nextNum++)
    },
    remove() {
      this.items.splice(this.randomIndex(), 1)
    },
    shuffle() {
      this.items = _.shuffle(this.items)
    }
  }
}

Vue.createApp(Demo).mount('#list-complete-demo')
```

```css
.list-complete-item {
  transition: all 0.8s ease;
  display: inline-block;
  margin-right: 10px;
}

.list-complete-enter-from,
.list-complete-leave-to {
  opacity: 0;
  transform: translateY(30px);
}

.list-complete-leave-active {
  position: absolute;
}
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="js,result" data-user="Vue" data-slug-hash="373b4429eb5769ae2e6d097fd954fd08" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Transition-group example">
  <span>See the Pen <a href="https://codepen.io/team/Vue/pen/373b4429eb5769ae2e6d097fd954fd08">
  Transition-group example</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

:::tip Совет
One important note is that these FLIP transitions do not work with elements set to `display: inline`. As an alternative, you can use `display: inline-block` or place elements in a flex context.
:::

These FLIP animations are also not limited to a single axis. Items in a multidimensional grid can be [transitioned too](https://codesandbox.io/s/github/vuejs/vuejs.org/tree/master/src/v2/examples/vue-20-list-move-transitions):

TODO: example

### Staggering List Transitions

By communicating with JavaScript transitions through data attributes, it's also possible to stagger transitions in a list:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.3.4/gsap.min.js"></script>

<div id="demo">
  <input v-model="query" />
  <transition-group
    name="staggered-fade"
    tag="ul"
    :css="false"
    @before-enter="beforeEnter"
    @enter="enter"
    @leave="leave"
  >
    <li
      v-for="(item, index) in computedList"
      :key="item.msg"
      :data-index="index"
    >
      {{ item.msg }}
    </li>
  </transition-group>
</div>
```

```js
const Demo = {
  data() {
    return {
      query: '',
      list: [
        { msg: 'Bruce Lee' },
        { msg: 'Jackie Chan' },
        { msg: 'Chuck Norris' },
        { msg: 'Jet Li' },
        { msg: 'Kung Fury' }
      ]
    }
  },
  computed: {
    computedList() {
      var vm = this
      return this.list.filter(item => {
        return item.msg.toLowerCase().indexOf(vm.query.toLowerCase()) !== -1
      })
    }
  },
  methods: {
    beforeEnter(el) {
      el.style.opacity = 0
      el.style.height = 0
    },
    enter(el, done) {
      gsap.to(el, {
        opacity: 1,
        height: '1.6em',
        delay: el.dataset.index * 0.15,
        onComplete: done
      })
    },
    leave(el, done) {
      gsap.to(el, {
        opacity: 0,
        height: 0,
        delay: el.dataset.index * 0.15,
        onComplete: done
      })
    }
  }
}

Vue.createApp(Demo).mount('#demo')
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="js,result" data-user="Vue" data-slug-hash="c2fc5107bd3025ceadea049b3ee44ec0" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Staggered Lists">
  <span>See the Pen <a href="https://codepen.io/team/Vue/pen/c2fc5107bd3025ceadea049b3ee44ec0">
  Staggered Lists</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Reusable Transitions

Transitions can be reused through Vue's component system. To create a reusable transition, all you have to do is place a `<transition>` or `<transition-group>` component at the root, then pass any children into the transition component.

TODO: refactor to Vue 3

Here's an example using a template component:

```js
Vue.component('my-special-transition', {
  template: '\
    <transition\
      name="very-special-transition"\
      mode="out-in"\
      @before-enter="beforeEnter"\
      @after-enter="afterEnter"\
    >\
      <slot></slot>\
    </transition>\
  ',
  methods: {
    beforeEnter(el) {
      // ...
    },
    afterEnter(el) {
      // ...
    }
  }
})
```

And [functional components](render-function.md#Functional-Components) are especially well-suited to this task:

```js
Vue.component('my-special-transition', {
  functional: true,
  render: function(createElement, context) {
    var data = {
      props: {
        name: 'very-special-transition',
        mode: 'out-in'
      },
      on: {
        beforeEnter(el) {
          // ...
        },
        afterEnter(el) {
          // ...
        }
      }
    }
    return createElement('transition', data, context.children)
  }
})
```

## Dynamic Transitions

Yes, even transitions in Vue are data-driven! The most basic example of a dynamic transition binds the `name` attribute to a dynamic property.

```html
<transition :name="transitionName">
  <!-- ... -->
</transition>
```

This can be useful when you've defined CSS transitions/animations using Vue's transition class conventions and want to switch between them.

Really though, any transition attribute can be dynamically bound. And it's not only attributes. Since event hooks are methods, they have access to any data in the context. That means depending on the state of your component, your JavaScript transitions can behave differently.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/velocity/1.2.3/velocity.min.js"></script>

<div id="dynamic-fade-demo" class="demo">
  Fade In:
  <input type="range" v-model="fadeInDuration" min="0" :max="maxFadeDuration" />
  Fade Out:
  <input
    type="range"
    v-model="fadeOutDuration"
    min="0"
    :max="maxFadeDuration"
  />
  <transition
    :css="false"
    @before-enter="beforeEnter"
    @enter="enter"
    @leave="leave"
  >
    <p v-if="show">hello</p>
  </transition>
  <button v-if="stop" @click="stop = false; show = false">
    Start animating
  </button>
  <button v-else @click="stop = true">Stop it!</button>
</div>
```

```js
const app = Vue.createApp({
  data() {
    return {
      show: true,
      fadeInDuration: 1000,
      fadeOutDuration: 1000,
      maxFadeDuration: 1500,
      stop: true
    }
  },
  mounted() {
    this.show = false
  },
  methods: {
    beforeEnter(el) {
      el.style.opacity = 0
    },
    enter(el, done) {
      var vm = this
      Velocity(
        el,
        { opacity: 1 },
        {
          duration: this.fadeInDuration,
          complete: function() {
            done()
            if (!vm.stop) vm.show = false
          }
        }
      )
    },
    leave(el, done) {
      var vm = this
      Velocity(
        el,
        { opacity: 0 },
        {
          duration: this.fadeOutDuration,
          complete: function() {
            done()
            vm.show = true
          }
        }
      )
    }
  }
})

app.mount('#dynamic-fade-demo')
```

TODO: example

Finally, the ultimate way of creating dynamic transitions is through components that accept props to change the nature of the transition(s) to be used. It may sound cheesy, but the only limit really is your imagination.
