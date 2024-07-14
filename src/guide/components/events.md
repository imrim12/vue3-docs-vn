<script setup>
import { onMounted } from 'vue'

if (typeof window !== 'undefined') {
  const hash = window.location.hash

  // The docs for v-model used to be part of this page. Attempt to redirect outdated links.
  if ([
    '#usage-with-v-model',
    '#v-model-arguments',
    '#multiple-v-model-bindings',
    '#handling-v-model-modifiers'
  ].includes(hash)) {
    onMounted(() => {
      window.location = './v-model.html' + hash
    })
  }
}
</script>
# Sự kiện Component {#component-events}

> Trang này giả định rằng bạn đã đọc qua [Cơ bản Component](/guide/essentials/component-basics). Hãy đọc phần đó trước nếu bạn mới bắt đầu với component.

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/defining-custom-events-emits" title="Free Vue.js Lesson on Defining Custom Events"/>
</div>

## Phát và lắng nghe sự kiện {#emitting-and-listening-to-events}

Một component có thể phát ra các sự kiện tùy chỉnh một cách trực tiếp trong biểu thức template (ví dụ trong một `v-on` handler) bằng cách sử dụng phương thức `$emit` tích hợp sẵn:

```vue-html
<!-- MyComponent -->
<button @click="$emit('someEvent')">nhấn vào tôi</button>
```

<div class="options-api">

Phương thức `$emit()` cũng có sẵn trên instance component như `this.$emit()`:

```js
export default {
  methods: {
    submit() {
      this.$emit('someEvent')
    }
  }
}
```

</div>

Component cha có thể lắng nghe các sự kiện được phát ra bởi component con bằng cách sử dụng `v-on`:

```vue-html
<MyComponent @some-event="callback" />
```

`.once` modifier cũng được hỗ trợ trên các event listener của component:

```vue-html
<MyComponent @some-event.once="callback" />
```

Giống như component và props, tên sự kiện cũng cung cấp một chuyển đổi chữ cái tự động. Lưu ý rằng chúng tôi phát ra một sự kiện camelCase, nhưng có thể lắng nghe nó bằng cách sử dụng một listener kebab-cased trong component cha. Tương tự như [props casing](/guide/components/props#prop-name-casing), chúng tôi khuyến khích sử dụng kebab-cased event listener trong template.

:::tip
Không giống như các sự kiện DOM thuần, các sự kiện phát ra bởi component **không** lan truyền. Bạn chỉ có thể lắng nghe các sự kiện được phát ra bởi một component con trực tiếp. Nếu có nhu cầu giao tiếp giữa các component anh em hoặc sâu, hãy sử dụng một event bus bên ngoài hoặc một [giải pháp quản lý trạng thái toàn cục](/guide/scaling-up/state-management).
:::

## Đối số của sự kiện {#event-arguments}

Đôi khi, việc phát một giá trị cụ thể với một sự kiện rất có ích. Ví dụ, chúng ta có thể muốn component `<BlogPost>` chịu trách nhiệm phóng to văn bản với một mức độ cụ thể. Trong những trường hợp đó, chúng ta có thể truyền thêm đối số cho `$emit` để cung cấp giá trị này:

```vue-html
<button @click="$emit('increaseBy', 1)">
  Increase by 1
</button>
```

Lúc này, khi chúng ta lắng nghe sự kiện trong component cha, chúng ta có thể sử dụng một arrow function trực tiếp như một listener, cho phép chúng ta truy cập đối số của sự kiện:

```vue-html
<MyButton @increase-by="(n) => count += n" />
```

Hoặc nếu event handler là một phương thức:

```vue-html
<MyButton @increase-by="increaseCount" />
```

Lúc này giá trị sẽ được truyền như là tham số đầu tiên của phương thức đó:

<div class="options-api">

```js
methods: {
  increaseCount(n) {
    this.count += n
  }
}
```

</div>
<div class="composition-api">

```js
function increaseCount(n) {
  count.value += n
}
```

</div>

:::tip
Tất cả các đối số được truyền vào `$emit` sau tên sự kiện sẽ được chuyển tiếp cho listener. Ví dụ, với `$emit('foo', 1, 2, 3)` thì listener function sẽ nhận được ba đối số.
:::

## Khai báo sự kiện được phát ra {#declaring-emitted-events}

Một component có thể khai báo rõ ràng các sự kiện nó sẽ phát ra bằng cách sử dụng <span class="composition-api">[`defineEmits()`](/api/sfc-script-setup#defineprops-defineemits) macro</span><span class="options-api">[`emits`](/api/options-state#emits) option</span>:

<div class="composition-api">

```vue
<script setup>
defineEmits(['inFocus', 'submit'])
</script>
```

Phương thức `$emit` mà chúng ta đã sử dụng trong `<template>` không thể truy cập được trong phần `<script setup>` của một component, nhưng `defineEmits()` trả về một hàm tương đương mà chúng ta có thể sử dụng thay thế:

```vue
<script setup>
const emit = defineEmits(['inFocus', 'submit'])

function buttonClick() {
  emit('submit')
}
</script>
```

`defineEmits()` macro **không thể** được sử dụng bên trong một function, nó phải được đặt trực tiếp trong `<script setup>`, như ví dụ trên.

Nếu bạn đang sử dụng một function `setup` thay vì `<script setup>`, các sự kiện nên được khai báo bằng cách sử dụng tùy chọn [`emits`](/api/options-state#emits), và hàm `emit` được truy cập thông qua context của `setup()`:

```js
export default {
  emits: ['inFocus', 'submit'],
  setup(props, ctx) {
    ctx.emit('submit')
  }
}
```

Giống như các thuộc tính của context `setup()`, `emit` có thể được destructured một cách an toàn:

```js
export default {
  emits: ['inFocus', 'submit'],
  setup(props, { emit }) {
    emit('submit')
  }
}
```

</div>
<div class="options-api">

```js
export default {
  emits: ['inFocus', 'submit']
}
```

</div>

Tuỳ chọn `emits` cũng hỗ trợ cú pháp object, cho phép chúng ta thực hiện xác thực runtime của payload của các sự kiện được phát ra:

<div class="composition-api">

```vue
<script setup>
const emit = defineEmits({
  submit(payload) {
    // Trả về `true` hoặc `false` để chỉ ra
    // xác thực pass / fail
  }
})
</script>
```

Nếu bạn đang sử dụng TypeScript với `<script setup>`, cũng có thể khai báo các sự kiện được phát ra bằng các type annotation thuần:

```vue
<script setup lang="ts">
const emit = defineEmits<{
  (e: 'change', id: number): void
  (e: 'update', value: string): void
}>()
</script>
```

Xem thêm: [Typing Component Emits](/guide/typescript/composition-api#typing-component-emits) <sup class="vt-badge ts" />

</div>
<div class="options-api">

```js
export default {
  emits: {
    submit(payload) {
      // Trả về `true` hoặc `false` để chỉ ra
      // xác thực pass / fail
    }
  }
}
```

Xem thêm: [Typing Component Emits](/guide/typescript/options-api#typing-component-emits) <sup class="vt-badge ts" />

</div>

Mặc dù là tùy chọn, nhưng việc định nghĩa tất cả các sự kiện được phát ra được khuyến khích để tài liệu hóa tốt hơn cách một component nên hoạt động. Nó cũng cho phép Vue loại bỏ các listeners đã biết từ [fallthrough attributes](/guide/components/attrs#v-on-listener-inheritance), tránh các trường hợp đặc biệt của các DOM events được phát ra bằng cách thủ công bởi code của bên thứ ba.

:::tip
Nếu một sự kiện thuần (ví dụ `click`) được định nghĩa trong tùy chọn `emits`, listener sẽ chỉ lắng nghe các sự kiện `click` được phát ra bởi component và không còn phản hồi với các sự kiện `click` thuần.
:::

## Xác thực sự kiện {#event-validation}

Tương tự như xác thực prop type, một sự kiện được phát ra có thể được xác thực nếu nó được định nghĩa với cú pháp object thay vì cú pháp array.

Để thêm xác thực, sự kiện được gắn một hàm xác thực nhận các đối số được truyền vào `<span class="options-api">`this.$emit`</span><span class="composition-api">`emit`</span> và trả về một boolean để chỉ ra sự kiện có hợp lệ hay không.

<div class="composition-api">

```vue
<script setup>
const emit = defineEmits({
  // Không xác thực
  click: null,

  // Xác thực sự kiện submit
  submit: ({ email, password }) => {
    if (email && password) {
      return true
    } else {
      console.warn('Invalid submit event payload!')
      return false
    }
  }
})

function submitForm(email, password) {
  emit('submit', { email, password })
}
</script>
```

</div>
<div class="options-api">

```js
export default {
  emits: {
    // Không xác thực
    click: null,

    // Xác thực sự kiện submit
    submit: ({ email, password }) => {
      if (email && password) {
        return true
      } else {
        console.warn('Invalid submit event payload!')
        return false
      }
    }
  },
  methods: {
    submitForm(email, password) {
      this.$emit('submit', { email, password })
    }
  }
}
```

</div>
