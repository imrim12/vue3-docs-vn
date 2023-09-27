# Composition API: Lifecycle Hooks {#composition-api-lifecycle-hooks}

:::info Ghi chú sử dụng
Tất cả các API được liệt kê tại trang này phải được gọi đồng bộ trong hàm `setup()` của một component. Xem [Guide - Lifecycle Hooks](/guide/essentials/lifecycle) để biết thêm chi tiết.
:::

## onMounted() {#onmounted}

Đăng ký một callback và được gọi sau khi component mounted.

- **Loại**

  ```ts
  function onMounted(callback: () => void): void
  ```

- **Chi tiết**

  Một component được coi là mounted sau khi:

  - Tất cả các component con đồng bộ của nó đã được mounted (không bao gồm các component bất đồng bộ hoặc component trong cây `<Suspense>`).

  - Cây DOM của nó đã được tạo và được chèn vào container cha. Lưu ý rằng nó chỉ đảm bảo rằng cây DOM của component nằm trong document nếu root container của application cũng nằm trong document.

  Hook này thường được sử dụng để thực hiện các side effects, những cái mà cần truy cập vào DOM của component đã được render, hoặc để giới hạn code liên quan đến DOM chỉ chạy trên client trong một [server-rendered application](/guide/scaling-up/ssr).

  **Hook này không được gọi trong server-side rendering.**

- **Ví dụ**

  Truy cập vào một element thông qua template ref:

  ```vue
  <script setup>
  import { ref, onMounted } from 'vue'

  const el = ref()

  onMounted(() => {
    el.value // <div>
  })
  </script>

  <template>
    <div ref="el"></div>
  </template>
  ```

## onUpdated() {#onupdated}

Đăng ký một callback và được gọi sau khi component đã cập nhật lại cây DOM của nó sau khi reactive state thay đổi.

- **Loại**

  ```ts
  function onUpdated(callback: () => void): void
  ```

- **Chi tiết**

  Hook updated của component cha được gọi sau khi hook của các component con đã chạy xong.

  Hook này được gọi sau bất kỳ cập nhật DOM nào của component. Việc này có thể xảy ra khi các state thay đổi, vì nhiều thay đổi state có thể được gom vào chung một render cycle đơn lẻ vì lý do hiệu năng. Nếu bạn cần truy cập vào DOM đã được cập nhật sau một sự thay đổi state cụ thể, hãy sử dụng [nextTick()](/api/general#nexttick).

  **Hook này không được gọi trong server-side rendering.**

  :::warning
  Không thay đổi state của component trong hook updated - việc này có thể dẫn đến một vòng lặp cập nhật vô hạn!
  :::

- **Ví dụ**

  Truy cập vào DOM đã được cập nhật:

  ```vue
  <script setup>
  import { ref, onUpdated } from 'vue'

  const count = ref(0)

  onUpdated(() => {
    // text content nên có giá trị giống với `count.value` hiện tại
    console.log(document.getElementById('count').textContent)
  })
  </script>

  <template>
    <button id="count" @click="count++">{{ count }}</button>
  </template>
  ```

## onUnmounted() {#onunmounted}

Đăng ký một callback và được gọi sau khi component đã được unmounted.

- **Loại**

  ```ts
  function onUnmounted(callback: () => void): void
  ```

- **Chi tiết**

  Một component được coi là unmounted sau khi:

  - Tất cả các component con của nó đã được unmounted.

  - Tất cả các reactive effects liên quan (render effect và computed / watchers được tạo trong `setup()`) đã được dừng.

  Sử dụng hook này để dọn dẹp các side effects được tạo thủ công như timers, DOM event listeners hoặc server connections.

  **Hook này không được gọi trong server-side rendering.**

- **Ví dụ**

  ```vue
  <script setup>
  import { onMounted, onUnmounted } from 'vue'

  let intervalId
  onMounted(() => {
    intervalId = setInterval(() => {
      // ...
    })
  })

  onUnmounted(() => clearInterval(intervalId))
  </script>
  ```

## onBeforeMount() {#onbeforemount}

Đăng ký một hook và được gọi ngay trước khi component được mounted.

- **Loại**

  ```ts
  function onBeforeMount(callback: () => void): void
  ```

- **Chi tiết**

  Khi hook này được gọi, component đã hoàn thành việc thiết lập reactive state của nó, nhưng chưa có DOM nodes nào được tạo ra. Nó sắp thực thi render effect của DOM lần đầu tiên.

  **Hook này không được gọi trong server-side rendering.**

## onBeforeUpdate() {#onbeforeupdate}

Đăng ký một hook và được gọi ngay trước khi component sắp cập nhật cây DOM của nó do một sự thay đổi state reactive.

- **Loại**

  ```ts
  function onBeforeUpdate(callback: () => void): void
  ```

- **Chi tiết**

  Hook này có thể được sử dụng để truy cập vào DOM state trước khi Vue cập nhật DOM. Việc thay đổi state của component trong hook này cũng rất an toàn.

  **Hook này không được gọi trong server-side rendering.**

## onBeforeUnmount() {#onbeforeunmount}

Đăng ký một hook và được gọi ngay trước khi một thực thể component bị unmounted.

- **Loại**

  ```ts
  function onBeforeUnmount(callback: () => void): void
  ```

- **Chi tiết**

  Khi hook này được gọi, thực thể component vẫn hoàn toàn hoạt động.

  **Hook này không được gọi trong server-side rendering.**

## onErrorCaptured() {#onerrorcaptured}

Đăng ký một hook và được gọi ngay khi bắt được một lỗi lan truyền từ component con.

- **Loại**

  ```ts
  function onErrorCaptured(callback: ErrorCapturedHook): void

  type ErrorCapturedHook = (
    err: unknown,
    instance: ComponentPublicInstance | null,
    info: string
  ) => boolean | void
  ```

- **Chi tiết**

  Các nguồn gây ra lỗi có thể là:

  - Component renders
  - Event handlers
  - Lifecycle hooks
  - `setup()` function
  - Watchers
  - Custom directive hooks
  - Transition hooks

  Hook nhận ba tham số: lỗi, thực thể component đã gây ra lỗi, và một chuỗi thông tin chỉ định loại nguồn lỗi.

  Bạn có thể thay đổi state của component trong `errorCaptured()` để hiển thị một trạng thái lỗi cho người dùng. Tuy nhiên, điều quan trọng là trạng thái lỗi không nên render nội dung gốc đã gây ra lỗi; nếu không component sẽ bị ném vào một vòng lặp render vô hạn.

  Hook có thể trả về `false` để ngăn lỗi lan truyền xa hơn. Xem chi tiết lan truyền lỗi bên dưới.

  **Quy tắc lan truyền lỗi**

  - Mặc định, tất cả các lỗi vẫn được gửi đến [`app.config.errorHandler`](/api/application#app-config-errorhandler) của application nếu nó được định nghĩa, để những lỗi này vẫn có thể được báo cáo đến một dịch vụ analytics ở một nơi duy nhất.

  - Nếu nhiều `errorCaptured` hooks tồn tại trên một inheritance chain hoặc parent chain của component, tất cả chúng sẽ được gọi trên cùng một lỗi, theo thứ tự từ dưới lên. Điều này tương tự như cơ chế nổi của native DOM events.

  - Nếu hook `errorCaptured` tự ném ra một lỗi, cả lỗi này và lỗi được bắt từ gốc đều được gửi đến `app.config.errorHandler`.

  - Một hook `errorCaptured` có thể trả về `false` để ngăn lỗi lan truyền xa hơn. Điều này về cơ bản có nghĩa là "lỗi này đã được xử lý và nên bị bỏ qua." Nó sẽ ngăn bất kỳ hook `errorCaptured` hoặc `app.config.errorHandler` bổ sung nào được gọi cho lỗi này.

## onRenderTracked() <sup class="vt-badge dev-only" /> {#onrendertracked}

Đăng ký một debug hook và được gọi khi một reactive dependency đã được theo dõi bởi render effect của component.

**Hook này chỉ được gọi trong development mode và không được gọi trong server-side rendering.**

- **Loại**

  ```ts
  function onRenderTracked(callback: DebuggerHook): void

  type DebuggerHook = (e: DebuggerEvent) => void

  type DebuggerEvent = {
    effect: ReactiveEffect
    target: object
    type: TrackOpTypes /* 'get' | 'has' | 'iterate' */
    key: any
  }
  ```

- **Xem thêm** [Reactivity in Depth](/guide/extras/reactivity-in-depth)

## onRenderTriggered() <sup class="vt-badge dev-only" /> {#onrendertriggered}

Đăng ký một debug hook và được gọi khi một reactive dependency kích hoạt render effect của component để chạy lại.

**Hook này chỉ được gọi trong development mode và không được gọi trong server-side rendering.**

- **Loại**

  ```ts
  function onRenderTriggered(callback: DebuggerHook): void

  type DebuggerHook = (e: DebuggerEvent) => void

  type DebuggerEvent = {
    effect: ReactiveEffect
    target: object
    type: TriggerOpTypes /* 'set' | 'add' | 'delete' | 'clear' */
    key: any
    newValue?: any
    oldValue?: any
    oldTarget?: Map<any, any> | Set<any>
  }
  ```

- **Xem thêm** [Reactivity in Depth](/guide/extras/reactivity-in-depth)

## onActivated() {#onactivated}

Đăng ký một callback và được gọi sau khi thực thể component được chèn vào DOM như một phần của cây và được cache bởi [`<KeepAlive>`](/api/built-in-components#keepalive).

**Hook này không được gọi trong server-side rendering.**

- **Loại**

  ```ts
  function onActivated(callback: () => void): void
  ```

- **Xem thêm** [Guide - Lifecycle of Cached Instance](/guide/built-ins/keep-alive#lifecycle-of-cached-instance)

## onDeactivated() {#ondeactivated}

Đăng ký một callback và được gọi sau khi thực thể component được gỡ bỏ khỏi DOM như một phần của cây và được cache bởi [`<KeepAlive>`](/api/built-in-components#keepalive).

**Hook này không được gọi trong server-side rendering.**

- **Loại**

  ```ts
  function onDeactivated(callback: () => void): void
  ```

- **Xem thêm** [Guide - Lifecycle of Cached Instance](/guide/built-ins/keep-alive#lifecycle-of-cached-instance)

## onServerPrefetch() <sup class="vt-badge" data-text="SSR only" /> {#onserverprefetch}

Đăng ký một async function và được resolve trước khi thực thể component được render trên server.

- **Loại**

  ```ts
  function onServerPrefetch(callback: () => Promise<any>): void
  ```

- **Chi tiết**

  Nếu callback trả về một Promise, server renderer sẽ đợi cho đến khi Promise được resolve trước khi render component.

  Hook này chỉ được gọi trong server-side rendering và có thể được sử dụng để thực hiện data fetching server-only.

- **Ví dụ**

  ```vue
  <script setup>
  import { ref, onServerPrefetch, onMounted } from 'vue'

  const data = ref(null)

  onServerPrefetch(async () => {
    // component is rendered as part of the initial request
    // pre-fetch data on server as it is faster than on the client
    data.value = await fetchOnServer(/* ... */)
  })

  onMounted(async () => {
    if (!data.value) {
      // if data is null on mount, it means the component
      // is dynamically rendered on the client. Perform a
      // client-side fetch instead.
      data.value = await fetchOnClient(/* ... */)
    }
  })
  </script>
  ```

- **Xem thêm** [Server-Side Rendering](/guide/scaling-up/ssr)
