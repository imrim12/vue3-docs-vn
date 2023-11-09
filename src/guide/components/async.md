# Component bất đồng bộ {#async-components}

## Basic Usage {#basic-usage}

Trong các ứng dụng lớn, chúng ta có thể cần phải chia ứng dụng thành những phần nhỏ hơn và chỉ tải các component này từ server khi cần thiết. Để làm điều đó, Vue có một hàm [`defineAsyncComponent`](/api/general#defineasynccomponent):

```js
import { defineAsyncComponent } from 'vue'

const AsyncComp = defineAsyncComponent(() => {
  return new Promise((resolve, reject) => {
    // ...tải component từ server
    resolve(/* component sau khi tải xong */)
  })
})
// ... sử dụng `AsyncComp` như một component bình thường
```

Như bạn có thể thấy, `defineAsyncComponent` nhận một hàm tải và trả về một Promise. Hàm `resolve` của Promise sẽ được gọi khi bạn đã lấy được định nghĩa component từ server. Bạn cũng có thể gọi `reject(reason)` để chỉ ra rằng việc tải đã thất bại.

[ES module dynamic import](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import) cũng trả về một Promise, vì vậy phần lớn thời gian chúng ta sẽ sử dụng nó kết hợp với `defineAsyncComponent`. Các bundler như Vite và webpack cũng hỗ trợ cú pháp này (và sẽ sử dụng nó như điểm chia nhỏ bundle), vì vậy chúng ta có thể sử dụng nó để import Vue SFCs:

```js
import { defineAsyncComponent } from 'vue'

const AsyncComp = defineAsyncComponent(() =>
  import('./components/MyComponent.vue')
)
```

Kết quả của `AsyncComp` là một component bao bọc chỉ gọi hàm tải khi nó được hiển thị trên trang. Ngoài ra, nó sẽ chuyển tiếp bất kỳ props và slots nào cho component bên trong, vì vậy bạn có thể sử dụng 'bao bọc bất đồng bộ' để thay thế component gốc một cách mượt mà trong khi đạt được lazy loading.

Giống như các component bình thường khác, component bất đồng bộ có thể được [đăng ký toàn cầu](/guide/components/registration#global-registration) bằng cách sử dụng `app.component()`:

```js
app.component('MyComponent', defineAsyncComponent(() =>
  import('./components/MyComponent.vue')
))
```

<div class="options-api">

Bạn cũng có thể sử dụng `defineAsyncComponent` khi [đăng ký một component cục bộ](/guide/components/registration#local-registration):

```vue
<script>
import { defineAsyncComponent } from 'vue'

export default {
  components: {
    AdminPage: defineAsyncComponent(() =>
      import('./components/AdminPageComponent.vue')
    )
  }
}
</script>

<template>
  <AdminPage />
</template>
```

</div>

<div class="composition-api">

Chúng cũng có thể được định nghĩa trực tiếp bên trong component cha:

```vue
<script setup>
import { defineAsyncComponent } from 'vue'

const AdminPage = defineAsyncComponent(() =>
  import('./components/AdminPageComponent.vue')
)
</script>

<template>
  <AdminPage />
</template>
```

</div>

## Trạng thái Loading và Error {#loading-and-error-states}

Các quy trình bất đồng bộ không thể tránh khỏi các trạng thái loading và error - `defineAsyncComponent()` hỗ trợ xử lý các trạng thái này thông qua các tùy chọn nâng cao:

```js
const AsyncComp = defineAsyncComponent({
  // hàm tải
  loader: () => import('./Foo.vue'),

  // Component để sử dụng trong khi component bất đồng bộ đang tải
  loadingComponent: LoadingComponent,
  // Khoảng thời gian trước khi hiển thị component loading. Mặc định: 200ms.
  delay: 200,

  // Component để sử dụng nếu việc tải thất bại
  errorComponent: ErrorComponent,
  // Error component sẽ được hiển thị nếu timeout được 
  // khai báo và thời gian chờ đã bị vượt qua. Mặc định: Infinity.
  timeout: 3000
})
```

Nếu loading component được khai báo, nó sẽ được hiển thị trước khi component bên trong được tải. Mặc định là 200ms trước khi loading component được hiển thị - việc này là bởi vì trên các đường truyền mạng nhanh, trạng thái loading có thể bị thay thế quá nhanh và sẽ trông như bị giật.

Nếu error component được khai báo, nó sẽ được hiển thị khi Promise được trả về bởi hàm tải bị reject. Bạn cũng có thể chỉ định một timeout để hiển thị error component khi request mất quá nhiều thời gian.

## Sử dụng với Suspense {#using-with-suspense}

Component bất đồng bộ có thể được sử dụng với built-in component `<Suspense>`. Tương tác giữa `<Suspense>` và component bất đồng bộ được viết trong tài liệu [chương riêng cho `<Suspense>`](/guide/built-ins/suspense).
