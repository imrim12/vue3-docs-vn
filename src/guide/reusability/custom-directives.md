# Directives tuỳ chỉnh {#custom-directives}

<script setup>
const vFocus = {
  mounted: el => {
    el.focus()
  }
}
</script>

## Giới thiệu {#introduction}

Ngoài các hướng dẫn mặc định được thiết lập trong core (như `v-model` hoặc `v-show`), Vue cũng cho phép bạn đăng ký các hướng dẫn tuỳ chỉnh của riêng bạn.

Chúng ta đã giới thiệu hai hình thức tái sử dụng mã trong Vue: [components](/guide/essentials/component-basics) và [composables](./composables). Components là các khối xây dựng chính, trong khi composables tập trung vào việc tái sử dụng logic có trạng thái. Ngược lại, các hướng dẫn tuỳ chỉnh chủ yếu được sử dụng để tái sử dụng logic liên quan đến truy cập DOM cấp thấp trên các phần tử thông thường.

Một directive tuỳ chỉnh được định nghĩa như một đối tượng chứa các lifecycle hooks tương tự như của một component. Các hooks nhận vào phần tử mà directive được ràng buộc. Dưới đây là một ví dụ về một directive tập trung vào việc focus vào một input khi phần tử được chèn vào DOM bởi Vue:

<div class="composition-api">

```vue
<script setup>
// kích hoạt v-focus trong template
const vFocus = {
  mounted: (el) => el.focus()
}
</script>

<template>
  <input v-focus />
</template>
```

</div>

<div class="options-api">

```js
const focus = {
  mounted: (el) => el.focus()
}

export default {
  directives: {
    // kích hoạt v-focus trong template
    focus
  }
}
```

```vue-html
<input v-focus />
```

</div>

<div class="demo">
  <input v-focus placeholder="Phần tử này nên được focus khi render" />
</div>

Giả định rằng bạn chưa nhấn vào bất kỳ chỗ nào khác trong trang, input ở trên sẽ được tự động focus. Directive này hữu ích hơn so với thuộc tính `autofocus` vì nó hoạt động không chỉ khi trang được load - nó còn hoạt động khi phần tử được chèn vào DOM bởi Vue.
Assuming you haven't clicked elsewhere on the page, the input above should be auto-focused. This directive is more useful than the `autofocus` attribute because it works not just on page load - it also works when the element is dynamically inserted by Vue.

<div class="composition-api">

Trong `<script setup>`, bất kỳ biến camelCase nào bắt đầu bằng tiền tố `v` có thể được sử dụng như một directive tuỳ chỉnh. Trong ví dụ trên, `vFocus` có thể được sử dụng trong template như `v-focus`.

Nếu không sử dụng `<script setup>`, directive tuỳ chỉnh có thể được đăng ký bằng cách sử dụng tùy chọn `directives`:

```js
export default {
  setup() {
    /*...*/
  },
  directives: {
    // kích hoạt v-focus trong template
    focus: {
      /* ... */
    }
  }
}
```

</div>

<div class="options-api">

Tương tự với components, directive tuỳ chỉnh phải được đăng ký để có thể sử dụng trong template. Trong ví dụ trên, chúng ta sử dụng đăng ký cục bộ thông qua tùy chọn `directives`.

</div>

Việc đăng ký directive tuỳ chỉnh cũng có thể được thực hiện toàn cục tại cấp ứng dụng:

```js
const app = createApp({})

// khiến cho v-focus có thể sử dụng trong tất cả các component
app.directive('focus', {
  /* ... */
})
```

:::tip
Directive tuỳ chỉnh chỉ nên được sử dụng khi chức năng mong muốn chỉ có thể đạt được thông qua việc trực tiếp thao tác DOM. Ưu tiên sử dụng template khai báo thông qua các directive mặc định như `v-bind` khi có thể vì chúng hiệu quả hơn và thân thiện với server-rendering.
:::

## Directive Hooks {#directive-hooks}

Một đối tượng định nghĩa directive có thể cung cấp một số hàm hook (tất cả đều là tùy chọn):

```js
const myDirective = {
  // được gọi trước khi các thuộc tính của phần tử được ràng buộc
  // hoặc các event listener được áp dụng.
  created(el, binding, vnode, prevVnode) {
    // xem bên dưới để biết chi tiết về các đối số
  },
  // được gọi ngay trước khi phần tử được chèn vào DOM.
  beforeMount(el, binding, vnode, prevVnode) {},
  // được gọi sau khi phần tử cha và 
  // và tất cả các phần tử con của nó được chèn vào DOM.
  mounted(el, binding, vnode, prevVnode) {},
  // được gọi trước khi component cha được cập nhật
  beforeUpdate(el, binding, vnode, prevVnode) {},
  // được gọi sau khi component cha và
  // tất cả các phần tử con của nó được cập nhật
  updated(el, binding, vnode, prevVnode) {},
  // được gọi trước khi component cha được gỡ bỏ
  beforeUnmount(el, binding, vnode, prevVnode) {},
  // được gọi sau khi component cha đã gỡ bỏ
  unmounted(el, binding, vnode, prevVnode) {}
}
```

### Đối số của Hook {#hook-arguments}

Tất cả các hook của directive đều nhận vào các đối số sau:

- `el`: phần tử mà directive được ràng buộc. Điều này có thể được sử dụng để trực tiếp thao tác DOM.

- `binding`: một đối tượng chứa các thuộc tính sau.

  - `value`: Giá trị được truyền vào directive. Ví dụ trong `v-my-directive="1 + 1"`, giá trị sẽ là `2`.
  - `oldValue`: Giá trị trước đó, chỉ có sẵn trong `beforeUpdate` và `updated`. Nó có sẵn dù giá trị có thay đổi hay không.
  - `arg`: Đối số được truyền vào directive, nếu có. Ví dụ trong `v-my-directive:foo`, đối số sẽ là `"foo"`.
  - `modifiers`: Một đối tượng chứa các modifier, nếu có. Ví dụ trong `v-my-directive.foo.bar`, đối tượng modifier sẽ là `{ foo: true, bar: true }`.
  - `instance`: Thể hiện của component mà directive được sử dụng.
  - `dir`: đối tượng định nghĩa directive.

- `vnode`: VNode cơ bản đại diện cho phần tử được ràng buộc.
- `prevVnode`: VNode đại diện cho phần tử được ràng buộc từ lần render trước. Chỉ có sẵn trong các hook `beforeUpdate` và `updated`.

Lấy ví dụ, hãy xem xét việc sử dụng directive sau:

```vue-html
<div v-example:foo.bar="baz">
```

Đối số `binding` sẽ là một đối tượng có dạng:

```js
{
  arg: 'foo',
  modifiers: { bar: true },
  value: /* giá trị của `baz` */,
  oldValue: /* giá trị của `baz` từ lần cập nhật trước */
}
```

Tương tự như các directive mặc định, các đối số của directive tuỳ chỉnh có thể là biểu thức JavaScript hợp lệ. Ví dụ:

```vue-html
<div v-example:[arg]="value"></div>
```

Ở đây, đối số của directive sẽ được cập nhật một cách linh hoạt dựa trên thuộc tính `arg` trong trạng thái của component của chúng ta.

:::tip Lưu ý
Ngoại trừ `el`, bạn nên coi các đối số này là chỉ đọc và không bao giờ thay đổi chúng. Nếu bạn cần chia sẻ thông tin qua các hook, nên sử dụng [dataset](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dataset) của phần tử.
:::

## Cú pháp rút gọn{#function-shorthand}

Đối với các directive tuỳ chỉnh, việc có chung hành vi cho `mounted` và `updated` rất là bình thường mà không cần các hook khác. Trong những trường hợp như vậy, chúng ta có thể định nghĩa directive dưới dạng một hàm:

```vue-html
<div v-color="color"></div>
```

```js
app.directive('color', (el, binding) => {
  // directive này sẽ được gọi cho cả `mounted` và `updated`
  el.style.color = binding.value
})
```

## Object Literals {#object-literals}

Nếu directive của bạn cần nhiều giá trị khác nhau, bạn cũng có thể truyền vào một object literal JavaScript. Hãy nhớ rằng directive có thể nhận bất kỳ biểu thức JavaScript hợp lệ nào.

```vue-html
<div v-demo="{ color: 'white', text: 'xin chào!' }"></div>
```

```js
app.directive('demo', (el, binding) => {
  console.log(binding.value.color) // => "white"
  console.log(binding.value.text) // => "xin chào!"
})
```

## Sử dụng trong Component {#usage-on-components}

Khi sử dụng trên components, directive tuỳ chỉnh sẽ luôn được áp dụng cho phần tử gốc của component, tương tự như [Fallthrough Attributes](/guide/components/attrs).

```vue-html
<MyComponent v-demo="test" />
```

```vue-html
<!-- template của MyComponent -->

<div> <!-- v-demo directive sẽ được áp dụng tại đây -->
  <span>My component content</span>
</div>
```

Lưu ý rằng components có thể có nhiều hơn một phần tử gốc. Khi áp dụng cho một component có nhiều phần tử gốc, directive sẽ bị bỏ qua và một cảnh báo sẽ được ném ra. Khác với thuộc tính, directive không thể được truyền vào một phần tử khác với `v-bind="$attrs"`. Nói chung, **không** nên sử dụng directive tuỳ chỉnh trên components.
