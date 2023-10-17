---
outline: deep
---

# Suspense {#suspense}

:::warning Tính năng thử nghiệm
`<Suspense>` là một tính năng thử nghiệm. Nó không được đảm bảo sẽ đạt được trạng thái ổn định và API có thể thay đổi trước khi đạt được trạng thái ổn định.
:::

`<Suspense>` là một component tích hợp sẵn, phục vụ mục đích điều phối các phụ thuộc bất đồng bộ trong một cây component. Nó có thể render một trạng thái loading trong khi đợi nhiều phụ thuộc bất đồng bộ lồng nhau trong cây component được giải quyết.

## Phụ thuộc bất đồng bộ {#async-dependencies}

Để giải thích vấn đề `<Suspense>` đang cố gắng giải quyết và cách nó tương tác với các phụ thuộc bất đồng bộ này, hãy tưởng tượng một cây component như sau:

```
<Suspense>
└─ <Dashboard>
   ├─ <Profile>
   │  └─ <FriendStatus> (component sử dụng async setup())
   └─ <Content>
      ├─ <ActivityFeed> (async component)
      └─ <Stats> (async component)
```

Trong cây component này có nhiều component lồng nhau mà việc render của chúng phụ thuộc vào một số tài nguyên bất đồng bộ được giải quyết trước. Nếu không có `<Suspense>`, mỗi component sẽ cần xử lý trạng thái loading / error và loaded của riêng nó. Trong trường hợp xấu nhất, chúng ta có thể thấy ba spinner loading trên trang, với nội dung được hiển thị vào các thời điểm khác nhau.

`<Suspense>` cho phép chúng ta hiển thị trạng thái loading / error của cấp độ cao nhất trong khi chúng ta đợi các phụ thuộc bất đồng bộ lồng nhau này được giải quyết.

Có hai loại phụ thuộc bất đồng bộ mà `<Suspense>` có thể đợi:

1. Component với async `setup()` hook. Điều này bao gồm các component sử dụng `<script setup>` với các biểu thức `await` ở cấp độ cao nhất.

2. [Async Components](/guide/components/async).

### `async setup()` {#async-setup}

`setup()` hook của một component Composition API có thể là async:

```js
export default {
  async setup() {
    const res = await fetch(...)
    const posts = await res.json()
    return {
      posts
    }
  }
}
```

Nếu sử dụng `<script setup>`, sự hiện diện của các biểu thức `await` ở cấp độ cao nhất tự động làm cho component trở thành một phụ thuộc bất đồng bộ:

```vue
<script setup>
const res = await fetch(...)
const posts = await res.json()
</script>

<template>
  {{ posts }}
</template>
```

### Async Components {#async-components}

Async component có thể **"suspensible"** theo mặc định. Điều này có nghĩa là nếu nó có một `<Suspense>` trong chuỗi cha, nó sẽ được coi là một phụ thuộc bất đồng bộ của `<Suspense>` đó. Trong trường hợp này, trạng thái loading sẽ được điều khiển bởi `<Suspense>`, và các tùy chọn loading, error, delay và timeout của component sẽ bị bỏ qua.

Async component có thể tắt việc điều khiển trạng thái loading của `<Suspense>` và cho phép component luôn điều khiển trạng thái loading của riêng nó bằng cách chỉ định `suspensible: false` trong tùy chọn của nó.

## Loading State {#loading-state}

`<Suspense>` component có hai slot: `#default` và `#fallback`. Cả hai slot chỉ cho phép **một** node con ngay lập tức. Node trong slot mặc định được hiển thị nếu có thể. Nếu không, node trong slot fallback sẽ được hiển thị thay thế.

```vue-html
<Suspense>
  <!-- component với các phụ thuộc bất đồng bộ lồng nhau -->
  <Dashboard />

  <!-- loading state thông qua #fallback slot -->
  <template #fallback>
    Loading...
  </template>
</Suspense>
```

Trong lần render đầu tiên, `<Suspense>` sẽ render nội dung mặc định của slot trong bộ nhớ. Nếu có bất kỳ phụ thuộc bất đồng bộ nào được gặp phải trong quá trình này, nó sẽ vào trạng thái **pending**. Trong trạng thái pending, nội dung fallback sẽ được hiển thị. Khi tất cả các phụ thuộc bất đồng bộ đã được giải quyết, `<Suspense>` vào trạng thái **resolved** và nội dung mặc định được giải quyết được hiển thị.

Nếu không có phụ thuộc bất đồng bộ nào được gặp phải trong lần render đầu tiên, `<Suspense>` sẽ trực tiếp vào trạng thái **resolved**.

Một khi ở trạng thái resolved, `<Suspense>` chỉ sẽ quay trở lại trạng thái pending nếu node gốc của slot `#default` được thay thế. Các phụ thuộc bất đồng bộ mới lồng nhau sâu hơn trong cây sẽ **không** khiến `<Suspense>` quay trở lại trạng thái pending.

Khi việc quay lại được thực hiện, nội dung sẽ không được hiển thị ngay lập tức. Thay vào đó, `<Suspense>` sẽ hiển thị nội dung `#default` trước đó trong khi đợi nội dung mới và các phụ thuộc bất đồng bộ của nó được giải quyết. Hành vi này có thể được cấu hình với tùy chọn `timeout`: `<Suspense>` sẽ chuyển sang nội dung fallback nếu nó mất nhiều hơn `timeout` để render nội dung mặc định mới. Giá trị `timeout` là `0` sẽ khiến nội dung fallback được hiển thị ngay lập tức khi nội dung mặc định được thay thế.

## Events {#events}

`<Suspense>` component phát ra 3 sự kiện: `pending`, `resolve` và `fallback`. Sự kiện `pending` xảy ra khi vào trạng thái pending. Sự kiện `resolve` được phát ra khi nội dung mới đã hoàn thành giải quyết trong slot `default`. Sự kiện `fallback` được phát ra khi nội dung trong slot `fallback` được hiển thị.

Những sự kiện này có thể được sử dụng theo nhiều cách khác nhau, ví dụ, hiển thị một màn hình loading trên DOM cũ trong khi các component mới đang được tải.

## Xử lý lỗi {#error-handling}

`<Suspense>` hiện tại không cung cấp tính năng xử lý lỗi thông qua component - tuy nhiên, bạn có thể sử dụng tùy chọn [`errorCaptured`](/api/options-lifecycle#errorcaptured) hoặc hook [`onErrorCaptured()`](/api/composition-api-lifecycle#onerrorcaptured) để bắt và xử lý lỗi async trong component cha của `<Suspense>`.

## Kết hợp với những component khác {#combining-with-other-components}

Thường thì bạn muốn sử dụng `<Suspense>` kết hợp với các component [`<Transition>`](./transition) và [`<KeepAlive>`](./keep-alive). Thứ tự lồng nhau của các component rất quan trọng để có thể hoạt động đúng cách.

Thêm vào đó, những component này thường được sử dụng kết hợp với component `<RouterView>` từ [Vue Router](https://router.vuejs.org/).

Ví dụ bên dưới cho thấy cách lồng những component này với nhau để chúng hoạt động như mong đợi. Đối với những kết hợp đơn giản hơn, bạn có thể bỏ bớt những component không cần thiết:

```vue-html
<RouterView v-slot="{ Component }">
  <template v-if="Component">
    <Transition mode="out-in">
      <KeepAlive>
        <Suspense>
          <!-- main content -->
          <component :is="Component"></component>

          <!-- loading state -->
          <template #fallback>
            Loading...
          </template>
        </Suspense>
      </KeepAlive>
    </Transition>
  </template>
</RouterView>
```

Vue Router có sẵn hỗ trợ cho [lazily loading components](https://router.vuejs.org/guide/advanced/lazy-loading.html) bằng cách sử dụng dynamic imports. Đây là những component khác biệt so với async component và hiện tại chúng sẽ không kích hoạt `<Suspense>`. Tuy nhiên, chúng vẫn có thể có async component con và những component này có thể kích hoạt `<Suspense>` theo cách thông thường.