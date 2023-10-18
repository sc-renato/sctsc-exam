Every framework out there helps bridge the gap between HTML and JavaScript by offering different mechanisms of reactivity. Your job will be to implement _cross_ function, i.e. function `x`, that will produce a reactive object. Here's an example usage:

```html
<button type="button" id="decrease">-1</button>
<button type="button" id="increase">+1</button>
<div>Current count: <span id="count-output"></span></div>
<div>Also current count: <span class="js-count"></span></div>
```

```ts
const output = document.querySelector('#count-output');

const state = x({
  count: [10, ['.js-count', output]],
});

const decrease = document.querySelector('#decrease');
const increase = document.querySelector('#increase');

decrease?.addEventListener('click', () => {
  state.count -= 1;
});

increase?.addEventListener('click', () => {
  state.count += 1;
});
```

Output in the HTML should be:

```html
<div>Current count: <span id="count-output">10</span></div>
<div>Also current count: <span class="js-count">10</span></div>
```

After clicking increase button:

```html
<div>Current count: <span id="count-output">11</span></div>
<div>Also current count: <span class="js-count">11</span></div>
```

`x` function accepts only one, mandatory argument - an object which defines relation between state (values) and HTML elements that listen to (value) changes. Keys in definition objects are keys that need to be in the resulting state (e.g. in the above example, only one key `count`). Values are arrays (tuples) that have either one or two elements. An example is below:

```ts
const user = x({
  name: ['John', ['.name-output']],
  email: [''],
});
```

First element of the array is the initial value of the field. The second (optional) element is the array of either string selectors or HTML elements. If the second argument is not provided, that means that _no_ HTML elements will reflect the value of that field. To understand it better, in the example above, _every_ HTML element that has a class of `.name-output` will reflect the value of the `name` property. When the websitee is loaded, the content of each `.name-output` element should be `John`. If we go back to the `count` example, then _every_ element with `.js-count` class should reflect the changes AND the (single) `output` element that was provided.

As a return type, `x` function should return the state object that was passed in the definitions. The initial value is the one that should define the type of each value. In the first example, if we hover over the `state` variable, TypeScript should say:

```ts
const state: {
  count: number;
}
```

In case of the `user` variable, the type should say:

```ts
const user: {
  name: string;
  email: string;
}
```

Whenever the state value is modified, every registered element should reflect the change of that value. For example, in the `count` case, whenever the increase button is clicked, i.e. when `state.count += 1` occurs, every registered listener (element) should reflect the change and display a new value.

In order to make the resulting object, i.e. state, reactive, you can use Proxy:
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy/set