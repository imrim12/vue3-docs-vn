# Provide / Inject {#provide-inject}

> Trang này giả định bạn đã đọc qua [Cơ bản về Component](/guide/essentials/component-basics). Hãy đọc trước nếu bạn mới bắt đầu với component.

## Truyền dữ liệu qua các Prop {#prop-drilling}

Thông thường, khi chúng ta cần truyền dữ liệu từ component cha xuống component con, chúng ta sử dụng [props](/guide/components/props). Tuy nhiên, hãy tưởng tượng trường hợp mà chúng ta có một cây component lớn, và một component con sâu cần một cái gì đó từ một component cha xa. Với chỉ props, chúng ta sẽ phải truyền cùng một prop qua toàn bộ chuỗi cha:

![sơ đồ truyền dữ liệu qua các prop](./images/prop-drilling.png)

<!-- https://www.figma.com/file/yNDTtReM2xVgjcGVRzChss/prop-drilling -->

Chú ý rằng mặc dù component `<Footer>` có thể không quan tâm đến các props này, nó vẫn cần phải khai báo và truyền chúng đi chỉ để `<DeepChild>` có thể truy cập. Nếu có một chuỗi cha dài hơn, nhiều component sẽ bị ảnh hưởng trên đường đi. Việc này được gọi là "props drilling" và chắc chắn không thú vị để xử lý.

Chúng ta có thể giải quyết props drilling bằng cách sử dụng `provide` và `inject`. Một component cha có thể phục vụ như một **dependency provider** cho tất cả các component con của nó. Bất kỳ component nào trong cây con, bất kể nó có sâu đến đâu, đều có thể **inject** các dependency được cung cấp bởi các component cha của nó.

![Sơ đồ Provide/inject](./images/provide-inject.png)

<!-- https://www.figma.com/file/PbTJ9oXis5KUawEOWdy2cE/provide-inject -->

## Provide {#provide}

<div class="composition-api">

Để cung cấp dữ liệu cho các component con, hãy sử dụng hàm [`provide()`](/api/composition-api-dependency-injection#provide):

```vue
<script setup>
import { provide } from 'vue'

provide(/* key */ 'message', /* value */ 'hello!')
</script>
```

Nếu không sử dụng `<script setup>`, hãy chắc chắn rằng `provide()` được gọi đồng bộ bên trong `setup()`:

```js
import { provide } from 'vue'

export default {
  setup() {
    provide(/* key */ 'message', /* value */ 'hello!')
  }
}
```

Hàm `provide()` nhận vào hai đối số. Đối số đầu tiên được gọi là **injection key**, có thể là một chuỗi hoặc một `Symbol`. Injection key được sử dụng bởi các component con để tìm kiếm giá trị mong muốn để inject. Một component đơn có thể gọi `provide()` nhiều lần với các injection key khác nhau để cung cấp các giá trị khác nhau.

Đối số thứ hai là giá trị được cung cấp. Giá trị có thể là bất kỳ kiểu nào, bao gồm cả reactive state như refs:

```js
import { ref, provide } from 'vue'

const count = ref(0)
provide('key', count)
```

Cung cấp các giá trị reactive cho các component con sẽ cho phép các component con sử dụng giá trị được cung cấp để thiết lập một kết nối reactive với component cung cấp.

</div>

<div class="options-api">

Để cung cấp dữ liệu cho các component con, hãy sử dụng tùy chọn [`provide`](/api/options-composition#provide):

```js
export default {
  provide: {
    message: 'hello!'
  }
}
```

Đối với mỗi thuộc tính trong đối tượng `provide`, key sẽ được sử dụng bởi các component con để tìm kiếm giá trị chính xác để inject, trong khi value là thứ sẽ được inject.

Nếu chúng ta cần cung cấp dữ liệu theo từng state của instance, ví dụ như dữ liệu được khai báo thông qua `data()`, thì `provide` phải sử dụng một giá trị hàm:

```js{7-12}
export default {
  data() {
    return {
      message: 'hello!'
    }
  },
  provide() {
    // use function syntax so that we can access `this`
    return {
      message: this.message
    }
  }
}
```

Tuy nhiên, hãy lưu ý rằng việc này **không** khiến injection trở nên reactive. Chúng ta sẽ thảo luận về [làm cho injection reactive](#working-with-reactivity) bên dưới.

</div>

## Cung cấp ở mức App {#app-level-provide}

Bên cạnh việc cung cấp dữ liệu trong một component, chúng ta cũng có thể cung cấp ở mức app:

```js
import { createApp } from 'vue'

const app = createApp({})

app.provide(/* key */ 'message', /* value */ 'hello!')
```

Cung cấp ở mức app sẽ làm cho dữ liệu có sẵn cho tất cả các component được render trong app. Điều này đặc biệt hữu ích khi viết [plugins](/guide/reusability/plugins), vì các plugin thường không thể cung cấp giá trị bằng cách sử dụng các component.

## Inject {#inject}

<div class="composition-api">

Để inject dữ liệu được cung cấp bởi một component cha, hãy sử dụng hàm [`inject()`](/api/composition-api-dependency-injection#inject):

```vue
<script setup>
import { inject } from 'vue'

const message = inject('message')
</script>
```

Nếu giá trị được cung cấp là một ref, nó sẽ được inject đầu đủ và sẽ **không** bị tự động unwrap. Điều này cho phép component inject giữ kết nối reactive với component cung cấp.

[Ví dụ đầy đủ về provide + inject cùng với Reactivity](https://play.vuejs.org/#eNqFUUFugzAQ/MrKF1IpxfeIVKp66Kk/8MWFDXYFtmUbpArx967BhURRU9/WOzO7MzuxV+fKcUB2YlWovXYRAsbBvQije2d9hAk8Xo7gvB11gzDDxdseCuIUG+ZN6a7JjZIvVRIlgDCcw+d3pmvTglz1okJ499I0C3qB1dJQT9YRooVaSdNiACWdQ5OICj2WwtTWhAg9hiBbhHNSOxQKu84WT8LkNQ9FBhTHXyg1K75aJHNUROxdJyNSBVBp44YI43NvG+zOgmWWYGt7dcipqPhGZEe2ef07wN3lltD+lWN6tNkV/37+rdKjK2rzhRTt7f3u41xhe37/xJZGAL2PLECXa9NKdD/a6QTTtGnP88LgiXJtYv4BaLHhvg==)

Như đã đề cập, nếu không sử dụng `<script setup>`, `inject()` chỉ nên được gọi đồng bộ bên trong `setup()`:

```js
import { inject } from 'vue'

export default {
  setup() {
    const message = inject('message')
    return { message }
  }
}
```

</div>

<div class="options-api">

Để inject dữ liệu được cung cấp bởi một component cha, hãy sử dụng tùy chọn [`inject`](/api/options-composition#inject):

```js
export default {
  inject: ['message'],
  created() {
    console.log(this.message) // giá trị đã được inject
  }
}
```

Các inject được giải quyết **trước** state của component, vì vậy bạn có thể truy cập các thuộc tính được inject trong `data()`:

```js
export default {
  inject: ['message'],
  data() {
    return {
      // dữ liệu khởi tạo dựa trên giá trị được inject
      fullMessage: this.message
    }
  }
}
```

[Ví dụ đầy đủ về provide + inject](https://play.vuejs.org/#eNqNkcFqwzAQRH9l0EUthOhuRKH00FO/oO7B2JtERZaEvA4F43+vZCdOTAIJCImRdpi32kG8h7A99iQKobs6msBvpTNt8JHxcTC2wS76FnKrJpVLZelKR39TSUO7qreMoXRA7ZPPkeOuwHByj5v8EqI/moZeXudCIBL30Z0V0FLXVXsqIA9krU8R+XbMR9rS0mqhS4KpDbZiSgrQc5JKQqvlRWzEQnyvuc9YuWbd4eXq+TZn0IvzOeKr8FvsNcaK/R6Ocb9Uc4FvefpE+fMwP0wH8DU7wB77nIo6x6a2hvNEME5D0CpbrjnHf+8excI=)

### Đăt biệt danh cho Injection \* {#injection-aliasing}

Khi sử dụng cú pháp mảng cho `inject`, các thuộc tính được inject sẽ được expose trên instance của component bằng cùng một key. Trong ví dụ trên, thuộc tính được cung cấp dưới key `"message"`, và được inject như `this.message`. Key cục bộ sẽ giống với injection key.

Nếu chúng ta muốn inject thuộc tính bằng một key khác, chúng ta cần sử dụng cú pháp object cho `inject`:

```js
export default {
  inject: {
    /* key cục bộ */ localMessage: {
      from: /* injection key */ 'message'
    }
  }
}
```

Tại đây, component sẽ tìm kiếm một thuộc tính được cung cấp dưới key `"message"`, sau đó expose nó như `this.localMessage`.

</div>

### Giá trị mặc định cho Injection {#injection-default-values}

Mặc định, `inject` giả định rằng key được inject sẽ được cung cấp ở mức cha. Trong trường hợp key không được cung cấp, sẽ có một cảnh báo trong runtime.

Nếu chúng ta muốn một thuộc tính được inject hoạt động với các provider tùy chọn, chúng ta cần khai báo một giá trị mặc định, tương tự như props:

<div class="composition-api">

```js
// `value` sẽ có giá trị "default value"
// nếu không có dữ liệu nào khớp với "message" được cung cấp
const value = inject('message', 'default value')
```

Trong một số trường hợp, giá trị mặc định có thể cần được tạo bằng cách gọi một hàm hoặc khởi tạo một class mới. Để tránh những tính toán không cần thiết hoặc các hiệu ứng phụ trong trường hợp giá trị tùy chọn không được sử dụng, chúng ta có thể sử dụng một hàm factory để tạo giá trị mặc định:

```js
const value = inject('key', () => new ExpensiveClass(), true)
```

Tham số thứ ba cho `inject` chỉ ra rằng giá trị mặc định có nên được coi là một hàm factory hay không.

</div>

<div class="options-api">

```js
export default {
  // cú pháp object là bắt buộc
  // khi khai báo giá trị mặc định cho injection
  inject: {
    message: {
      from: 'message', // việc này là tùy chọn nếu sử dụng cùng một key cho injection
      default: 'default value'
    },
    user: {
      // sử dụng một hàm factory cho các giá trị không phải là primitive và tốn kém
      // để khởi tạo, hoặc những giá trị độc nhất cho mỗi instance component
      default: () => ({ name: 'John' })
    }
  }
}
```

</div>

## Làm việc với Reactivity {#working-with-reactivity}

<div class="composition-api">

Khi sử dụng reactive provide / inject values, bạn **nên giữ bất kỳ mutations nào đối với reactive state bên trong _provider_ nếu có thể**. Điều này đảm bảo rằng state được cung cấp và các mutations có thể có được đặt cùng với nhau trong cùng một component, làm cho nó dễ bảo trì hơn trong tương lai.

Đôi lúc chúng ta có thể cần cập nhật dữ liệu từ một component injector. Trong những trường hợp như vậy, chúng tôi khuyến khích cung cấp một hàm có trách nhiệm thay đổi state:

```vue{7-9,13}
<!-- bên trong component provider -->
<script setup>
import { provide, ref } from 'vue'

const location = ref('North Pole')

function updateLocation() {
  location.value = 'South Pole'
}

provide('location', {
  location,
  updateLocation
})
</script>
```

```vue{5}
<!-- trong component được inject -->
<script setup>
import { inject } from 'vue'

const { location, updateLocation } = inject('location')
</script>

<template>
  <button @click="updateLocation">{{ location }}</button>
</template>
```

Cuối cùng, bạn có thể bọc giá trị được cung cấp bằng [`readonly()`](/api/reactivity-core#readonly) nếu bạn muốn đảm bảo rằng dữ liệu được cung cấp thông qua `provide` không thể bị thay đổi bởi component injector.

```vue
<script setup>
import { ref, provide, readonly } from 'vue'

const count = ref(0)
provide('read-only-count', readonly(count))
</script>
```

</div>

<div class="options-api">

Để có thể khiến injection trở nên reactive và liên kết với component khai báo provide, chúng ta cần cung cấp một computed property sử dụng hàm [`computed()`](/api/reactivity-core#computed):

```js{10}
import { computed } from 'vue'

export default {
  data() {
    return {
      message: 'hello!'
    }
  },
  provide() {
    return {
      // cung cấp một computed property rõ ràng
      message: computed(() => this.message)
    }
  }
}
```

[Ví dụ đầy đủ về provide + inject cùng với Reactivity](https://play.vuejs.org/#eNqNUctqwzAQ/JVFFyeQxnfjBEoPPfULqh6EtYlV9EKWTcH43ytZtmPTQA0CsdqZ2dlRT16tPXctkoKUTeWE9VeqhbLGeXirheRwc0ZBds7HKkKzBdBDZZRtPXIYJlzqU40/I4LjjbUyIKmGEWw0at8UgZrUh1PscObZ4ZhQAA596/RcAShsGnbHArIapTRBP74O8Up060wnOO5QmP0eAvZyBV+L5jw1j2tZqsMp8yWRUHhUVjKPoQIohQ460L0ow1FeKJlEKEnttFweijJfiORElhCf5f3umObb0B9PU/I7kk17PJj7FloN/2t7a2Pj/Zkdob+x8gV8ZlMs2de/8+14AXwkBngD9zgVqjg2rNXPvwjD+EdlHilrn8MvtvD1+Q==)

Hàm `computed()` thường được sử dụng trong các component có dùng Composition API, nhưng cũng có thể được sử dụng để bổ sung cho một số trường hợp sử dụng Options API. Bạn có thể tìm hiểu thêm về cách sử dụng nó bằng cách đọc [Nền tảng về Reactivity](/guide/essentials/reactivity-fundamentals) và [Thuộc tính Computed](/guide/essentials/computed) với API Preference được đặt thành Composition API.

:::warning Cấu hình yêu cầu tạm thời

Những cách thức sử dụng ở trên yêu cầu thiết lập `app.config.unwrapInjectedRef = true` để các injection tự động unwrap computed refs. Điều này sẽ trở thành hành vi mặc định trong Vue 3.3 và config này được giới thiệu tạm thời để tránh gây ra lỗi. Nó sẽ không còn được yêu cầu sau 3.3.
:::

</div>

## Làm việc với Symbol Keys {#working-with-symbol-keys}

Cho đến hiện tại, chúng ta đã sử dụng các string injection keys trong các ví dụ. Nếu bạn đang làm việc trong một ứng dụng lớn với nhiều dependency provider, hoặc bạn đang viết các component sẽ được sử dụng bởi các nhà phát triển khác, thì nên sử dụng Symbol injection keys để tránh xung đột tiềm năng.

Việc export Symbol trong một file riêng biệt được khuyến khích:

```js
// keys.js
export const myInjectionKey = Symbol()
```

<div class="composition-api">

```js
// trong component thực hiện provide
import { provide } from 'vue'
import { myInjectionKey } from './keys.js'

provide(myInjectionKey, {
  /* dữ liệu được cung cấp */
})
```

```js
// trong component được inject
import { inject } from 'vue'
import { myInjectionKey } from './keys.js'

const injected = inject(myInjectionKey)
```

Xem thêm: [Ép kiểu cho Provide / Inject](/guide/typescript/composition-api#typing-provide-inject) <sup class="vt-badge ts" />

</div>

<div class="options-api">

```js
// trong component thực hiện provide
import { myInjectionKey } from './keys.js'

export default {
  provide() {
    return {
      [myInjectionKey]: {
        /* dữ liệu được cung cấp */
      }
    }
  }
}
```

```js
// trong component được inject
import { myInjectionKey } from './keys.js'

export default {
  inject: {
    injected: { from: myInjectionKey }
  }
}
```

</div>
