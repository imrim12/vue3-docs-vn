# Options: Lifecycle {#options-lifecycle}

:::info Xem thêm
Để sử dụng chung các lifecycle hooks, xem [Guide - Lifecycle Hooks](/guide/essentials/lifecycle)
:::

## beforeCreate {#beforecreate}

Được gọi khi thực thể được khởi tạo.

- **Loại**

  ```ts
  interface ComponentOptions {
    beforeCreate?(this: ComponentPublicInstance): void
  }
  ```

- **Chi tiết**

  Được gọi ngay khi thực thể được khởi tạo, sau khi xử lý props, trước khi xử lý các options khác như `data()` hoặc `computed`.

  Lưu ý rằng `setup()` của Composition API được gọi trước bất kỳ hook Options API nào, thậm chí là `beforeCreate()`.

## created {#created}

Được gọi sau khi thực thể đã hoàn thành việc xử lý tất cả các options liên quan đến state.

- **Loại**

  ```ts
  interface ComponentOptions {
    created?(this: ComponentPublicInstance): void
  }
  ```

- **Chi tiết**

  Khi hook này được gọi, các thành phần sau đã được thiết lập: reactive data, computed properties, methods, và watchers. Tuy nhiên, giai đoạn mounted chưa được bắt đầu, và thuộc tính `$el` sẽ chưa có sẵn.

## beforeMount {#beforemount}

Được gọi ngay trước khi component được mounted.

- **Loại**

  ```ts
  interface ComponentOptions {
    beforeMount?(this: ComponentPublicInstance): void
  }
  ```

- **Chi tiết**

  Khi hook này được gọi, component đã hoàn thành việc thiết lập reactive state, nhưng chưa có DOM nodes nào được tạo ra. Nó sẽ thực thi DOM render effect lần đầu tiên.

  **Hook này không được gọi trong server-side rendering.**

## mounted {#mounted}

Được gọi sau khi component đã được mounted.

- **Loại**

  ```ts
  interface ComponentOptions {
    mounted?(this: ComponentPublicInstance): void
  }
  ```

- **Chi tiết**

  Một component được coi là mounted sau khi:

  - Tất cả các component con đồng bộ của nó đã được mounted (không bao gồm các component bất đồng bộ hoặc component trong cây `<Suspense>`).

  - Cây DOM của nó đã được tạo và được chèn vào container cha. Lưu ý rằng nó chỉ đảm bảo rằng cây DOM của component nằm trong document nếu root container của application cũng nằm trong document.

  Hook này thường được sử dụng để thực hiện những side effects mà cần truy cập vào DOM của component đã được render, hoặc để giới hạn code liên quan đến DOM chỉ chạy trên client trong một [server-rendered application](/guide/scaling-up/ssr).

  **Hook này không được gọi trong server-side rendering.**

## beforeUpdate {#beforeupdate}

Được gọi ngay trước khi component chuẩn bị cập nhật DOM tree của nó do một reactive state change.

- **Loại**

  ```ts
  interface ComponentOptions {
    beforeUpdate?(this: ComponentPublicInstance): void
  }
  ```

- **Chi tiết**

  Hook này có thể được sử dụng để truy cập vào DOM state trước khi Vue cập nhật DOM. Việc thay đổi component state trong hook này cũng an toàn.

  **Hook này không được gọi trong server-side rendering.**

## updated {#updated}

Được gọi sau khi component đã cập nhật lại cây DOM của nó sau khi reactive state thay đổi.

- **Loại**

  ```ts
  interface ComponentOptions {
    updated?(this: ComponentPublicInstance): void
  }
  ```

- **Chi tiết**

  Hook updated của component cha được gọi sau khi hook của các component con đã chạy xong.

  Hook này được gọi sau bất kỳ DOM update của component. Việc này có thể được gây ra bởi các sự thay đổi state khác nhau. Nếu bạn cần truy cập vào DOM đã được cập nhật sau một sự thay đổi state cụ thể, hãy sử dụng [nextTick()](/api/general#nexttick).

  Hook này không được gọi trong server-side rendering.

  :::warning
  Không thay đổi state của component trong hook updated - việc này có thể dẫn đến vòng lặp cập nhật vô hạn!
  :::

## beforeUnmount {#beforeunmount}

Được gọi ngay trước khi một thực thể component bị unmounted.

- **Loại**

  ```ts
  interface ComponentOptions {
    beforeUnmount?(this: ComponentPublicInstance): void
  }
  ```

- **Chi tiết**

  Khi hook này được gọi, thực thể component vẫn hoạt động bình thường.

  Hook này không được gọi trong server-side rendering.

## unmounted {#unmounted}

Được gọi sau khi component đã bị unmounted.

- **Loại**

  ```ts
  interface ComponentOptions {
    unmounted?(this: ComponentPublicInstance): void
  }
  ```

- **Chi tiết**

  Một component được coi là unmounted sau khi:

  - Tất cả các component con của nó đã được unmounted.

  - Tất cả các reactive effects liên quan (render effect và computed / watchers được tạo trong `setup()`) đã được dừng.

  Sử dụng hook này để dọn dẹp các side effects được tạo ra thủ công như timers, DOM event listeners hoặc server connections.

  Hook này không được gọi trong server-side rendering.

## errorCaptured {#errorcaptured}

Được gọi ngay khi bắt được một lỗi lan truyền từ component con.

- **Loại**

  ```ts
  interface ComponentOptions {
    errorCaptured?(
      this: ComponentPublicInstance,
      err: unknown,
      instance: ComponentPublicInstance | null,
      info: string
    ): boolean | void
  }
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

  Bạn có thể thay đổi state của component trong `errorCaptured()` để hiển thị một trạng thái lỗi cho người dùng. Tuy nhiên, điều quan trọng là trạng thái lỗi không được render nội dung gốc đã gây ra lỗi; nếu không component sẽ bị ném vào một vòng lặp render vô hạn.

  Hook có thể trả về `false` để ngăn lỗi lan truyền xa hơn. Xem chi tiết lan truyền lỗi bên dưới.

  **Quy tắc lan truyền lỗi**

  - Mặc định, tất cả các lỗi vẫn được gửi đến [`app.config.errorHandler`](/api/application#app-config-errorhandler) của level application nếu nó được định nghĩa, để các lỗi này vẫn có thể được báo cáo cho một dịch vụ analytics tại một nơi duy nhất.

  - Nếu nhiều hook `errorCaptured` tồn tại trên một inheritance chain hoặc parent chain của component, tất cả chúng sẽ được gọi trên cùng một lỗi, theo thứ tự từ dưới lên. Điều này tương tự như cơ chế bubbling của native DOM events.

  - Nếu hook `errorCaptured` tự ném ra một lỗi, cả lỗi này và lỗi được bắt từ gốc đều được gửi đến `app.config.errorHandler`.

  - Một hook `errorCaptured` có thể trả về `false` để ngăn lỗi lan truyền xa hơn. Điều này về cơ bản có nghĩa là "lỗi này đã được xử lý và nên bị bỏ qua." Nó sẽ ngăn bất kỳ hook `errorCaptured` hoặc `app.config.errorHandler` bổ sung nào được gọi cho lỗi này.

## renderTracked <sup class="vt-badge dev-only" /> {#rendertracked}

Được gọi khi một reactive dependency đã được theo dõi bởi render effect của component.

**Hook này chỉ được gọi trong development mode và không được gọi trong server-side rendering.**

- **Loại**

  ```ts
  interface ComponentOptions {
    renderTracked?(this: ComponentPublicInstance, e: DebuggerEvent): void
  }

  type DebuggerEvent = {
    effect: ReactiveEffect
    target: object
    type: TrackOpTypes /* 'get' | 'has' | 'iterate' */
    key: any
  }
  ```

- **Xem thêm** [Reactivity in Depth](/guide/extras/reactivity-in-depth)

## renderTriggered <sup class="vt-badge dev-only" /> {#rendertriggered}

Được gọi khi một reactive dependency kích hoạt render effect của component để chạy lại.

**Hook này chỉ được gọi trong development mode và không được gọi trong server-side rendering.**

- **Loại**

  ```ts
  interface ComponentOptions {
    renderTriggered?(this: ComponentPublicInstance, e: DebuggerEvent): void
  }

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

## activated {#activated}

Được gọi sau khi thực thể component được chèn vào DOM như một phần của cây và được cache bởi [`<KeepAlive>`](/api/built-in-components#keepalive).

**Hook này không được gọi trong server-side rendering.**

- **Loại**

  ```ts
  interface ComponentOptions {
    activated?(this: ComponentPublicInstance): void
  }
  ```

- **Xem thêm** [Guide - Lifecycle of Cached Instance](/guide/built-ins/keep-alive#lifecycle-of-cached-instance)

## deactivated {#deactivated}

Được gọi sau khi thực thể component được gỡ bỏ khỏi DOM như một phần của cây và được cache bởi [`<KeepAlive>`](/api/built-in-components#keepalive).

**Hook này không được gọi trong server-side rendering.**

- **Loại**

  ```ts
  interface ComponentOptions {
    deactivated?(this: ComponentPublicInstance): void
  }
  ```

- **Xem thêm** [Guide - Lifecycle of Cached Instance](/guide/built-ins/keep-alive#lifecycle-of-cached-instance)

## serverPrefetch <sup class="vt-badge" data-text="SSR only" /> {#serverprefetch}

Hàm async được resolve trước khi thực thể component được render trên server.

- **Loại**

  ```ts
  interface ComponentOptions {
    serverPrefetch?(this: ComponentPublicInstance): Promise<any>
  }
  ```

- **Chi tiết**

  Nếu hook trả về một Promise, server renderer sẽ đợi cho đến khi Promise được resolve trước khi render component.

  Hook này chỉ được gọi trong server-side rendering và có thể được sử dụng để thực hiện data fetching server-only.

- **Ví dụ**

  ```js
  export default {
    data() {
      return {
        data: null
      }
    },
    async serverPrefetch() {
      // component is rendered as part of the initial request
      // pre-fetch data on server as it is faster than on the client
      this.data = await fetchOnServer(/* ... */)
    },
    async mounted() {
      if (!this.data) {
        // if data is null on mount, it means the component
        // is dynamically rendered on the client. Perform a
        // client-side fetch instead.
        this.data = await fetchOnClient(/* ... */)
      }
    }
  }
  ```

- **Xem thêm** [Server-Side Rendering](/guide/scaling-up/ssr)
