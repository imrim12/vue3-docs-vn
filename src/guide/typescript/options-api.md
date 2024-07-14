# TypeScript với Options API {#typescript-with-options-api}

> Trang này giả định rằng bạn đã đọc qua phần [Sử dụng Vue với TypeScript](./overview).

:::tip
Mặc dù Vue hỗ trợ việc sử dụng TypeScript với Options API, nhưng nó khuyến khích sử dụng Vue với TypeScript thông qua Composition API vì nó cung cấp khả năng suy luận kiểu đơn giản, hiệu quả và mạnh mẽ hơn.
:::

## Ép kiểu cho Props của Component {#typing-component-props}

Kết xuất kiểu dữ liệu cho props trong Options API yêu cầu bọc component với `defineComponent()`. Với nó, Vue có thể suy luận kiểu cho props dựa trên tùy chọn `props`, sử dụng các tùy chọn bổ sung như `required: true` và `default`:

```ts
import { defineComponent } from 'vue'

export default defineComponent({
  // cho phép kết xuất kiểu
  props: {
    name: String,
    id: [Number, String],
    msg: { type: String, required: true },
    metadata: null
  },
  mounted() {
    this.name // kiểu dữ liệu: string | undefined
    this.id // kiểu dữ liệu: number | string | undefined
    this.msg // kiểu dữ liệu: string
    this.metadata // kiểu dữ liệu: any
  }
})
```

Tuy nhiên, tùy chọn `props` trong runtime chỉ hỗ trợ sử dụng các hàm khởi tạo như kiểu của props - không có cách nào để chỉ định các kiểu phức tạp như các đối tượng với các thuộc tính lồng nhau hoặc khởi tạo hàm.

Để ép kiểu các props phức tạp, chúng ta có thể sử dụng tiện ích `PropType`:

```ts
import { defineComponent } from 'vue'
import type { PropType } from 'vue'

interface Book {
  title: string
  author: string
  year: number
}

export default defineComponent({
  props: {
    book: {
      // cung cấp kiểu cụ thể hơn cho `Object`
      type: Object as PropType<Book>,
      required: true
    },
    // hoặc sử dụng một hàm khởi tạo
    callback: Function as PropType<(id: number) => void>
  },
  mounted() {
    this.book.title // string
    this.book.year // number

    // TS Error: đối số của kiểu 'string' không thể 
    // gán cho tham số của kiểu 'number'
    this.callback?.('123')
  }
})
```

### Lưu ý {#caveats}

Nếu phiên bản TypeScript của bạn nhỏ hơn `4.7`, bạn phải cẩn thận khi sử dụng giá trị hàm cho các tùy chọn `validator` và `default` - hãy chắc chắn sử dụng các arrow function:

```ts
import { defineComponent } from 'vue'
import type { PropType } from 'vue'

interface Book {
  title: string
  year?: number
}

export default defineComponent({
  props: {
    bookA: {
      type: Object as PropType<Book>,
      // Chắc chắn rằng bạn sử dụng arrow function nếu phiên bản TypeScript của bạn nhỏ hơn 4.7
      default: () => ({
        title: 'Arrow Function Expression'
      }),
      validator: (book: Book) => !!book.title
    }
  }
})
```

Việc này ngăn TypeScript phải suy luận kiểu của `this` bên trong các hàm này, điều này, tiếc thay, có thể gây ra lỗi suy luận kiểu. Đây là một [hạn chế thiết kế](https://github.com/microsoft/TypeScript/issues/38845), và hiện đã được cải thiện trong [TypeScript 4.7](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-4-7.html#improved-function-inference-in-objects-and-methods).

## Ép kiểu Component Emits {#typing-component-emits}

Chúng ta có thể khai báo kiểu dữ liệu mong đợi cho một sự kiện được phát ra bằng cách sử dụng cú pháp đối tượng của tùy chọn `emits`. Ngoài ra, tất cả các sự kiện được phát ra không được khai báo sẽ ném ra một lỗi kiểu khi được gọi:

```ts
import { defineComponent } from 'vue'

export default defineComponent({
  emits: {
    addBook(payload: { bookName: string }) {
      // thực hiện validation tại runtime
      return payload.bookName.length > 0
    }
  },
  methods: {
    onSubmit() {
      this.$emit('addBook', {
        bookName: 123 // Lỗi kiểu dữ liệu!
      })

      this.$emit('non-declared-event') // Lỗi kiểu dữ liệu!
    }
  }
})
```

## Ép kiểu thuộc tính Computed {#typing-computed-properties}

Một thuộc tính computed sẽ suy luận kiểu dữ liệu của nó dựa trên giá trị trả về:

```ts
import { defineComponent } from 'vue'

export default defineComponent({
  data() {
    return {
      message: 'Hello!'
    }
  },
  computed: {
    greeting() {
      return this.message + '!'
    }
  },
  mounted() {
    this.greeting // kiểu dữ liệu: string
  }
})
```

Trong một số trường hợp, bạn có thể muốn chú thích rõ ràng kiểu của một thuộc tính computed để đảm bảo việc thực hiện của nó là chính xác:

```ts
import { defineComponent } from 'vue'

export default defineComponent({
  data() {
    return {
      message: 'Hello!'
    }
  },
  computed: {
    // khai báo rõ ràng kiểu dữ liệu trả về
    greeting(): string {
      return this.message + '!'
    },

    // khai báo rõ ràng kiểu dữ liệu trả về và kiểu dữ liệu của giá trị gán
    greetingUppercased: {
      get(): string {
        return this.greeting.toUpperCase()
      },
      set(newValue: string) {
        this.message = newValue.toUpperCase()
      }
    }
  }
})
```

Khai báo kiểu dữ liệu rõ ràng cũng có thể được yêu cầu trong một số trường hợp đặc biệt mà TypeScript không thể suy luận kiểu dữ liệu của thuộc tính computed do vòng lặp suy luận đệ quy.

## Ép kiểu Event Handlers {#typing-event-handlers}

Khi xử lý các sự kiện DOM thuần,việc ép kiểu cho đối số có thể hữu ích khi chúng ta truyền vào trình xử lý. Hãy xem xét ví dụ này:

```vue
<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  methods: {
    handleChange(event) {
      // `event` ngầm định có kiểu `any`
      console.log(event.target.value)
    }
  }
})
</script>

<template>
  <input type="text" @change="handleChange" />
</template>
```

Khi không có chú thích kiểu, đối số `event` sẽ ngầm định có kiểu `any`. Điều này cũng sẽ dẫn đến lỗi TS nếu `"strict": true` hoặc `"noImplicitAny": true` được sử dụng trong `tsconfig.json`. Do đó, nên khai báo rõ ràng đối số của bộ xử lý sự kiện. Ngoài ra, bạn có thể cần sử dụng các khai báo kiểu khi truy cập các thuộc tính của `event`:

```ts
import { defineComponent } from 'vue'

export default defineComponent({
  methods: {
    handleChange(event: Event) {
      console.log((event.target as HTMLInputElement).value)
    }
  }
})
```

## Bổ sung các thuộc tính toàn cục {#augmenting-global-properties}

Một số plugin cài đặt các thuộc tính có sẵn toàn cục cho tất cả các trường hợp component thông qua [`app.config.globalProperties`](/api/application#app-config-globalproperties). Ví dụ, chúng ta có thể cài đặt `this.$http` để lấy dữ liệu hoặc `this.$translate` để đa ngôn ngữ. Để làm cho việc này hoạt động tốt với TypeScript, Vue cung cấp một giao diện `ComponentCustomProperties` được thiết kế để được bổ sung thông qua [TypeScript module augmentation](https://www.typescriptlang.org/docs/handbook/declaration-merging.html#module-augmentation):

```ts
import axios from 'axios'

declare module 'vue' {
  interface ComponentCustomProperties {
    $http: typeof axios
    $translate: (key: string) => string
  }
}
```

Xem thêm:

- [TypeScript unit tests for component type extensions](https://github.com/vuejs/core/blob/main/packages/dts-test/componentTypeExtensions.test-d.tsx)

### Ép kiểu cho các Thay thế bổ sung {#type-augmentation-placement}

Chúng ta có thể đặt kiểu bổ sung này trong một tệp `.ts`, hoặc trong một tệp `*.d.ts` toàn cục. Dù cách nào đi nữa, hãy chắc chắn nó được bao gồm trong `tsconfig.json`. Đối với các tác giả thư viện / plugin, tệp này nên được chỉ định trong thuộc tính `types` trong `package.json`.

Để có thể tận dụng các bổ sung module, bạn cần đảm bảo rằng bổ sung được đặt trong một [TypeScript module](https://www.typescriptlang.org/docs/handbook/modules.html). Nói cách khác, tệp cần chứa ít nhất một `import` hoặc `export` cấp cao nhất, ngay cả khi nó chỉ là `export {}`. Nếu bổ sung được đặt bên ngoài module, nó sẽ ghi đè lên các kiểu gốc thay vì bổ sung chúng!

```ts
// Không hoạt động, ghi đè lên các kiểu gốc.
declare module 'vue' {
  interface ComponentCustomProperties {
    $translate: (key: string) => string
  }
}
```

```ts
// Hoạt động bình thường
export {}

declare module 'vue' {
  interface ComponentCustomProperties {
    $translate: (key: string) => string
  }
}
```

## Các tuỳ chỉnh bổ sung {#augmenting-custom-options}

Một số plugin, ví dụ như `vue-router`, cung cấp hỗ trợ cho các tùy chọn component tùy chỉnh như `beforeRouteEnter`:

```ts
import { defineComponent } from 'vue'

export default defineComponent({
  beforeRouteEnter(to, from, next) {
    // ...
  }
})
```

Nếu không có bổ sung kiểu, các đối số của hook này sẽ ngầm định có kiểu `any`. Chúng ta có thể bổ sung `ComponentCustomOptions` để hỗ trợ các tùy chọn tùy chỉnh này:

```ts
import { Route } from 'vue-router'

declare module 'vue' {
  interface ComponentCustomOptions {
    beforeRouteEnter?(to: Route, from: Route, next: () => void): void
  }
}
```

Bây giờ tuỳ chọn `beforeRouteEnter` sẽ được ép kiểu đúng. Chú ý rằng đây là một ví dụ - các thư viện được định kiểu tốt như `vue-router` sẽ tự động thực hiện các bổ sung này trong các định nghĩa kiểu của riêng chúng.

Việc thay thế những bổ sung này phải tuân theo [các hạn chế](#type-augmentation-placement) tương tự như bổ sung cho các thuộc tính toàn cục.

Xem thêm:

- [TypeScript unit tests for component type extensions](https://github.com/vuejs/core/blob/main/packages/dts-test/componentTypeExtensions.test-d.tsx)
