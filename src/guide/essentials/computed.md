# Thuộc tính Computed {#computed-properties}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/computed-properties-in-vue-3" title="Free Vue.js Computed Properties Lesson"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-computed-properties-in-vue-with-the-composition-api" title="Free Vue.js Computed Properties Lesson"/>
</div>

## Ví dụ cơ bản {#basic-example}

Biểu thức trong template rất tiện lợi, nhưng chúng được thiết kế cho các hoạt động đơn giản. Đặt quá nhiều logic trong template có thể làm cho chúng trở nên rối rắm và khó bảo trì. Ví dụ, nếu chúng ta có một object với một mảng lồng nhau:

<div class="options-api">

```js
export default {
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Hướng dẫn nâng cao',
          'Vue 3 - Hướng dẫn cơ bản',
          'Vue 4 - Bí mật, bật mí'
        ]
      }
    }
  }
}
```

</div>
<div class="composition-api">

```js
const author = reactive({
  name: 'John Doe',
  books: [
    'Vue 2 - Hướng dẫn nâng cao',
    'Vue 3 - Hướng dẫn cơ bản',
    'Vue 4 - Bí mật, bật mí'
  ]
})
```

</div>

Và chúng ta muốn hiển thị các thông báo khác nhau tùy thuộc vào `author` đã có sách hay chưa:

```vue-html
<p>Đã phát hành sách hay chưa:</p>
<span>{{ author.books.length > 0 ? 'Rồi' : 'Chưa' }}</span>
```

Cho đến lúc này, template đã trở nên hơi rối rắm một chút. Chúng ta phải nhìn vào nó một lúc trước khi nhận ra rằng nó thực hiện một phép tính tùy thuộc vào `author.books`. Quan trọng hơn, chúng ta có thể không muốn lặp lại chính mình nếu chúng ta cần bao gồm phép tính này trong template nhiều hơn một lần.

Đó là lí do tại sao với logic phức tạp bao gồm reactive data, chúng ta nên sử dụng một **computed property**. Dưới đây là ví dụ tương tự, được tái cấu trúc:

<div class="options-api">

```js
export default {
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Hướng dẫn nâng cao',
          'Vue 3 - Hướng dẫn cơ bản',
          'Vue 4 - Bí mật, bật mí'
        ]
      }
    }
  },
  computed: {
    // a computed getter
    publishedBooksMessage() {
      // `this` points to the component instance
      return this.author.books.length > 0 ? 'Rồi' : 'Chưa'
    }
  }
}
```

```vue-html
<p>Đã phát hành sách hay chưa:</p>
<span>{{ publishedBooksMessage }}</span>
```

[Thử trong Playground](https://play.vuejs.org/#eNqFkN1KxDAQhV/l0JsqaFfUq1IquwiKsF6JINaLbDNui20S8rO4lL676c82eCFCIDOZMzkzXxetlUoOjqI0ykypa2XzQtC3ktqC0ydzjUVXCIAzy87OpxjQZJ0WpwxgzlZSp+EBEKylFPGTrATuJcUXobST8sukeA8vQPzqCNe4xJofmCiJ48HV/FfbLLrxog0zdfmn4tYrXirC9mgs6WMcBB+nsJ+C8erHH0rZKmeJL0sot2tqUxHfDONuyRi2p4BggWCr2iQTgGTcLGlI7G2FHFe4Q/xGJoYn8SznQSbTQviTrRboPrHUqoZZ8hmQqfyRmTDFTC1bqalsFBN5183o/3NG33uvoWUwXYyi/gdTEpwK)

Tại đây chúng ta đã khai báo một thuộc tính computed `publishedBooksMessage`.

Hãy thử thay đổi giá trị của mảng `books` trong `data` của ứng dụng và bạn sẽ thấy cách mà `publishedBooksMessage` thay đổi tương ứng.

Bạn có thể ràng buộc data với các thuộc tính computed trong template giống như một thuộc tính bình thường. Vue sẽ nhận thấy rằng `this.publishedBooksMessage` phụ thuộc vào `this.author.books`, vì vậy nó sẽ cập nhật bất kỳ ràng buộc nào phụ thuộc vào `this.publishedBooksMessage` khi `this.author.books` thay đổi.

Xem thêm: [Ép kiểu cho thuộc tính Computed](/guide/typescript/options-api#typing-computed-properties) <sup class="vt-badge ts" />

</div>

<div class="composition-api">

```vue
<script setup>
import { reactive, computed } from 'vue'

const author = reactive({
  name: 'John Doe',
  books: [
    'Vue 2 - Hướng dẫn nâng cao',
    'Vue 3 - Hướng dẫn cơ bản',
    'Vue 4 - Bí mật, bật mí'
  ]
})

// a computed ref
const publishedBooksMessage = computed(() => {
  return author.books.length > 0 ? 'Rồi' : 'Chưa'
})
</script>

<template>
  <p>Đã phát hành sách hay chưa:</p>
  <span>{{ publishedBooksMessage }}</span>
</template>
```

[Thử trong Playground](https://play.vuejs.org/#eNp1kE9Lw0AQxb/KI5dtoTainkoaaREUoZ5EEONhm0ybYLO77J9CCfnuzta0vdjbzr6Zeb95XbIwZroPlMySzJW2MR6OfDB5oZrWaOvRwZIsfbOnCUrdmuCpQo+N1S0ET4pCFarUynnI4GttMT9PjLpCAUq2NIN41bXCkyYxiZ9rrX/cDF/xDYiPQLjDDRbVXqqSHZ5DUw2tg3zP8lK6pvxHe2DtvSasDs6TPTAT8F2ofhzh0hTygm5pc+I1Yb1rXE3VMsKsyDm5JcY/9Y5GY8xzHI+wnIpVw4nTI/10R2rra+S4xSPEJzkBvvNNs310ztK/RDlLLjy1Zic9cQVkJn+R7gIwxJGlMXiWnZEq77orhH3Pq2NH9DjvTfpfSBSbmA==)

Tại đây chúng ta đã khai báo một thuộc tính computed `publishedBooksMessage`. Hàm `computed()` mong đợi được truyền một hàm getter, và giá trị trả về là một **computed ref**. Tương tự như refs bình thường, bạn có thể truy cập vào kết quả computed như `publishedBooksMessage.value`. Computed refs cũng được tự động unwrap trong template để bạn có thể tham chiếu đến chúng mà không cần `.value` trong biểu thức template.

Một thuộc tính computed tự động theo dõi các reactive phụ thuộc của nó. Vue nhận thấy rằng việc tính toán `publishedBooksMessage` phụ thuộc vào `author.books`, vì vậy nó sẽ cập nhật bất kỳ ràng buộc nào phụ thuộc vào `publishedBooksMessage` khi `author.books` thay đổi.

Xem thêm: [Ép kiểu cho thuộc tính Computed](/guide/typescript/options-api#typing-computed-properties) <sup class="vt-badge ts" />

</div>

## So sánh Caching của Computed với Methods {#computed-caching-vs-methods}

Bạn có thể thấy chúng ta có thể đạt được cùng một kết quả bằng cách gọi một method trong biểu thức:

```vue-html
<p>{{ calculateBooksMessage() }}</p>
```

<div class="options-api">

```js
// trong component
methods: {
  calculateBooksMessage() {
    return this.author.books.length > 0 ? 'Rồi' : 'Chưa'
  }
}
```

</div>

<div class="composition-api">

```js
// trong component
function calculateBooksMessage() {
  return author.books.length > 0 ? 'Rồi' : 'Chưa'
}
```

</div>

Thay vì một thuộc tính computed, chúng ta có thể định nghĩa cùng một hàm như một method. Đối với kết quả cuối cùng, hai cách tiếp cận này đều giống nhau. Tuy nhiên, khác biệt là **computed property được lưu trữ dựa trên các phụ thuộc reactive của nó**. Một computed property chỉ được tính toán lại khi một số phụ thuộc reactive của nó đã thay đổi. Điều này có nghĩa là miễn là `author.books` không thay đổi, nhiều lần truy cập vào `publishedBooksMessage` sẽ ngay lập tức trả về kết quả đã được tính toán trước đó mà không cần chạy hàm getter lại.

Điều này cũng có nghĩa là thuộc tính computed sau sẽ không bao giờ cập nhật, vì `Date.now()` không phải là một phụ thuộc reactive:

<div class="options-api">

```js
computed: {
  now() {
    return Date.now()
  }
}
```

</div>

<div class="composition-api">

```js
const now = computed(() => Date.now())
```

</div>

Trong khi đó, một method sẽ luôn luôn chạy hàm getter mỗi khi một re-render xảy ra.

Tại sao chúng ta cần caching? Hãy tưởng tượng chúng ta có một thuộc tính computed đắt giá `list`, yêu cầu lặp qua một mảng lớn và thực hiện nhiều tính toán. Sau đó chúng ta có thể có các thuộc tính computed khác phụ thuộc vào `list`. Nếu không có caching, chúng ta sẽ thực thi getter của `list` nhiều lần hơn cần thiết! Trong các trường hợp mà bạn không muốn caching, hãy sử dụng một method thay thế.

## Computed có thể ghi {#computed-setter}

Thuộc tính computed mặc định chỉ có thể đọc. Nếu bạn cố gắng gán một giá trị mới cho một thuộc tính computed, bạn sẽ nhận được một cảnh báo tại thời điểm chạy. Trong những trường hợp hiếm khi bạn cần một thuộc tính computed "có thể ghi", bạn có thể tạo một thuộc tính computed bằng cách cung cấp cả getter và setter:

<div class="options-api">

```js
export default {
  data() {
    return {
      firstName: 'John',
      lastName: 'Doe'
    }
  },
  computed: {
    fullName: {
      // getter
      get() {
        return this.firstName + ' ' + this.lastName
      },
      // setter
      set(newValue) {
        // Note: chúng ta đang sử dụng cú pháp destructuring assignment ở đây.
        [this.firstName, this.lastName] = newValue.split(' ')
      }
    }
  }
}
```

Bây giờ khi bạn chạy `this.fullName = 'John Doe'`, setter sẽ được gọi và `this.firstName` và `this.lastName` sẽ được cập nhật tương ứng.

</div>

<div class="composition-api">

```vue
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('John')
const lastName = ref('Doe')

const fullName = computed({
  // getter
  get() {
    return firstName.value + ' ' + lastName.value
  },
  // setter
  set(newValue) {
    // Note: chúng ta đang sử dụng cú pháp destructuring assignment ở đây.
    [firstName.value, lastName.value] = newValue.split(' ')
  }
})
</script>
```

Bây giờ khi bạn chạy `fullName.value = 'John Doe'`, setter sẽ được gọi và `firstName` và `lastName` sẽ được cập nhật tương ứng.

</div>

## Phương pháp sử dụng tốt nhất {#best-practices}

### Getter nên không có side effect {#getters-should-be-side-effect-free}

Một điều quan trọng mà bạn cần phải nhớ đó là getter của computed property chỉ nên thực hiện các tính toán và không gây ra bất kỳ side effect nào. Ví dụ, **đừng thực hiện các yêu cầu async hoặc thay đổi DOM trong getter của computed property!** Hãy nghĩ về một computed property như là một cách mô tả khai báo làm thế nào để tạo ra một giá trị dựa trên các giá trị khác - trách nhiệm duy nhất của nó là tính toán và trả về giá trị đó. Sau này trong hướng dẫn, chúng ta sẽ thảo luận về cách thực hiện các side effect khi có sự thay đổi state với [watchers](./watchers).

### Tránh việc thay đổi thuộc tính computed {#avoid-mutating-a-computed-property}

Giá trị trả về từ một thuộc tính computed là một giá trị dẫn xuất. Hãy nghĩ về nó như là một bản lưu tạm thời - mỗi khi state nguồn thay đổi, một bản lưu mới được tạo ra. Nó không có ý nghĩa khi thay đổi một bản lưu tạm thời, vì vậy một giá trị trả về từ thuộc tính computed nên được coi là chỉ đọc và không bao giờ được thay đổi - thay vào đó, hãy cập nhật state nguồn mà nó phụ thuộc để kích hoạt các tính toán mới.
