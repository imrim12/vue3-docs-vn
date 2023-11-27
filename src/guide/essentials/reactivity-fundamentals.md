---
outline: deep
---

# Nền tảng về Reactivity {#reactivity-fundamentals}

:::tip API Ưu tiên
Trang này và nhiều chương khác sau đó trong hướng dẫn chứa nội dung khác nhau cho API Options và API Composition. Tùy chọn ưu tiên hiện tại của bạn là <span class="options-api">Options API</span><span class="composition-api">Composition API</span>. Bạn có thể chuyển đổi giữa các kiểu API bằng cách sử dụng các công tắc "Ưu tiên API" ở đầu thanh bên trái.
:::

<div class="options-api">

## Khai báo Reactivity State \* {#declaring-reactive-state}

Với Option API, chúng ta sử dụng tùy chọn `data` để khai báo reactive state của một component. Giá trị tùy chọn nên là một hàm trả về một object. Vue sẽ gọi hàm khi tạo một instance component mới, và bao gồm đối tượng được trả về trong hệ thống phản ứng của nó. Bất kỳ thuộc tính cấp cao nào của đối tượng này đều được bao gồm trong instance component (`this` trong các phương thức và lifecycle hooks):

```js{2-6}
export default {
  data() {
    return {
      count: 1
    }
  },

  // `mounted` là một lifecycle hook sẽ được giải thích sau
  mounted() {
    // `this` tham chiếu đến instance component.
    console.log(this.count) // => 1

    // data cũng có thể bị thay đổi trong lifecycle hook
    this.count = 2
  }
}
```

[Thử trong Playground](https://play.vuejs.org/#eNpFUNFqhDAQ/JXBpzsoHu2j3B2U/oYPpnGtoetGkrW2iP/eRFsPApthd2Zndilex7H8mqioimu0wY16r4W+Rx8ULXVmYsVSC9AaNafz/gcC6RTkHwHWT6IVnne85rI+1ZLr5YJmyG1qG7gIA3Yd2R/LhN77T8y9sz1mwuyYkXazcQI2SiHz/7iP3VlQexeb5KKjEKEe2lPyMIxeSBROohqxVO4E6yV6ppL9xykTy83tOQvd7tnzoZtDwhrBO2GYNFloYWLyxrzPPOi44WWLWUt618txvASUhhRCKSHgbZt2scKy7HfCujGOqWL9BVfOgyI=)

Những thuộc tính này chỉ được thêm khi instance được tạo lần đầu tiên, vì vậy bạn cần đảm bảo chúng đều có mặt trong object được trả về bởi hàm `data`. Khi cần thiết, hãy sử dụng `null`, `undefined` hoặc một giá trị placeholder khác cho các thuộc tính mà giá trị mong muốn chưa có sẵn.

Bạn có thể thêm một thuộc tính mới trực tiếp vào `this` mà không cần bao gồm nó trong `data`. Tuy nhiên, các thuộc tính được thêm theo cách này sẽ không thể kích hoạt các cập nhật reactive.

Vue sử dụng tiền tố `$` khi tiết lộ các API tích hợp sẵn của nó thông qua instance component. Nó cũng dành tiền tố `_` cho các thuộc tính nội bộ. Bạn nên tránh sử dụng tên cho các thuộc tính `data` cấp cao bắt đầu bằng một trong hai ký tự này.

### Reactive Proxy và Original \* {#reactive-proxy-vs-original}

Trong Vue 3, data được tạo ra một cách reactive bằng cách tận dụng [JavaScript Proxies](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy). Người dùng đến từ Vue 2 nên biết về một số trường hợp đặc biệt sau:

```js
export default {
  data() {
    return {
      someObject: {}
    }
  },
  mounted() {
    const newObject = {}
    this.someObject = newObject

    console.log(newObject === this.someObject) // false
  }
}
```

Khi bạn truy cập `this.someObject` sau khi gán, giá trị của nó là một reactive proxy của `newObject` ban đầu. **Không giống như Vue 2, `newObject` ban đầu vẫn không bị ảnh hưởng và sẽ không được reactive: hãy luôn luôn truy cập trạng thái reactive như một thuộc tính của `this`.**

</div>

<div class="composition-api">

## Khai báo Reactivity State \*\* {#declaring-reactive-state-1}

### `ref()` \*\* {#ref}

Trong Composition API, cách khuyến khích để khai báo reactive state là sử dụng hàm [`ref()`](/api/reactivity-core#ref):

```js
import { ref } from 'vue'

const count = ref(0)
```

`ref()` nhận đối số và trả về chính nó - được bao bọc trong một đối tượng ref với thuộc tính `.value`:

```js
const count = ref(0)

console.log(count) // { value: 0 }
console.log(count.value) // 0

count.value++
console.log(count.value) // 1
```

> Xem thêm: [Ép kiểu cho Ref](/guide/typescript/composition-api#typing-ref) <sup class="vt-badge ts" />

Để truy cập ref trong template của một component, hãy khai báo và trả về chúng từ hàm `setup()` của component:

```js{5,9-11}
import { ref } from 'vue'

export default {
  // `setup` là một hook đặc biệt dành riêng cho Composition API.
  setup() {
    const count = ref(0)

    // trả về ref để template có thể truy cập
    return {
      count
    }
  }
}
```

```vue-html
<div>{{ count }}</div>
```

Chú ý rằng chúng ta **không** cần `.value` khi sử dụng ref trong template. Vì sự tiện lợi, ref được tự động unwrap khi sử dụng trong template (với một số [caveat](#caveat-when-unwrapping-in-templates)).

Bạn cũng có thể biến đổi ref trực tiếp trong các event handler:

```vue-html{1}
<button @click="count++">
  {{ count }}
</button>
```

Đối với các logic phức tạp, chúng ta có thể khai báo các hàm thay đổi ref trong cùng phạm vi và tiết lộ chúng như các phương thức cùng với state:

```js{7-10,15}
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)

    function increment() {
      // .value cần thiết khi thay đổi ref trong một hàm
      count.value++
    }

    // đừng quên trả về hàm để template có thể truy cập
    return {
      count,
      increment
    }
  }
}
```

Các phương thức được tiết lộ có thể được sử dụng như event handler:

```vue-html{1}
<button @click="increment">
  {{ count }}
</button>
```

Đây là một ví dụ trực tiếp trên [Codepen](https://codepen.io/vuejs-examples/pen/WNYbaqo), không sử dụng bất kỳ công cụ build nào.

### `<script setup>` \*\* {#script-setup}

Việc tiết lộ state và methods thủ công thông qua `setup()` có thể dài dòng. May mắn thay, chúng ta có thể tránh được việc này khi sử dụng [Single-File Components (SFCs)](/guide/scaling-up/sfc). Chúng ta có thể đơn giản hóa việc sử dụng với `<script setup>`:

```vue{1}
<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}
</script>

<template>
  <button @click="increment">
    {{ count }}
  </button>
</template>
```

[Thử trong Playground](https://play.vuejs.org/#eNo9jUEKgzAQRa8yZKMiaNcllvYe2dgwQqiZhDhxE3L3jrW4/DPvv1/UK8Zhz6juSm82uciwIef4MOR8DImhQMIFKiwpeGgEbQwZsoE2BhsyMUwH0d66475ksuwCgSOb0CNx20ExBCc77POase8NVUN6PBdlSwKjj+vMKAlAvzOzWJ52dfYzGXXpjPoBAKX856uopDGeFfnq8XKp+gWq4FAi)

Top-level imports, biến và hàm được khai báo trong `<script setup>` có thể được sử dụng tự động trong template của cùng một component. Hãy nghĩ về template như là một hàm JavaScript được khai báo trong cùng phạm vi - nó có thể tự nhiên truy cập vào tất cả mọi thứ được khai báo cùng nó.

:::tip
Đối với phần còn lại của hướng dẫn, chúng tôi sẽ chủ yếu sử dụng cú pháp SFC + `<script setup>` cho các ví dụ code về Composition API, vì đó là cách sử dụng phổ biến nhất cho các nhà phát triển Vue.

Nếu bạn không sử dụng SFC, bạn vẫn có thể sử dụng Composition API với tùy chọn [`setup()`](/api/composition-api-setup).
:::

### Tại sao lại là Ref? \*\* {#why-refs}

Bạn có thể thắc mắc tại sao chúng ta cần refs với `.value` thay vì các biến đơn giản. Để giải thích điều đó, chúng ta sẽ cần phải thảo luận ngắn gọn về cách hệ thống reactivity của Vue hoạt động.

Khi bạn sử dụng ref trong template, và thay đổi giá trị của ref, Vue sẽ tự động phát hiện thay đổi và cập nhật DOM tương ứng. Điều này được thực hiện bằng cách sử dụng một hệ thống reactivity dựa trên theo dõi các phụ thuộc. Khi một component được render lần đầu tiên, Vue **theo dõi** mọi ref được sử dụng trong quá trình render. Sau đó, khi ref bị thay đổi, nó sẽ **kích hoạt** một lần render cho các component đang theo dõi nó.

Trong JavaScript chuẩn, không có cách nào để phát hiện truy cập hoặc thay đổi các biến đơn giản. Tuy nhiên, chúng ta có thể chặn các hoạt động get và set của các thuộc tính của một object bằng cách sử dụng các phương thức getter và setter.

Thuộc tính `.value` cho phép Vue có cơ hội phát hiện khi một ref đã được truy cập hoặc thay đổi. Dưới nền tảng, Vue thực hiện việc theo dõi trong getter, và thực hiện việc kích hoạt trong setter. Về mặt khái niệm, bạn có thể nghĩ về ref như là một object có dạng như sau:

```js
// code giả, không phải cài đặt thực tế
const myRef = {
  _value: 0,
  get value() {
    track()
    return this._value
  },
  set value(newValue) {
    this._value = newValue
    trigger()
  }
}
```

Một khía cạnh quan trọng khác của refs là chúng có thể được truyền vào các hàm khác mà vẫn giữ được quyền truy cập vào giá trị mới nhất và kết nối reactivity. Điều này đặc biệt hữu ích khi tái cấu trúc logic phức tạp thành mã có thể tái sử dụng.

Hệ thống reactivity được thảo luận chi tiết hơn trong phần [Chuyên sâu về Reactivity](/guide/extras/reactivity-in-depth).
</div>

<div class="options-api">

## Khai báo phương thức \* {#declaring-methods}

<VueSchoolLink href="https://vueschool.io/lessons/methods-in-vue-3" title="Free Vue.js Methods Lesson"/>

Để thêm các phương thức vào một instance component, chúng ta sử dụng tùy chọn `methods`. Đây nên là một object chứa các phương thức mong muốn:

```js{7-11}
export default {
  data() {
    return {
      count: 0
    }
  },
  methods: {
    increment() {
      this.count++
    }
  },
  mounted() {
    // method có thể được gọi trong lifecycle hooks, hoặc trong các method khác!
    this.increment()
  }
}
```

Vue tự động ràng buộc giá trị `this` cho `methods` để luôn tham chiếu đến instance component. Điều này đảm bảo rằng một method sẽ giữ giá trị `this` đúng nếu nó được sử dụng như một event listener hoặc callback. Bạn nên tránh sử dụng arrow function khi định nghĩa `methods`, vì điều đó ngăn Vue ràng buộc giá trị `this` thích hợp:

```js
export default {
  methods: {
    increment: () => {
      // SAI: `this` không thể tham chiếu đến instance component
    }
  }
}
```

Giống như tất cả các thuộc tính khác của instance component, `methods` có thể được truy cập từ bên trong template của component. Trong template, chúng thường được sử dụng như event listener:

```vue-html
<button @click="increment">{{ count }}</button>
```

[Thử trong Playground](https://play.vuejs.org/#eNplj9EKwyAMRX8l+LSx0e65uLL9hy+dZlTWqtg4BuK/z1baDgZicsPJgUR2d656B2QN45P02lErDH6c9QQKn10YCKIwAKqj7nAsPYBHCt6sCUDaYKiBS8lpLuk8/yNSb9XUrKg20uOIhnYXAPV6qhbF6fRvmOeodn6hfzwLKkx+vN5OyIFwdENHmBMAfwQia+AmBy1fV8E2gWBtjOUASInXBcxLvN4MLH0BCe1i4Q==)

Trong ví dụ trên, phương thức `increment` sẽ được gọi khi `<button>` được click.

</div>

### Deep Reactivity {#deep-reactivity}

<div class="options-api">

Trong Vue, state được deep reactive theo mặc định. Điều này có nghĩa là bạn có thể mong đợi các thay đổi được phát hiện ngay cả khi bạn thay đổi các object hoặc mảng lồng nhau:

```js
export default {
  data() {
    return {
      obj: {
        nested: { count: 0 },
        arr: ['foo', 'bar']
      }
    }
  },
  methods: {
    mutateDeeply() {
      // các thay đổi này sẽ hoạt động như mong đợi.
      this.obj.nested.count++
      this.obj.arr.push('baz')
    }
  }
}
```

</div>

<div class="composition-api">

Ref có thể giữ bất kỳ kiểu giá trị nào, bao gồm các object lồng nhau, mảng hoặc các cấu trúc dữ liệu tích hợp sẵn của JavaScript như `Map`.

Ref sẽ tự động khiến giá trị của nó trở nên deep reactive. Điều này có nghĩa là bạn có thể mong đợi các thay đổi được phát hiện ngay cả khi bạn thay đổi các object hoặc mảng lồng nhau:

```js
import { ref } from 'vue'

const obj = ref({
  nested: { count: 0 },
  arr: ['foo', 'bar']
})

function mutateDeeply() {
  // các thay đổi này sẽ hoạt động như mong đợi.
  obj.value.nested.count++
  obj.value.arr.push('baz')
}
```

Các giá trị không nguyên thủy được chuyển thành các proxy reactive thông qua [`reactive()`](#reactive), được thảo luận bên dưới.

Việc thoát khỏi deep reactivity có thể được thực hiện bằng cách sử dụng [shallow refs](/api/reactivity-advanced#shallowref). Với shallow refs, chỉ có truy cập `.value` được theo dõi cho reactivity. Shallow refs có thể được sử dụng để tối ưu hóa hiệu suất bằng cách tránh chi phí quan sát của các object lớn, hoặc trong các trường hợp mà trạng thái bên trong được quản lý bởi một thư viện bên ngoài.

Đọc thêm:

- [Giảm thiểu chi phí Reactivity cho các cấu trúc lớn](/guide/best-practices/performance#reduce-reactivity-overhead-for-large-immutable-structures)
- [Tích hợp với các hệ thống State bên ngoài](/guide/extras/reactivity-in-depth#integration-with-external-state-systems)

</div>

### Thời gian update DOM {#dom-update-timing}

Khi bạn thay đổi state reactive, DOM sẽ được cập nhật tự động. Tuy nhiên, cần lưu ý rằng các cập nhật DOM không được áp dụng đồng bộ. Thay vào đó, Vue đưa chúng vào bộ đệm cho đến "tick tiếp theo" trong chu kỳ cập nhật để đảm bảo mỗi component chỉ cập nhật một lần bất kể bạn đã thay đổi trạng thái bao nhiêu lần.

Để chờ đợi cập nhật DOM hoàn tất sau một thay đổi trạng thái, bạn có thể sử dụng API toàn cục [`nextTick()`](/api/general#nexttick):

<div class="composition-api">

```js
import { nextTick } from 'vue'

async function increment() {
  count.value++
  await nextTick()
  // Bây giờ DOM đã được cập nhật
}
```

</div>
<div class="options-api">

```js
import { nextTick } from 'vue'

export default {
  methods: {
    async increment() {
      this.count++
      await nextTick()
      // Bây giờ DOM đã được cập nhật
    }
  }
}
```

</div>

<div class="composition-api">

## `reactive()` \*\* {#reactive}

Có một cách khác để khai báo reactive state, với API `reactive()`. Khác với ref, `reactive()` làm cho một object trở nên reactive:

```js
import { reactive } from 'vue'

const state = reactive({ count: 0 })
```

> Xem thêm: [Ép kiểu cho Reactive](/guide/typescript/composition-api#typing-reactive) <sup class="vt-badge ts" />

Cách sử dụng trong template:

```vue-html
<button @click="state.count++">
  {{ state.count }}
</button>
```

Reactive object là [JavaScript Proxies](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) và hoạt động giống như các object bình thường. Sự khác biệt là Vue có thể chặn truy cập và thay đổi tất cả các thuộc tính của một object reactive để theo dõi và kích hoạt reactivity.

`reactive()` chuyển đổi object theo chiều sâu: các object lồng nhau cũng được bao bọc trong `reactive()` khi được truy cập. Nó cũng được gọi bởi `ref()` nội bộ khi giá trị ref là một object. Tương tự như shallow refs, cũng có API [`shallowReactive()`](/api/reactivity-advanced#shallowreactive) để tắt deep reactivity.

### Reactive Proxy so với Original \*\* {#reactive-proxy-vs-original-1}

Lưu ý rằng giá trị trả về từ `reactive()` là một [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) của object ban đầu, không phải là object ban đầu:

```js
const raw = {}
const proxy = reactive(raw)

// proxy không bằng với object ban đầu.
console.log(proxy === raw) // false
```

Chỉ có proxy là reactive - thay đổi object ban đầu sẽ không kích hoạt cập nhật. Do đó, cách tốt nhất khi làm việc với hệ thống reactivity của Vue là **chỉ sử dụng các phiên bản được proxy của state của bạn**.

Để đảm bảo truy cập nhất quán vào proxy, gọi `reactive()` trên cùng một object luôn trả về cùng một proxy, và gọi `reactive()` trên một proxy hiện có cũng trả về cùng một proxy:

```js
// calling reactive() on the same object returns the same proxy
console.log(reactive(raw) === proxy) // true

// calling reactive() on a proxy returns itself
console.log(reactive(proxy) === proxy) // true
```

Quy tắc này cũng áp dụng cho các object lồng nhau. Do deep reactivity, các object lồng nhau bên trong một object reactive cũng là các proxy:

```js
const proxy = reactive({})

const raw = {}
proxy.nested = raw

console.log(proxy.nested === raw) // false
```

### Giới hạn của `reactive()` \*\* {#limitations-of-reactive}

`reactive()` API có một số hạn chế:

1. **Giới hạn các kiểu giá trị:** nó chỉ hoạt động cho các kiểu object (object, mảng và [collection types](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects#keyed_collections) như `Map` và `Set`). Nó không thể giữ các kiểu nguyên thủy như `string`, `number` hoặc `boolean`.

2. **Không thể thay thế toàn bộ object:** vì reactivity của Vue theo dõi các hoạt động thông qua việc truy cập thuộc tính, chúng ta phải luôn giữ cùng một tham chiếu đến object reactive. Điều này có nghĩa là chúng ta không thể dễ dàng "thay thế" một object reactive vì kết nối reactivity với tham chiếu đầu tiên bị mất:

   ```js
   let state = reactive({ count: 0 })

   // 
   // the above reference ({ count: 0 }) is no longer being tracked
   // (kết nối reactivity bị mất!)
   state = reactive({ count: 1 })
   ```

3. **Không thể sử dụng destructure:** khi chúng ta destructure một thuộc tính kiểu nguyên thuỷ của một reactive object thành một biến cục bộ, hoặc khi 

   ```js
   const state = reactive({ count: 0 })

   // count bi ngắt kết nối với state.count khi destructure.
   let { count } = state
   // không ảnh hưởng đến state.count
   count++

   // hàm nhận một số và
   // sẽ không theo dõi các thay đổi của state.count
   // chúng ta phải truyền toàn bộ object vào để giữ được reactivity
   callSomeFunction(state.count)
   ```

Do các hạn chế này, chúng tôi khuyến khích sử dụng `ref()` như API chính để khai báo reactive state.

## Chi tiết thêm về Unwrapping Ref \*\* {#additional-ref-unwrapping-details}

### Như thuộc tính của một object reactive \*\* {#as-a-property-of-a-reactive-object}

Ref sẽ tự động unwrap khi được truy cập hoặc thay đổi như một thuộc tính của một object reactive. Nói cách khác, nó hoạt động giống như một thuộc tính bình thường:

```js
const count = ref(0)
const state = reactive({
  count
})

console.log(state.count) // 0

state.count = 1
console.log(count.value) // 1
```

Nếu một ref mới được gán cho một thuộc tính liên kết với một ref hiện có, nó sẽ thay thế ref cũ:

```js
const otherCount = ref(2)

state.count = otherCount
console.log(state.count) // 2
// ref cũ không còn được theo dõi
console.log(count.value) // 1
```

Việc unwrap ref chỉ xảy ra khi nó được lồng trong một deep reactive object. Nó không áp dụng khi truy cập như một thuộc tính của một [shallow reactive object](/api/reactivity-advanced#shallowreactive).

### Những điều cần lưu ý về Mảng và Collection \*\* {#caveat-in-arrays-and-collections}

Không giống như các object reactive, không có unwrap tự động nào được thực hiện khi một ref được truy cập như một phần tử của một mảng reactive hoặc một kiểu bộ sưu tập khác như `Map`:

```js
const books = reactive([ref('Vue 3 Guide')])
// cần .value ở đây
console.log(books[0].value)

const map = reactive(new Map([['count', ref(0)]]))
// cần .value ở đây
console.log(map.get('count').value)
```

### Những điều cần lưu ý về Unwrapping trong Templates \*\* {#caveat-when-unwrapping-in-templates}

Ref unwrap trong template chỉ áp dụng nếu ref là một thuộc tính cấp cao trong ngữ cảnh render template.
Ref unwrapping in templates only applies if the ref is a top-level property in the template render context.

Trong ví dụ dưới đây, `count` và `object` là các thuộc tính cấp cao, nhưng `object.id` thì không:

```js
const count = ref(0)
const object = { id: ref(1) }
```

Do đó, biểu thức này sẽ hoạt động như mong đợi:

```vue-html
{{ count + 1 }}
```

...trong khi biểu thức này thì **KHÔNG**:

```vue-html
{{ object.id + 1 }}
```

Kết quả render sẽ là `[object Object]1` vì `object.id` không được unwrap khi đánh giá biểu thức và vẫn là một object ref. Để sửa lỗi này, chúng ta có thể destructure `id` thành một thuộc tính cấp cao:

```js
const { id } = object
```

```vue-html
{{ id + 1 }}
```

Bây giờ kết quả render sẽ là `2`.

Một điều cần lưu ý khác đó là ref sẽ được unwrap nếu nó là giá trị cuối cùng của một text interpolation (ví dụ: một thẻ <code v-pre>{{ }}</code>), vì vậy ví dụ sau sẽ render `1`:

```vue-html
{{ object.id }}
```

Điều này chỉ là một tính năng tiện lợi của text interpolation và tương đương với <code v-pre>{{ object.id.value }}</code>.

</div>

<div class="options-api">

### Stateful Methods \* {#stateful-methods}

Trong một số trường hợp, chúng ta có thể cần tạo một hàm method động, ví dụ như tạo một event handler debounced:

```js
import { debounce } from 'lodash-es'

export default {
  methods: {
    // Debouncing với Lodash
    click: debounce(function () {
      // ... phản hồi cho click ...
    }, 500)
  }
}
```

Tuy nhiên, cách tiếp cận này có vấn đề với các component được tái sử dụng vì một hàm debounced là **stateful**: nó duy trì một số trạng thái nội bộ theo thời gian. Nếu nhiều instance component chia sẻ cùng một hàm debounced, chúng sẽ can thiệp lẫn nhau.

Để giữ cho debounced function của mỗi instance component độc lập với các instance khác, chúng ta có thể tạo ra phiên bản debounced trong `created` lifecycle hook:

```js
export default {
  created() {
    // mỗi instance bây giờ có một bản sao riêng của handler debounced
    this.debouncedClick = _.debounce(this.click, 500)
  },
  unmounted() {
    // cũng nên hủy bỏ timer khi component bị xóa
    this.debouncedClick.cancel()
  },
  methods: {
    click() {
      // ... phản hồi cho click ...
    }
  }
}
```

</div>
