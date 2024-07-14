# Watchers {#watchers}

## Ví dụ cơ bản {#basic-example}

Các thuộc tính computed cho phép chúng ta khai báo các giá trị phụ phụ thuộc vào các giá trị khác. Tuy nhiên, có những trường hợp chúng ta cần thực hiện "hiệu ứng phụ" khi có sự thay đổi trạng thái - ví dụ: thay đổi DOM, hoặc thay đổi một phần khác của trạng thái dựa trên kết quả của một hoạt động bất đồng bộ.

<div class="options-api">

Với Options API, chúng ta có thể sử dụng tùy chọn [`watch`](/api/options-state#watch) để kích hoạt một hàm mỗi khi một thuộc tính reactive thay đổi:

```js
export default {
  data() {
    return {
      question: '',
      answer: 'Câu hỏi thường chứa một dấu chấm hỏi ở cuối câu. ;-)'
    }
  },
  watch: {
    // mỗi khi câu hỏi thay đổi, hàm này sẽ được gọi
    question(newQuestion, oldQuestion) {
      if (newQuestion.includes('?')) {
        this.getAnswer()
      }
    }
  },
  methods: {
    async getAnswer() {
      this.answer = 'Để tui suy nghĩ...'
      try {
        const res = await fetch('https://yesno.wtf/api')
        this.answer = (await res.json()).answer
      } catch (error) {
        this.answer = 'Lỗi! Không thể truy cập vào API. ' + error
      }
    }
  }
}
```

```vue-html
<p>
  Hãy đặt một câu hỏi có thể trả lời bằng "có" hoặc "không":
  <input v-model="question" />
</p>
<p>{{ answer }}</p>
```

[Thử trong Playground](https://play.vuejs.org/#eNptUk2PmzAQ/SuvXAA1sdVrmt0qqnroqa3UIxcLhuCGjKk/wkYR/70OBJLuroRkPDPvzbznuSS7rhOnQMkm2brS6s4/F0wvnbEeFdUqtB6XgoFKeZXl0z9gyQfL8w34G8h5bXiDNF3NQcWuJxtDv25Zh+CCatszSsNeaYZakDgqexD4vM7TCT9cj2Ek65Uvm83cTUr0DTGdyN7RZaN4T24F32iHOnA5hnvdtrCBJ+RcnTH180wrmLaaL4s+QNd4LBOaK3r5UWfplzTHM9afHmoxdhV78rtRcpbPmVHEf1qO5BtTuUWNcmcu8QC9046kk4l4Qvq70XzQvBdC3CyKJfb8OEa01fn4OC7Wq15pj5qidVnaeN+5jZRncmxE72upOp0uY77ulU3gSCT+uOhXnt9yiy6U1zdBRtYa+9aK+9TfrgUf8NWEtgKbK6mKQN8Qdj+/C6T4iJHkXcsKjt9WLpsZL56OXas8xRuw7cYD2LlDXKYoT7K5b+OU22rugsdpfTQVtU9FMueLBHKikRNPpLtcbnuLYZjCW7m0TIZ/92UFiQ==)

Tuỳ chọn `watch` cũng hỗ trợ một đường dẫn được phân tách bằng dấu chấm như là một key:

```js
export default {
  watch: {
    // Lưu ý: chỉ hỗ trợ đường dẫn đơn giản. Không hỗ trợ biểu thức.
    'some.nested.key'(newValue) {
      // ...
    }
  }
}
```

</div>

<div class="composition-api">

Với Composition API, chúng ta có thể sử dụng hàm [`watch`](/api/reactivity-core#watch) để kích hoạt một hàm mỗi khi một thuộc tính reactive thay đổi:

```vue
<script setup>
import { ref, watch } from 'vue'

const question = ref('')
const answer = ref('Câu hỏi thường chứa một dấu chấm hỏi ở cuối câu. ;-)')

// watch chạy trực tiếp trên một ref
watch(question, async (newQuestion, oldQuestion) => {
  if (newQuestion.indexOf('?') > -1) {
    answer.value = 'Để tui suy nghĩ...'
    try {
      const res = await fetch('https://yesno.wtf/api')
      answer.value = (await res.json()).answer
    } catch (error) {
      answer.value = 'Lỗi! Không thể truy cập vào API. ' + error
    }
  }
})
</script>

<template>
  <p>
    Hãy đặt một câu hỏi có thể trả lời bằng "có" hoặc "không":
    <input v-model="question" />
  </p>
  <p>{{ answer }}</p>
</template>
```

[Thử trong Playground](https://play.vuejs.org/#eNplkkGPmzAQhf/KKxdA3Rj1mpJUUdVDT22lHrlYxDRuYOzaJjRC/PcdxyGr3b2A7PfmmzcMc3awVlxGlW2z2rdO2wCvwmj3DenBGhcww6nuCZMM7QkLOmcG5FyRN9RQa8gH/BuVD9oQdtFb5Hm5KpL8pNx6/+vu8xj9KPv+CnYFqQnyhTFIdxb4vCkjpaFb32JVnyD9lVoUpKaVVmK3x9wQoLtXgtB0VP9/cOMveYk9Np/K5MM9l7jIflScLv990nTW9EcIwXNFR3DX1YwYk4dxyrNXTlIHdCrGyk8hWL+tqqvyZMQUukpaHYOnujdtilTLHPHXGyrKUiRH8i9obx+5UM4Z98j6Pu23qH/AVzP2R5CJRMl14aRw+PldIMdH3Bh3bnzxY+FcdZW2zPvlQ1CD7WVQfALquPToP/gzL4RHqsg89rJNWq3JjgGXzWCOqt812ao3GaqEqRKHcfO8/gDLkq7r6tEyW54Bf5TTlg==)

### Kiểu dữ liệu nguồn của Watch {#watch-source-types}

Đối số đầu tiên của `watch` có thể là các "nguồn" reactive khác nhau: nó có thể là một ref (bao gồm cả ref computed), một object reactive, một getter function, hoặc một mảng của nhiều nguồn:

```js
const x = ref(0)
const y = ref(0)

// ref
watch(x, (newX) => {
  console.log(`x is ${newX}`)
})

// getter
watch(
  () => x.value + y.value,
  (sum) => {
    console.log(`sum of x + y is: ${sum}`)
  }
)

// mảng của nhiều nguồn
watch([x, () => y.value], ([newX, newY]) => {
  console.log(`x is ${newX} and y is ${newY}`)
})
```

Lưu ý rằng bạn không thể theo dõi một thuộc tính của một object reactive như sau:

```js
const obj = reactive({ count: 0 })

// cái này sẽ không hoạt động vì chúng ta đang truyền một số cho watch()
watch(obj.count, (count) => {
  console.log(`count is: ${count}`)
})
```

Thay vào đó, hãy sử dụng một getter:

```js
// thay vào đó, hãy sử dụng một getter:
watch(
  () => obj.count,
  (count) => {
    console.log(`count is: ${count}`)
  }
)
```

</div>

## Deep Watchers {#deep-watchers}

<div class="options-api">

`watch` mặc định là shallow: callback chỉ được kích hoạt khi thuộc tính được theo dõi được gán một giá trị mới - nó sẽ không kích hoạt trên các thay đổi thuộc tính lồng nhau. Nếu bạn muốn callback được kích hoạt trên tất cả các thay đổi lồng nhau, bạn cần sử dụng một deep watcher:

```js
export default {
  watch: {
    someObject: {
      handler(newValue, oldValue) {
        // Lưu ý: `newValue` sẽ bằng `oldValue` ở
        // trên các thay đổi lồng nhau miễn là object chính nó
        // chưa được thay thế.
      },
      deep: true
    }
  }
}
```

</div>

<div class="composition-api">

Khi bạn gọi `watch()` trực tiếp trên một object reactive, nó sẽ tự động tạo một deep watcher - callback sẽ được kích hoạt trên tất cả các thay đổi lồng nhau:

```js
const obj = reactive({ count: 0 })

watch(obj, (newValue, oldValue) => {
  // kích hoạt trên các thay đổi thuộc tính lồng nhau
  // Lưu ý: `newValue` sẽ bằng `oldValue` ở đây
  // trên các thay đổi lồng nhau miễn là object chính nó
})

obj.count++
```

Việc này nên được phân biệt với một getter trả về một object reactive - trong trường hợp này, callback chỉ được kích hoạt nếu getter trả về một object khác:

```js
watch(
  () => state.someObject,
  () => {
    // chỉ được kích hoạt khi state.someObject bị thay thế
  }
)
```

Bạn có thể, tuy nhiên, ép buộc trường hợp thứ hai vào một deep watcher bằng cách sử dụng tùy chọn `deep` một cách rõ ràng:

```js
watch(
  () => state.someObject,
  (newValue, oldValue) => {
    // Lưu ý: `newValue` sẽ bằng `oldValue` ở đây
    // *trừ khi* state.someObject đã được thay thế
  },
  { deep: true }
)
```

</div>

:::warning Cẩn thận khi sử dụng
Deep watch cần phải duyệt qua tất cả các thuộc tính lồng nhau trong object được theo dõi, và có thể tốn kém khi được sử dụng trên các cấu trúc dữ liệu lớn. Hãy sử dụng nó chỉ khi cần thiết và cẩn thận với các ảnh hưởng về hiệu năng.
:::

## Eager Watchers {#eager-watchers}

`watch` được thiết lập mặc định là lazy: callback sẽ không được gọi cho đến khi nguồn được theo dõi đã thay đổi. Nhưng trong một số trường hợp, chúng ta có thể muốn cùng một logic callback được chạy một cách nhanh chóng - ví dụ: chúng ta có thể muốn lấy một số dữ liệu ban đầu, và sau đó lấy lại dữ liệu mỗi khi trạng thái liên quan thay đổi.
`watch` is lazy by default: the callback won't be called until the watched source has changed. But in some cases we may want the same callback logic to be run eagerly - for example, we may want to fetch some initial data, and then re-fetch the data whenever relevant state changes.

<div class="options-api">

Chúng ta có thể ép buộc callback của một watcher được gọi ngay lập tức bằng cách khai báo nó bằng một object với một hàm `handler` và tùy chọn `immediate: true`:

```js
export default {
  // ...
  watch: {
    question: {
      handler(newQuestion) {
        // cái này sẽ được chạy ngay lập tức khi component được tạo.
      },
      // ép buộc gọi callback ngay lập tức
      immediate: true
    }
  }
  // ...
}
```

Việc thực thi ban đầu của hàm xử lý sẽ xảy ra ngay trước khi `created` hook. Vue lúc này đã xử lý các tùy chọn `data`, `computed`, và `methods`, vì vậy các thuộc tính này sẽ có sẵn trong lần gọi đầu tiên.

</div>

<div class="composition-api">

Chúng ta có thể ép buộc callback của một watcher được gọi ngay lập tức bằng cách sử dụng tùy chọn `immediate: true`:

```js
watch(source, (newValue, oldValue) => {
  // được thực thi ngay lập tức, và sau đó lại chạy khi `source` thay đổi
}, { immediate: true })
```

</div>

<div class="composition-api">

## `watchEffect()` \*\* {#watcheffect}

Việc sử dụng lại cùng một reactive state trong callback của watcher là phổ biến. Ví dụ, hãy xem xét đoạn code sau, sử dụng một watcher để tải một nguồn từ xa mỗi khi `todoId` ref thay đổi:

```js
const todoId = ref(1)
const data = ref(null)

watch(todoId, async () => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  )
  data.value = await response.json()
}, { immediate: true })
```

Đặc biệt, hãy chú ý cách watcher sử dụng `todoId` hai lần, một lần là nguồn và một lần nữa trong callback.

Việc này có thể được đơn giản hóa với [`watchEffect()`](/api/reactivity-core#watcheffect). `watchEffect()` cho phép chúng ta theo dõi các phụ thuộc reactive của callback một cách tự động. Watcher ở trên có thể được viết lại như sau:

```js
watchEffect(async () => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  )
  data.value = await response.json()
})
```

Tại đây, callback sẽ được chạy ngay lập tức, không cần phải chỉ định `immediate: true`. Trong quá trình thực thi, nó sẽ tự động theo dõi `todoId.value` như là một phụ thuộc (tương tự như các thuộc tính computed). Khi nào `todoId.value` thay đổi, callback sẽ được chạy lại. Với `watchEffect()`, chúng ta không cần phải truyền `todoId` một cách rõ ràng như là một giá trị nguồn.

Bạn có thể xem qua [ví dụ sau](/examples/#fetching-data) của `watchEffect()` và reactive data-fetching.

Với những ví dụ như thế này, chỉ có một phụ thuộc, lợi ích của `watchEffect()` tương đối nhỏ. Nhưng với những watcher có nhiều phụ thuộc, việc sử dụng `watchEffect()` loại bỏ gánh nặng phải duy trì danh sách các phụ thuộc một cách thủ công. Ngoài ra, nếu bạn cần theo dõi nhiều thuộc tính trong một cấu trúc dữ liệu lồng nhau, `watchEffect()` có thể hiệu quả hơn một deep watcher, vì nó chỉ theo dõi các thuộc tính được sử dụng trong callback, thay vì đệ quy theo dõi tất cả chúng.

:::tip
`watchEffect()` chỉ theo dõi các phụ thuộc trong quá trình thực thi **đồng bộ** của callback. Khi sử dụng nó với một callback bất đồng bộ, chỉ các thuộc tính được truy cập trước lần `await` đầu tiên sẽ được theo dõi.
:::

### `watch` so với `watchEffect` {#watch-vs-watcheffect}

`watch` và `watchEffect` đều cho phép chúng ta thực hiện các hiệu ứng phụ một cách reactive. Sự khác biệt chính giữa chúng là cách theo dõi các phụ thuộc reactive của chúng:

- `watch` chỉ theo dõi các nguồn được theo dõi một cách rõ ràng. Nó sẽ không theo dõi bất cứ thứ gì được truy cập trong callback. Ngoài ra, callback chỉ được kích hoạt khi nguồn thực sự thay đổi. `watch` tách riêng việc theo dõi các phụ thuộc và hiệu ứng phụ, cho phép chúng ta kiểm soát chính xác hơn khi nào callback nên được kích hoạt.

- `watchEffect` lại kết hợp cả việc theo dõi các phụ thuộc và hiệu ứng phụ vào chung một giai đoạn. Nó tự động theo dõi mọi thuộc tính reactive được truy cập trong quá trình thực thi đồng bộ của callback. Điều này thuận tiện hơn và thường dẫn đến code ngắn gọn hơn, nhưng khiến các phụ thuộc reactive của nó ít rõ ràng hơn.

</div>

## Thời điểm xử lý Callback {#callback-flush-timing}

Khi bạn biến đổi reactive state, nó có thể kích hoạt cả cập nhật component Vue và các callback watcher được tạo.

Theo mặc định, các callback watcher được tạo bởi `watch()` và `watchEffect()` được gọi **trước** khi component Vue được cập nhật. Điều này có nghĩa là nếu bạn cố gắng truy cập DOM trong một callback watcher, DOM sẽ ở trạng thái trước khi Vue áp dụng bất kỳ cập nhật nào.

Nếu bạn muốn truy cập DOM trong một callback watcher **sau** khi Vue đã cập nhật nó, bạn cần chỉ định tùy chọn `flush: 'post'`:

<div class="options-api">

```js
export default {
  // ...
  watch: {
    key: {
      handler() {},
      flush: 'post'
    }
  }
}
```

</div>

<div class="composition-api">

```js
watch(source, callback, {
  flush: 'post'
})

watchEffect(callback, {
  flush: 'post'
})
```

Post-flush `watchEffect()` cũng có một bí danh tiện lợi, `watchPostEffect()`:

```js
import { watchPostEffect } from 'vue'

watchPostEffect(() => {
  /* thực thi sau khi Vue cập nhật */
})
```

</div>

<div class="options-api">

## `this.$watch()` \* {#this-watch}

Việc tạo watcher một cách cụ thể là khả thi bằng cách sử dụng [phương thức `$watch()`](/api/component-instance#watch) trên một instance component:
It's also possible to imperatively create watchers using the [`$watch()` instance method](/api/component-instance#watch):

```js
export default {
  created() {
    this.$watch('question', (newQuestion) => {
      // ...
    })
  }
}
```

Việc này rất hữu ích khi bạn cần tạo một watcher một cách có điều kiện, hoặc chỉ thẽo dõi một vài thứ để phản hồi với sự tương tác của người dùng. Nó cũng cho phép bạn dừng watcher sớm.

</div>

## Dừng Watcher {#stopping-a-watcher}

<div class="options-api">

Watcher được khai báo bằng tùy chọn `watch` hoặc phương thức `$watch()` sẽ tự động dừng khi component chủ bị unmount, vì vậy trong hầu hết các trường hợp bạn không cần phải lo lắng về việc dừng watcher.

Trong một số trường hợp hiếm gặp khi bạn cần dừng một watcher trước khi component chủ bị unmount, API `$watch()` trả về một hàm cho việc đó:

```js
const unwatch = this.$watch('foo', callback)

// ...khi watcher không còn cần thiết nữa:
unwatch()
```

</div>

<div class="composition-api">

Watcher được khai báo đồng bộ bên trong `setup()` hoặc `<script setup>` được gắn với instance component chủ, và sẽ tự động dừng khi component chủ bị unmount. Trong hầu hết các trường hợp, bạn không cần phải lo lắng về việc dừng watcher.

Chìa khóa ở đây là watcher phải được tạo **đồng bộ**: nếu watcher được tạo trong một callback bất đồng bộ, nó sẽ không được gắn với component chủ và phải được dừng thủ công để tránh rò rỉ bộ nhớ. Dưới đây là một ví dụ:

```vue
<script setup>
import { watchEffect } from 'vue'

// cái này sẽ tự động dừng
watchEffect(() => {})

// cái này thì không!
setTimeout(() => {
  watchEffect(() => {})
}, 100)
</script>
```

Để dừng một watcher được tạo bất đồng bộ một cách thủ công, bạn có thể sử dụng hàm `watch()` trả về. Điều này cũng hoạt động cho các watcher được tạo bằng `watchEffect()`:

```js
const unwatch = watchEffect(() => {})

// ...khi watcher không còn cần thiết nữa:
unwatch()
```

Lưu ý rằng chỉ có rất ít trường hợp mà bạn cần phải tạo các watcher bất đồng bộ, và việc tạo đồng bộ nên được ưu tiên hơn mỗi khi có thể. Nếu bạn cần phải tạo các watcher bất đồng bộ, bạn nên đảm bảo rằng bạn dừng chúng một cách thủ công.

```js
// data được tải bất đồng bộ
const data = ref(null)

watchEffect(() => {
  if (data.value) {
    // làm gì đó khi data đã được tải
  }
})
```

</div>
