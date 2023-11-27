# Template Refs {#template-refs}

Mặc dù mô hình render khai báo của Vue trừu tượng hóa hầu hết các hoạt động DOM trực tiếp cho bạn, nhưng vẫn có thể có những trường hợp mà chúng ta cần truy cập trực tiếp vào các phần tử DOM cơ sở. Để đạt được điều này, chúng ta có thể sử dụng thuộc tính đặc biệt `ref`:

```vue-html
<input ref="input">
```

`ref` là một thuộc tính đặc biệt, tương tự như thuộc tính `key` được thảo luận trong chương `v-for`. Nó cho phép chúng ta có được một tham chiếu trực tiếp đến một phần tử DOM cụ thể hoặc một thể hiện thành phần con sau khi nó được gắn kết. Điều này có thể hữu ích khi bạn muốn, ví dụ, tập trung vào một đầu vào trên thành phần mount, hoặc khởi tạo một thư viện bên thứ 3 trên một phần tử.

## Truy cập Refs {#accessing-the-refs}

<div class="composition-api">

Để truy cập tham chiếu với Composition API, chúng ta cần khai báo một ref với cùng tên:

```vue
<script setup>
import { ref, onMounted } from 'vue'

// khai báo một ref để giữ tham chiếu đến phần tử
// tên phải khớp với giá trị ref của template
const input = ref(null)

onMounted(() => {
  input.value.focus()
})
</script>

<template>
  <input ref="input" />
</template>
```

Nếu không sử dụng `<script setup>`, hãy chắc chắn trả về ref từ `setup()`:

```js{6}
export default {
  setup() {
    const input = ref(null)
    // ...
    return {
      input
    }
  }
}
```

</div>
<div class="options-api">

Kết quả của ref được tiết lộ trên `this.$refs`:

```vue
<script>
export default {
  mounted() {
    this.$refs.input.focus()
  }
}
</script>

<template>
  <input ref="input" />
</template>
```

</div>

Lưu ý rằng bạn chỉ có thể truy cập ref **sau khi component được mounted.** Nếu bạn cố gắng truy cập <span class="options-api">`$refs.input`</span><span class="composition-api">`input`</span> trong một biểu thức template, nó sẽ hiển thị `null` trong lần render đầu tiên. Việc này là do phần tử không tồn tại cho đến sau lần render đầu tiên!

<div class="composition-api">

Nếu bạn đang cố gắng theo dõi các thay đổi của một ref template, hãy chắc chắn xem xét trường hợp mà ref có giá trị `null`:


```js
watchEffect(() => {
  if (input.value) {
    input.value.focus()
  } else {
    // chưa được mounted, hoặc phần tử đã bị unmounted (ví dụ bởi v-if)
  }
})
```

Xem thêm: [Ép kiểu Template Refs](/guide/typescript/composition-api#typing-template-refs) <sup class="vt-badge ts" />

</div>

## Refs trong `v-for` {#refs-inside-v-for}

> Yêu cầu v3.2.25 hoặc cao hơn

<div class="composition-api">

Khi `ref` được sử dụng bên trong `v-for`, ref tương ứng nên chứa một giá trị mảng chứa các phần tử tương ứng:

```vue
<script setup>
import { ref, onMounted } from 'vue'

const list = ref([
  /* ... */
])

const itemRefs = ref([])

onMounted(() => console.log(itemRefs.value))
</script>

<template>
  <ul>
    <li v-for="item in list" ref="itemRefs">
      {{ item }}
    </li>
  </ul>
</template>
```

[Thử trong Playground](https://play.vuejs.org/#eNpFjs1qwzAQhF9l0CU2uDZtb8UOlJ576bXqwaQyCGRJyCsTEHr3rGwnOehnd2e+nSQ+vW/XqMSH6JdL0J6wKIr+LK2evQuEhKCmBs5+u2hJ/SNjCm7GiV0naaW9OLsQjOZrKNrq97XBW4P3v/o51qTmHzUtd8k+e0CrqsZwRpIWGI0KVN0N7TqaqNp59JUuEt2SutKXY5elmimZT9/t2Tk1F+z0ZiTFFdBHs738Mxrry+TCIEWhQ9sttRQl0tEsK6U4HEBKW3LkfDA6o3dst3H77rFM5BtTfm/P)

</div>
<div class="options-api">

Khi `ref` được sử dụng bên trong `v-for`, ref tương ứng nên chứa một giá trị mảng chứa các phần tử tương ứng:

```vue
<script>
export default {
  data() {
    return {
      list: [
        /* ... */
      ]
    }
  },
  mounted() {
    console.log(this.$refs.items)
  }
}
</script>

<template>
  <ul>
    <li v-for="item in list" ref="items">
      {{ item }}
    </li>
  </ul>
</template>
```

[Thử trong Playground](https://play.vuejs.org/#eNpFjk0KwjAQha/yCC4Uaou6kyp4DuOi2KkGYhKSiQildzdNa4WQmTc/37xeXJwr35HEUdTh7pXjszT0cdYzWuqaqBm9NEDbcLPeTDngiaM3PwVoFfiI667AvsDhNpWHMQzF+L9sNEztH3C3JlhNpbaPNT9VKFeeulAqplfY5D1p0qurxVQSqel0w5QUUEedY8q0wnvbWX+SYgRAmWxIiuSzm4tBinkc6HvkuSE7TIBKq4lZZWhdLZfE8AWp4l3T)

</div>

Nên lưu ý rằng mảng ref **không** đảm bảo cùng một thứ tự như mảng nguồn.

## Hàm Refs {#function-refs}

Thay vì một khóa chuỗi, thuộc tính `ref` cũng có thể được ràng buộc với một hàm, sẽ được gọi trên mỗi cập nhật thành phần và cho phép bạn linh hoạt hoàn toàn về nơi lưu trữ tham chiếu phần tử. Hàm nhận tham chiếu phần tử làm đối số đầu tiên:

```vue-html
<input :ref="(el) => { /* assign el to a property or ref */ }">
```

Lưu ý rằng chúng ta đang sử dụng một `:ref` ràng buộc động để chúng ta có thể truyền cho nó một hàm thay vì một chuỗi tên ref. Khi phần tử bị unmounted, đối số sẽ là `null`. Bạn có thể, tất nhiên, sử dụng một phương thức thay vì một hàm inline.

## Ref trong Component {#ref-on-component}

> Phần này giả định bạn đã biết về [Components](/guide/essentials/component-basics). Bạn có thể bỏ qua và quay lại sau nếu chưa biết.

`ref` cũng có thể được sử dụng trên một component. Trong trường hợp này, tham chiếu sẽ là một instance của một component:

<div class="composition-api">

```vue
<script setup>
import { ref, onMounted } from 'vue'
import Child from './Child.vue'

const child = ref(null)

onMounted(() => {
  // child.value sẽ giữ một instance của <Child />
})
</script>

<template>
  <Child ref="child" />
</template>
```

</div>
<div class="options-api">

```vue
<script>
import Child from './Child.vue'

export default {
  components: {
    Child
  },
  mounted() {
    // this.$refs.child sẽ giữ một instance của <Child />
  }
}
</script>

<template>
  <Child ref="child" />
</template>
```

</div>
<span class="composition-api">Nếu component con đang sử dụng Options API hoặc không sử dụng `<script setup>`,</span><span class="options-api">Nếu component con đang sử dụng Options API,</span> tham chiếu instance sẽ giống với `this` của component con, điều này có nghĩa là component cha sẽ có quyền truy cập đầy đủ vào mọi thuộc tính và phương thức của component con. Điều này làm cho việc tạo chi tiết thực thi được liên kết chặt chẽ giữa cha và con trở nên dễ dàng, vì vậy refs của component nên chỉ được sử dụng khi thực sự cần thiết - trong hầu hết các trường hợp, bạn nên thử triển khai các tương tác cha / con bằng giao diện props và emit tiêu chuẩn trước.

<div class="composition-api">

Một ngoại lệ ở đây là các component sử dụng `<script setup>` mặc định là **private**: một component cha tham chiếu một component con sử dụng `<script setup>` sẽ không thể truy cập bất cứ thứ gì trừ khi component con chọn để tiết lộ một giao diện công khai bằng macro `defineExpose`:

```vue
<script setup>
import { ref } from 'vue'

const a = 1
const b = ref(2)

// Macro của trình biên dịch, chẳng hạn như defineExpose, không cần phải được import
defineExpose({
  a,
  b
})
</script>
```

Khi một component cha nhận một instance của component này thông qua refs template, instance được truy xuất sẽ có hình dạng `{ a: number, b: number }` (refs được tự động unwrap giống như trên các instance bình thường).

Xem thêm: [Ép kiểu Component Template Refs](/guide/typescript/composition-api#typing-component-template-refs) <sup class="vt-badge ts" />

</div>
<div class="options-api">

Tuỳ chọn `expose` có thể được sử dụng để giới hạn quyền truy cập vào một instance con:

```js
export default {
  expose: ['publicData', 'publicMethod'],
  data() {
    return {
      publicData: 'foo',
      privateData: 'bar'
    }
  },
  methods: {
    publicMethod() {
      /* ... */
    },
    privateMethod() {
      /* ... */
    }
  }
}
```

Trong ví dụ trên, một component cha tham chiếu component này thông qua template ref sẽ chỉ có thể truy cập `publicData` và `publicMethod`.

</div>
