# Quản lý State  {#state-management}

## Quản lý State là gì? {#what-is-state-management}

Nói theo một cách kỹ thuật, mỗi instance component Vue đã "quản lý" riêng reactive state của nó. Hãy xem xét một component đơn giản như một ví dụ:

<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue'

// state
const count = ref(0)

// actions
function increment() {
  count.value++
}
</script>

<!-- view -->
<template>{{ count }}</template>
```

</div>
<div class="options-api">

```vue
<script>
export default {
  // state
  data() {
    return {
      count: 0
    }
  },
  // actions
  methods: {
    increment() {
      this.count++
    }
  }
}
</script>

<!-- view -->
<template>{{ count }}</template>
```

</div>

Mỗi component là một unit độc lập với các thành phần sau:

- **state**, nguồn tin cậy để thúc đẩy ứng dụng của chúng ta;
- **view**, một cách khai báo để ánh xạ **state**;
- **actions**, các cách để có thể thay đổi **state** phản ứng với các đầu vào của người dùng từ **view**.

Đây là một biểu diễn đơn giản của khái niệm "one-way data flow":

<p style="text-align: center">
  <img alt="state flow diagram" src="./images/state-flow.png" width="252px" style="margin: 40px auto">
</p>

Tuy nhiên, sự đơn giản bắt đầu bị phá vỡ khi chúng ta có **nhiều component chia sẻ chung một state**:

1. Nhiều view có thể phụ thuộc vào cùng một state.
2. Các actions từ các view khác nhau có thể cần thay đổi cùng một state.

Đối với trường hợp thứ nhất, có một giải pháp xử lý tạm thời đó là "nâng" state được dùng chung lên một component tổ tiên chung, và sau đó truyền nó xuống dưới dạng props. Tuy nhiên, việc này nhanh chóng trở nên phiền toái đối với cây component có các cấu trúc phân cấp sâu, dẫn đến một vấn đề khác được gọi là [Prop Drilling](/guide/components/provide-inject#prop-drilling).

Đối với trường hợp thứ hai, chúng ta thường thấy mình phải sử dụng các giải pháp như truy cập trực tiếp vào các instance cha / con thông qua các template refs, hoặc cố gắng biến đổi và đồng bộ hóa nhiều bản sao của state thông qua các sự kiện được phát ra. Cả hai pattern này đều rất dễ gãy và nhanh chóng dẫn đến việc code rất khó thể bảo trì.

Một giải pháp đơn giản và trực tiếp hơn đó là trích xuất state được dùng chung ra khỏi các component, và quản lý nó trong một singleton toàn cục. Với điều này, cây component của chúng ta trở thành một "view" lớn, và bất kỳ component nào cũng có thể truy cập vào state hoặc kích hoạt các actions, bất kể chúng ở đâu trong cây!

## Quản lý State đơn giản với Options API {#simple-state-management-with-reactivity-api}

<div class="options-api">

Trong Options API, chúng ta có thể sử dụng `data()` để khai báo reactive data. Bên dưới, object được trả về bởi `data()` sẽ được khiến cho reactive thông qua hàm [`reactive()`](/api/reactivity-core#reactive) - cũng là một public API khả dụng.

</div>

Nếu bạn có một state cần được sử dụng bởi nhiều instance, bạn có thể sử dụng [`reactive()`](/api/reactivity-core#reactive) để tạo một object reactive, và sau đó import nó vào nhiều component:

```js
// store.js
import { reactive } from 'vue'

export const store = reactive({
  count: 0
})
```

<div class="composition-api">

```vue
<!-- ComponentA.vue -->
<script setup>
import { store } from './store.js'
</script>

<template>From A: {{ store.count }}</template>
```

```vue
<!-- ComponentB.vue -->
<script setup>
import { store } from './store.js'
</script>

<template>From B: {{ store.count }}</template>
```

</div>
<div class="options-api">

```vue
<!-- ComponentA.vue -->
<script>
import { store } from './store.js'

export default {
  data() {
    return {
      store
    }
  }
}
</script>

<template>From A: {{ store.count }}</template>
```

```vue
<!-- ComponentB.vue -->
<script>
import { store } from './store.js'

export default {
  data() {
    return {
      store
    }
  }
}
</script>

<template>From B: {{ store.count }}</template>
```

</div>

Bất kỳ khi nào object `store` bị thay đổi, cả `<ComponentA>` và `<ComponentB>` sẽ tự động cập nhật view của chúng - bây giờ chúng ta có một nguồn tin cậy duy nhất.

Tuy nhiên, điều này cũng có nghĩa là bất kỳ component nào import `store` cũng có thể thay đổi nó bất cứ khi nào chúng muốn:

```vue-html{2}
<template>
  <button @click="store.count++">
    From B: {{ store.count }}
  </button>
</template>
```

Mặc dù điều này hoạt động đối với các trường hợp đơn giản, nhưng global state có thể bị thay đổi tùy ý bởi bất kỳ component nào sẽ khó có thể bảo trì trong tương lai. Để đảm bảo logic làm thay đổi state được tập trung như chính bản thân state, việc định nghĩa các phương thức actions trong store với tên biểu thị ý định được khuyến khích:

```js{6-8}
// store.js
import { reactive } from 'vue'

export const store = reactive({
  count: 0,
  increment() {
    this.count++
  }
})
```

```vue-html{2}
<template>
  <button @click="store.increment()">
    From B: {{ store.count }}
  </button>
</template>
```

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNrNkk1uwyAQha8yYpNEiUzXllPVrtRTeJNSqtLGgGBsVbK4ewdwnT9FWWSTFczwmPc+xMhqa4uhl6xklRdOWQQvsbfPrVadNQ7h1dCqpcYaPp3pYFHwQyteXVxKm0tpM0krnm3IgAqUnd3vUFIFUB1Z8bNOkzoVny+wDTuNcZ1gBI/GSQhzqlQX3/5Gng81pA1t33tEo+FF7JX42bYsT1BaONlRguWqZZMU4C261CWMk3EhTK8RQphm8Twse/BscoUsvdqDkTX3kP3nI6aZwcmdQDUcMPJPabX8TQphtCf0RLqd1csxuqQAJTxtYnEUGtIpAH4pn1Ou17FDScOKhT+QNAVM)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNrdU8FqhDAU/JVHLruyi+lZ3FIt9Cu82JilaTWR5CkF8d8bE5O1u1so9FYQzAyTvJnRTKTo+3QcOMlIbpgWPT5WUnS90gjPyr4ll1jAWasOdim9UMum3a20vJWWqxSgkvzTyRt+rocWYVpYFoQm8wRsJh+viHLBcyXtk9No2ALkXd/WyC0CyDfW6RVTOiancQM5ku+x7nUxgUGlOcwxn8Ppu7HJ7udqaqz3SYikOQ5aBgT+OA9slt9kasToFnb5OiAqCU+sFezjVBHvRUimeWdT7JOKrFKAl8VvYatdI6RMDRJhdlPtWdQf5mdQP+SHdtyX/IftlH9pJyS1vcQ2NK8ZivFSiL8BsQmmpMG1s1NU79frYA1k8OD+/I3pUA6+CeNdHg6hmoTMX9pPSnk=)

</div>

:::tip
Lưu ý rằng trình xử lý sự kiện click phải sử dụng `store.increment()` với dấu ngoặc đơn - điều này là cần thiết để gọi phương thức với ngữ cảnh `this` đúng vì nó không phải là một phương thức của component.
:::

Mặc dù chúng ta đã sử dụng một reactive object để làm store, bạn cũng có thể chia sẻ reactive state được tạo bằng các [Reactivity APIs](/api/reactivity-core) khác như `ref()` hoặc `computed()`, hoặc thậm chí trả về global state từ một [Composable](/guide/reusability/composables):

```js
import { ref } from 'vue'

// global state, created in module scope
const globalCount = ref(1)

export function useCount() {
  // local state, created per-component
  const localCount = ref(1)

  return {
    globalCount,
    localCount
  }
}
```

Việc hệ thống Vue reactivity được tách rời khỏi mô hình component khiến nó cực kỳ linh hoạt.

## Cân nhắc đối với SSR {#ssr-considerations}

Nếu bạn đang xây dựng một ứng dụng sử dụng [Server-Side Rendering (SSR)](./ssr), pattern trên có thể dẫn đến các vấn đề do store là một singleton được chia sẻ trên nhiều request. Điều này được thảo luận [chi tiết hơn](./ssr#cross-request-state-pollution) trong hướng dẫn SSR.

## Pinia {#pinia}

Mặc dù giải pháp quản lý state tự làm của chúng ta có thể đủ cho các trường hợp đơn giản, nhưng còn nhiều vấn dề cần xem xét hơn trong các ứng dụng production quy mô lớn:

- Convention chắc chắn hơn cho việc hợp tác nhóm
- Tích hợp với Vue DevTools, bao gồm timeline, in-component inspection, và time-travel debugging
- Thay Module "Nóng"
- Hỗ trợ Server-Side Rendering

[Pinia](https://pinia.vuejs.org) là một thư viện quản lý state có thể thực hiện tất cả những điều trên. Nó được bảo trì bởi nhóm Vue core, và hoạt động với cả Vue 2 và Vue 3.

Những người dùng hiện tại có thể đã quen thuộc với [Vuex](https://vuex.vuejs.org/), thư viện quản lý state trước đây được khuyến khích cho Vue. Với Pinia đảm nhận vai trò tương tự trong hệ sinh thái, Vuex hiện đang ở chế độ bảo trì. Nó vẫn hoạt động, nhưng sẽ không nhận được các tính năng mới nữa. Chúng tôi khuyến khích sử dụng Pinia cho các ứng dụng mới.

Pinia ban đầu được tạo ra như một khám phá về những gì phiên bản tiếp theo của Vuex có thể trông giống như thế nào, kết hợp nhiều ý tưởng từ các cuộc thảo luận của nhóm core cho Vuex 5. Cuối cùng, chúng tôi nhận ra rằng Pinia đã thực hiện hầu hết những gì chúng tôi muốn trong Vuex 5, và quyết định khiến nó thành khuyến nghị mới thay thế.

So với Vuex, Pinia cung cấp một API đơn giản hơn với ít cách thức hơn, cung cấp các API theo kiểu Composition-API, và quan trọng nhất, có hỗ trợ suy luận kiểu tốt khi được sử dụng với TypeScript.
