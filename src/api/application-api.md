# Application API

In Vue 3, APIs that globally mutate Vue's behavior are now moved to application instances created by the new `createApp` method. In addition, their effects are now scoped to that specific application's instance:

```js
import { createApp } from 'vue'

const app = createApp({})
```

Calling `createApp` returns an application instance. This instance provides an application context. The entire component tree mounted by the application instance share the same context, which provides the configurations that were previously "global" in Vue 2.x.

In addition, since the `createApp` method returns the application instance itself, you can chain other methods after it which can be found in the following sections.

## component

- **Arguments:**

  - `{string} name`
  - `{Function | Object} [definition]`

- **Usage:**

  Register or retrieve a global component. Registration also automatically sets the component's `name` with the given `name` parameter.

- **Example:**

```js
import { createApp } from 'vue'

const app = createApp({})

// register an options object
app.component('my-component', {
  /* ... */
})

// retrieve a registered component (always return constructor)
const MyComponent = app.component('my-component', {})
```

- **See also:** [Components](../guide/component-basics.html)

## config

- **Usage:**

An object containing application configurations.

- **Example:**

```js
import { createApp } from 'vue'
const app = createApp({})

app.config = {...}
```

- **See also:** [Application Config](./application-config.html)

## directive

- **Arguments:**

  - `{string} name`
  - `{Function | Object} [definition]`

- **Usage:**

  Register or retrieve a global directive.

- **Example:**

```js
import { createApp } from 'vue'
const app = createApp({})

// register
app.directive('my-directive', {
  // Directive has a set of lifecycle hooks:
  // called before bound element's parent component is mounted
  beforeMount() {},
  // called when bound element's parent component is mounted
  mounted() {},
  // called before the containing component's VNode is updated
  beforeUpdate() {},
  // called after the containing component's VNode and the VNodes of its children // have updated
  updated() {},
  // called before the bound element's parent component is unmounted
  beforeUnmount() {},
  // called when the bound element's parent component is unmounted
  unmounted() {}
})

// register (function directive)
app.directive('my-directive', () => {
  // this will be called as `mounted` and `updated`
})

// getter, return the directive definition if registered
const myDirective = app.directive('my-directive')
```

Directive hooks are passed these arguments:

#### el

The element the directive is bound to. This can be used to directly manipulate the DOM.

#### binding

An object containing the following properties.

- `instance`: The instance of the component where directive is used.
- `value`: The value passed to the directive. For example in `v-my-directive="1 + 1"`, the value would be `2`.
- `oldValue`: The previous value, only available in `beforeUpdate` and `updated`. It is available whether or not the value has changed.
- `arg`: The argument passed to the directive, if any. For example in `v-my-directive:foo`, the arg would be `"foo"`.
- `modifiers`: An object containing modifiers, if any. For example in `v-my-directive.foo.bar`, the modifiers object would be `{ foo: true, bar: true }`.
- `dir`: an object, passed as a parameter when directive is registered. For example, in the directive

```js
app.directive('focus', {
  mounted(el) {
    el.focus()
  }
})
```

`dir` would be the following object:

```js
{
  mounted(el) {
    el.focus()
  }
}
```

#### vnode

A blueprint of the real DOM element received as el argument above.

#### prevNode

The previous virtual node, only available in the `beforeUpdate` and `updated` hooks.

:::tip Note
Apart from `el`, you should treat these arguments as read-only and never modify them. If you need to share information across hooks, it is recommended to do so through element's [dataset](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dataset).
:::

- **See also:** [Custom Directives](../guide/custom-directive.html)

## mixin

- **Arguments:**

  - `{Object} mixin`

- **Usage:**

  Apply a mixin in the whole application scope. Once registered they can be used in the template of any component within the current application. This can be used by plugin authors to inject custom behavior into components. **Not recommended in application code**.

- **See also:** [Global Mixin](../guide/mixins.html#global-mixin)

## mount

- **Arguments:**

  - `{Element | string} rootContainer`
  - `{boolean} isHydrate`

- **Usage:**

  Mounts a root component of the application instance on the provided DOM element.

- **Example:**

```html
<body>
  <div id="my-app"></div>
</body>
```

```js
import { createApp } from 'vue'

const app = createApp({})
// do some necessary preparations
app.mount('#my-app')
```

- **See also:**
  - [Lifecycle Diagram](../guide/instance.html#lifecycle-diagram)

## unmount

- **Arguments:**

  - `{Element | string} rootContainer`

- **Usage:**

  Unmounts a root component of the application instance on the provided DOM element.

- **Example:**

```html
<body>
  <div id="my-app"></div>
</body>
```

```js
import { createApp } from 'vue'

const app = createApp({})
// do some necessary preparations
app.mount('#my-app')

// Application will be unmounted 5 seconds after mount
setTimeout(() => app.unmount('#my-app'), 5000)
```

## use

- **Arguments:**

  - `{Object | Function} plugin`

- **Usage:**

  Install a Vue.js plugin. If the plugin is an Object, it must expose an `install` method. If it is a function itself, it will be treated as the install method. The install method will be called with Vue as the argument.

  When this method is called on the same plugin multiple times, the plugin will be installed only once.

- **See also:** [Plugins](../guide/plugins.html)
