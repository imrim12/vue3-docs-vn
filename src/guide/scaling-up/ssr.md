---
outline: deep
---

# Server-Side Rendering (SSR) {#server-side-rendering-ssr}

## Tổng quát {#overview}

### SSR là gì? {#what-is-ssr}

Vue.js là một framework để xây dựng các ứng dụng client-side. Theo mặc định, các thành phần Vue tạo và điều khiển DOM trong trình duyệt như đầu ra. Tuy nhiên, nó cũng có thể được sử dụng để tạo các chuỗi HTML tĩnh trên máy chủ, gửi chúng trực tiếp đến trình duyệt và cuối cùng "hydrate" các đánh dấu tĩnh thành một ứng dụng tương tác đầy đủ trên máy khách.

Một ứng dụng Vue SSR cũng có thể được coi là "isomorphic" hoặc "universal", trong ý nghĩa là hầu hết mã ứng dụng của bạn chạy trên cả máy chủ **và** máy khách.

### Tại sao lại sử dụng SSR? {#why-ssr}

So sánh với một ứng dụng Single-Page Application (SPA) phía máy khách, lợi thế của SSR chủ yếu nằm ở:

- **Thời gian hiển thị content nhanh hơn**: đây là một lợi thế rất lớn đối với các kết nối Internet hoặc thiết bị có tốc độ chậm. Đánh dấu được tạo bởi máy chủ không cần phải chờ đến khi tất cả các mã JavaScript được tải xuống và thực thi để được hiển thị, vì vậy người dùng của bạn sẽ thấy một trang đã được hiển thị hoàn toàn sớm hơn. 
Ngoài ra, việc lấy dữ liệu được thực hiện trên phía server cho lần truy cập đầu tiên, nơi mà kết nối đến cơ sở dữ liệu của bạn có thể nhanh hơn nhiều so với phía máy khách. Điều này thường cải thiện đáng kể các chỉ số trong [Core Web Vitals](https://web.dev/vitals/), trải nghiệm người dùng tốt hơn và có thể là quan trọng đối với các ứng dụng mà thời gian hiển thị content được liên kết trực tiếp với tỷ lệ chuyển đổi.

- **Mô hình tâm lý thống nhất**: bạn có thể sử dụng cùng một ngôn ngữ và cùng một mô hình tâm lý, hướng đối tượng, để phát triển toàn bộ ứng dụng của bạn, thay vì nhảy qua lại giữa một hệ thống mẫu trình bày phía máy chủ và một framework phía máy khách.

- **SEO tốt hơn**: các trình thu thập của công cụ tìm kiếm sẽ trực tiếp nhìn thấy trang đã được hiển thị hoàn toàn.

  :::tip
  Như hiện tại, Google và Bing có thể lập chỉ mục các ứng dụng JavaScript đồng bộ. Đồng bộ là từ khóa quan trọng ở đây. Nếu ứng dụng của bạn bắt đầu với một màn hình tải, sau đó lấy nội dung thông qua Ajax, trình thu thập sẽ không chờ bạn hoàn thành. Điều này có nghĩa là nếu bạn có nội dung được lấy bất đồng bộ trên các trang mà SEO là quan trọng, SSR có thể là cần thiết.
  :::

Tuy nhiên, SSR cũng có một số nhược điểm:

- Ràng buộc phát triển. Các đoạn code chỉ chạy trên trình duyệt chỉ có thể được sử dụng trong một số lifecycle hook; một số thư viện bên ngoài có thể cần xử lý đặc biệt để có thể chạy trong một ứng dụng được render trên server.

- Yêu cầu thiết lập và triển khai phức tạp hơn. Không giống như một SPA tĩnh hoàn toàn, có thể được triển khai trên bất kỳ server tệp tĩnh nào, một ứng dụng được render trên server yêu cầu một môi trường nơi mà server Node.js có thể chạy.

- Tải trọng phía máy chủ cao hơn. Render một ứng dụng đầy đủ trong Node.js sẽ tốn nhiều CPU hơn chỉ phục vụ các tệp tĩnh, vì vậy nếu bạn mong đợi lưu lượng truy cập cao, hãy chuẩn bị cho tải trọng máy chủ tương ứng và sử dụng các chiến lược caching một cách khôn ngoan.

Trước khi sử dụng SSR cho ứng dụng của bạn, câu hỏi đầu tiên bạn nên đặt cho chính mình là liệu bạn có thực sự cần nó hay không. Điều này phụ thuộc chủ yếu vào việc thời gian hiển thị content có quan trọng hay không đối với ứng dụng của bạn. Ví dụ, nếu bạn đang xây dựng một dashboard nội bộ nơi mà vài trăm mili giây bổ sung trong thời gian tải trang không quan trọng, SSR sẽ là trở thành "đao mổ trâu giết gà". Tuy nhiên, trong các trường hợp mà thời gian hiển thị content tuyệt đối quan trọng, SSR có thể giúp bạn đạt được hiệu suất tốt nhất có thể đối với thời gian hiển thị content ban đầu.

### SSR vs. SSG {#ssr-vs-ssg}

**Trình tạo trang tĩnh (SSG)**, còn được gọi là pre-rendering, là một kỹ thuật phổ biến khác để xây dựng các trang web nhanh chóng. Nếu dữ liệu cần thiết để render một trang là giống nhau cho mọi người dùng, thay vì render trang mỗi khi một yêu cầu đến, chúng ta có thể render nó chỉ một lần và dùng mãi mãi, trong bước build. Các trang được render trước được tạo và phục vụ dưới dạng các tệp HTML tĩnh.

SSG giữ nguyên các đặc điểm hiệu suất của các ứng dụng SSR: nó cung cấp hiệu suất thời gian hiển thị content tuyệt vời. Đồng thời, nó cũng rẻ hơn và dễ dàng triển khai hơn các ứng dụng SSR vì đầu ra là HTML và các tài nguyên tĩnh. Từ khóa ở đây là **tĩnh**: SSG chỉ có thể được áp dụng cho các trang tiêu thụ dữ liệu tĩnh, tức là dữ liệu được biết trước vào thời điểm xây dựng và không thay đổi giữa các lần triển khai. Mỗi khi dữ liệu thay đổi, cần phải triển khai lại.

Nếu bạn chỉ đang tìm hiểu về SSR để cải thiện SEO của một số trang tiếp thị (ví dụ: `/`, `/about`, `/contact`, v.v.), thì bạn có thể muốn sử dụng SSG thay vì SSR. SSG cũng rất tuyệt vời cho các trang web dựa trên nội dung như các trang tài liệu hoặc blog. Trên thực tế, trang web bạn đang đọc ngay bây giờ được tạo tĩnh bằng [VitePress](https://vitepress.dev/), một trình tạo trang tĩnh được cung cấp bởi Vue.

## Hướng dẫn cơ bản {#basic-tutorial}

### Render một ứng dụng {#rendering-an-app}

Hãy xem xét một ứng dụng cơ bản nhất của Vue SSR:

1. Tạo một thư mục mới và `cd` vào đó
2. Chạy `npm init -y`
3. Thêm `"type": "module"` vào `package.json` để Node.js chạy ở [chế độ ES modules](https://nodejs.org/api/esm.html#modules-ecmascript-modules).
4. Chạy `npm install vue`
5. Tạo một tệp `example.js`:

```js
// cái này chạy trong Node.js trên máy chủ.
import { createSSRApp } from 'vue'
// Vue SSR API được phơi bày dưới `vue/server-renderer`.
import { renderToString } from 'vue/server-renderer'

const app = createSSRApp({
  data: () => ({ count: 1 }),
  template: `<button @click="count++">{{ count }}</button>`
})

renderToString(app).then((html) => {
  console.log(html)
})
```

Sau đó chạy:

```sh
> node example.js
```

Dòng lệnh sẽ in ra:

```
<button>1</button>
```

[`renderToString()`](/api/ssr#rendertostring) nhận một instance Vue và trả về một Promise giải quyết thành HTML được render của ứng dụng. Cũng có thể render dưới dạng stream bằng cách sử dụng [Node.js Stream API](https://nodejs.org/api/stream.html) hoặc [Web Streams API](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API). Kiểm tra [SSR API Reference](/api/ssr) để biết chi tiết đầy đủ.

Chúng ta có thể di dời Vue SSR code vào một request handler của máy chủ, nơi mà nó bọc markup của ứng dụng với HTML đầy đủ. Chúng ta sẽ sử dụng [`express`](https://expressjs.com/) cho các bước tiếp theo:

- Chạy `npm install express`
- Tạo tệp `server.js` với nội dung sau:

```js
import express from 'express'
import { createSSRApp } from 'vue'
import { renderToString } from 'vue/server-renderer'

const server = express()

server.get('/', (req, res) => {
  const app = createSSRApp({
    data: () => ({ count: 1 }),
    template: `<button @click="count++">{{ count }}</button>`
  })

  renderToString(app).then((html) => {
    res.send(`
    <!DOCTYPE html>
    <html>
      <head>
        <title>Ví dụ về Vue SSR</title>
      </head>
      <body>
        <div id="app">${html}</div>
      </body>
    </html>
    `)
  })
})

server.listen(3000, () => {
  console.log('ready')
})
```

Cuối cùng, chạy `node server.js` và truy cập `http://localhost:3000`. Bạn sẽ thấy trang hoạt động với nút được tạo sẵn.

[Thử trong StackBlitz](https://stackblitz.com/fork/vue-ssr-example-basic?file=index.js)

### Hydration ở phía Client {#client-hydration}

Nếu nhấn vào nút, bạn sẽ nhận thấy count không thay đổi. HTML sẽ ở trong trạng thái tĩnh trên máy khách vì chúng ta chưa tải Vue về trình duyệt.

Để làm cho ứng dụng phía máy khách có thể tương tác được, Vue cần thực hiện bước **hydration**. Trong quá trình hydration, nó tạo ra cùng một ứng dụng Vue đã chạy trên máy chủ, khớp mỗi thành phần với các nút DOM mà nó nên điều khiển và gắn các trình lắng nghe sự kiện DOM.

Để mount một ứng dụng trong chế độ hydration, chúng ta cần sử dụng [`createSSRApp()`](/api/application#createssrapp) thay vì `createApp()`:

```js{2}
// cái này chạy trong trình duyệt.
import { createSSRApp } from 'vue'

const app = createSSRApp({
  // ...cùng một ứng dụng như trên server
})

// mounting một ứng dụng SSR trên máy khách
// giả định rằng HTML đã được render trước
// và sẽ thực hiện hydration thay vì mount các nút DOM mới.
app.mount('#app')
```

### Cấu trúc code {#code-structure}

Hãy chú ý cách chúng ta cần phải tái sử dụng cùng một cài đặt ứng dụng như trên máy chủ. Đây là nơi chúng ta cần bắt đầu suy nghĩ về cấu trúc code trong một ứng dụng SSR - làm thế nào để chúng ta có thể chia sẻ cùng một code ứng dụng giữa máy chủ và máy khách?

Tại đây chúng ta sẽ thực hiện cấu trúc code đơn giản nhất. Đầu tiên, hãy chia logic tạo ứng dụng thành một tệp riêng biệt, `app.js`:

```js
// app.js (được chia sẻ giữa máy chủ và máy khách)
import { createSSRApp } from 'vue'

export function createApp() {
  return createSSRApp({
    data: () => ({ count: 1 }),
    template: `<button @click="count++">{{ count }}</button>`
  })
}
```

File này và các phụ thuộc của nó được chia sẻ giữa máy chủ và máy khách - chúng ta gọi chúng là **universal code**. Có một số điều bạn cần chú ý khi viết universal code, như chúng ta sẽ [thảo luận bên dưới](#writing-ssr-friendly-code).

Điểm đầu vào phía máy khách sẽ nhập universal code, tạo ứng dụng và thực hiện việc mount:

```js
// client.js
import { createApp } from './app.js'

createApp().mount('#app')
```

Và server sẽ sử dụng universal code để tạo ứng dụng và render HTML:

```js{2,5}
// server.js (irrelevant code omitted)
import { createApp } from './app.js'

server.get('/', (req, res) => {
  const app = createApp()
  renderToString(app).then(html => {
    // ...
  })
})
```

Bên cạnh đó, để có thể tải các tệp trong trình duyệt trên máy khách, ta còn cần phải:

1. Gửi các tệp máy khách bằng cách sử dụng `express.static()` trong `server.js`:
2. Tải đầu vào của máy khách bằng cách thêm `<script src="/client.js"></script>` vào HTML shell.
3. Hỗ trợ việc sử dụng `import * from 'vue'` trong trình duyệt bằng cách thêm một [Import Map](https://github.com/WICG/import-maps) vào HTML shell.

[Thử ví dụ đầy đủ trên StackBlitz](https://stackblitz.com/fork/vue-ssr-example?file=index.js). Nút bây giờ đã có thể tương tác trên máy khách

## Higher Level Solutions {#higher-level-solutions}

Chuyển đổi từ ví dụ đơn giản sang một ứng dụng SSR thực tế sẽ cần nhiều yếu tố hơn. Chúng ta sẽ cần:

- Hỗ trợ Vue SFC và các yêu cầu về xây dựng khác. Trong thực tế, chúng ta sẽ cần phối hợp hai bước xây dựng cho cùng một ứng dụng: một cho máy khách và một cho máy chủ.

- Support Vue SFCs and other build step requirements. In fact, we will need to coordinate two builds for the same app: one for the client, and one for the server.

  :::tip
  Vue component được biên dịch khác nhau khi được sử dụng cho SSR - các template được biên dịch thành chuỗi nối thay vì các hàm render Virtual DOM để tăng hiệu suất hiển thị.
  :::

- Trong server request handler, render HTML với các liên kết tài nguyên phù hợp với phía máy khách và các gợi ý tài nguyên tối ưu. Chúng ta cũng có thể cần chuyển đổi giữa chế độ SSR và SSG, hoặc thậm chí kết hợp cả hai trong cùng một ứng dụng.
- In the server request handler, render the HTML with the correct client-side asset links and optimal resource hints. We may also need to switch between SSR and SSG mode, or even mix both in the same app.

- Quản lý điều hướng, lấy dữ liệu và các kho lưu trữ state một cách đồng nhất.
- Manage routing, data fetching, and state management stores in a universal manner.

Một cài đặt đầy đủ sẽ khá phức tạp và phụ thuộc vào bộ công cụ xây dựng mà bạn đã chọn để làm việc. Do đó, chúng tôi rất khuyến khích bạn nên sử dụng một giải pháp có mức độ cao hơn, mang tính chủ quan hơn để trừu tượng hóa các chi tiết phức tạp cho bạn. Dưới đây chúng tôi sẽ giới thiệu một số giải pháp SSR được khuyến nghị trong hệ sinh thái Vue.

### Nuxt {#nuxt}

[Nuxt](https://nuxt.com/) là một framework cao cấp được xây dựng trên cơ sở của hệ sinh thái Vue, cung cấp một trải nghiệm phát triển được tối ưu hóa cho việc viết các ứng dụng Vue đa nền tảng. Hơn thế nữa, bạn cũng có thể sử dụng nó như một trình tạo trang tĩnh! Chúng tôi rất khuyến khích bạn nên thử nó.

### Quasar {#quasar}

[Quasar](https://quasar.dev) là một giải pháp dựa trên Vue hoàn chỉnh cho phép bạn tạo ra SPA, SSR, PWA, ứng dụng di động, ứng dụng desktop và tiện ích trình duyệt chỉ bằng một codebase. Nó không chỉ xử lý thiết lập xây dựng, mà còn cung cấp một bộ sưu tập đầy đủ các thành phần UI tuân thủ Material Design.

### Vite SSR {#vite-ssr}

Vite cung cấp các [hỗ trợ tích hợp sẵn cho Vue server-side rendering](https://vitejs.dev/guide/ssr.html), nhưng nó được thiết kế với mức độ low-level. Nếu bạn muốn tiếp tục với Vite, hãy xem [vite-plugin-ssr](https://vite-plugin-ssr.com/), một plugin cộng đồng trừu tượng hóa nhiều chi tiết phức tạp cho bạn.

Bạn cũng có thể tìm thấy một ví dụ về dự án Vue + Vite SSR sử dụng thiết lập thủ công [ở đây](https://github.com/vitejs/vite-plugin-vue/tree/main/playground/ssr-vue), nó có thể được sử dụng như một cơ sở để xây dựng. Lưu ý rằng điều này chỉ được khuyến khích nếu bạn có kinh nghiệm với SSR / các công cụ xây dựng và thực sự muốn có sự kiểm soát hoàn toàn về kiến trúc cấp cao hơn.

## Writing SSR-friendly Code {#writing-ssr-friendly-code}

Regardless of your build setup or higher-level framework choice, there are some principles that apply in all Vue SSR applications.

### Reactivity on the Server {#reactivity-on-the-server}

During SSR, each request URL maps to a desired state of our application. There is no user interaction and no DOM updates, so reactivity is unnecessary on the server. By default, reactivity is disabled during SSR for better performance.

### Component Lifecycle Hooks {#component-lifecycle-hooks}

Since there are no dynamic updates, lifecycle hooks such as <span class="options-api">`mounted`</span><span class="composition-api">`onMounted`</span> or <span class="options-api">`updated`</span><span class="composition-api">`onUpdated`</span> will **NOT** be called during SSR and will only be executed on the client.<span class="options-api"> The only hooks that are called during SSR are `beforeCreate` and `created`</span>

You should avoid code that produces side effects that need cleanup in <span class="options-api">`beforeCreate` and `created`</span><span class="composition-api">`setup()` or the root scope of `<script setup>`</span>. An example of such side effects is setting up timers with `setInterval`. In client-side only code we may setup a timer and then tear it down in <span class="options-api">`beforeUnmount`</span><span class="composition-api">`onBeforeUnmount`</span> or <span class="options-api">`unmounted`</span><span class="composition-api">`onUnmounted`</span>. However, because the unmount hooks will never be called during SSR, the timers will stay around forever. To avoid this, move your side-effect code into <span class="options-api">`mounted`</span><span class="composition-api">`onMounted`</span> instead.

### Access to Platform-Specific APIs {#access-to-platform-specific-apis}

Universal code cannot assume access to platform-specific APIs, so if your code directly uses browser-only globals like `window` or `document`, they will throw errors when executed in Node.js, and vice-versa.

For tasks that are shared between server and client but with different platform APIs, it's recommended to wrap the platform-specific implementations inside a universal API, or use libraries that do this for you. For example, you can use [`node-fetch`](https://github.com/node-fetch/node-fetch) to use the same fetch API on both server and client.

For browser-only APIs, the common approach is to lazily access them inside client-only lifecycle hooks such as <span class="options-api">`mounted`</span><span class="composition-api">`onMounted`</span>.

Note that if a third-party library is not written with universal usage in mind, it could be tricky to integrate it into a server-rendered app. You _might_ be able to get it working by mocking some of the globals, but it would be hacky and may interfere with the environment detection code of other libraries.

### Cross-Request State Pollution {#cross-request-state-pollution}

In the State Management chapter, we introduced a [simple state management pattern using Reactivity APIs](state-management#simple-state-management-with-reactivity-api). In an SSR context, this pattern requires some additional adjustments.

The pattern declares shared state in a JavaScript module's root scope. This makes them **singletons** - i.e. there is only one instance of the reactive object throughout the entire lifecycle of our application. This works as expected in a pure client-side Vue application, since the modules in our application are initialized fresh for each browser page visit.

However, in an SSR context, the application modules are typically initialized only once on the server, when the server boots up. The same module instances will be reused across multiple server requests, and so will our singleton state objects. If we mutate the shared singleton state with data specific to one user, it can be accidentally leaked to a request from another user. We call this **cross-request state pollution.**

We can technically re-initialize all the JavaScript modules on each request, just like we do in browsers. However, initializing JavaScript modules can be costly, so this would significantly affect server performance.

The recommended solution is to create a new instance of the entire application - including the router and global stores - on each request. Then, instead of directly importing it in our components, we provide the shared state using [app-level provide](/guide/components/provide-inject#app-level-provide) and inject it in components that need it:

```js
// app.js (shared between server and client)
import { createSSRApp } from 'vue'
import { createStore } from './store.js'

// called on each request
export function createApp() {
  const app = createSSRApp(/* ... */)
  // create new instance of store per request
  const store = createStore(/* ... */)
  // provide store at the app level
  app.provide('store', store)
  // also expose store for hydration purposes
  return { app, store }
}
```

State Management libraries like Pinia are designed with this in mind. Consult [Pinia's SSR guide](https://pinia.vuejs.org/ssr/) for more details.

### Hydration Mismatch {#hydration-mismatch}

If the DOM structure of the pre-rendered HTML does not match the expected output of the client-side app, there will be a hydration mismatch error. Hydration mismatch is most commonly introduced by the following causes:

1. The template contains invalid HTML nesting structure, and the rendered HTML got "corrected" by the browser's native HTML parsing behavior. For example, a common gotcha is that [`<div>` cannot be placed inside `<p>`](https://stackoverflow.com/questions/8397852/why-cant-the-p-tag-contain-a-div-tag-inside-it):

   ```html
   <p><div>hi</div></p>
   ```

   If we produce this in our server-rendered HTML, the browser will terminate the first `<p>` when `<div>` is encountered and parse it into the following DOM structure:

   ```html
   <p></p>
   <div>hi</div>
   <p></p>
   ```

2. The data used during render contains randomly generated values. Since the same application will run twice - once on the server, and once on the client - the random values are not guaranteed to be the same between the two runs. There are two ways to avoid random-value-induced mismatches:

   1. Use `v-if` + `onMounted` to render the part that depends on random values only on the client. Your framework may also have built-in features to make this easier, for example the `<ClientOnly>` component in VitePress.

   2. Use a random number generator library that supports generating with seeds, and guarantee the server run and the client run are using the same seed (e.g. by including the seed in serialized state and retrieving it on the client).

3. The server and the client are in different time zones. Sometimes, we may want to convert a timestamp into the user's local time. However, the timezone during the server run and the timezone during the client run are not always the same, and we may not reliably know the user's timezone during the server run. In such cases, the local time conversion should also be performed as a client-only operation.

When Vue encounters a hydration mismatch, it will attempt to automatically recover and adjust the pre-rendered DOM to match the client-side state. This will lead to some rendering performance loss due to incorrect nodes being discarded and new nodes being mounted, but in most cases, the app should continue to work as expected. That said, it is still best to eliminate hydration mismatches during development.

### Custom Directives {#custom-directives}

Since most custom directives involve direct DOM manipulation, they are ignored during SSR. However, if you want to specify how a custom directive should be rendered (i.e. what attributes it should add to the rendered element), you can use the `getSSRProps` directive hook:

```js
const myDirective = {
  mounted(el, binding) {
    // client-side implementation:
    // directly update the DOM
    el.id = binding.value
  },
  getSSRProps(binding) {
    // server-side implementation:
    // return the props to be rendered.
    // getSSRProps only receives the directive binding.
    return {
      id: binding.value
    }
  }
}
```

### Teleports {#teleports}

Teleports require special handling during SSR. If the rendered app contains Teleports, the teleported content will not be part of the rendered string. An easier solution is to conditionally render the Teleport on mount.

If you do need to hydrate teleported content, they are exposed under the `teleports` property of the ssr context object:

```js
const ctx = {}
const html = await renderToString(app, ctx)

console.log(ctx.teleports) // { '#teleported': 'teleported content' }
```

You need to inject the teleport markup into the correct location in your final page HTML similar to how you need to inject the main app markup.

:::tip
Avoid targeting `body` when using Teleports and SSR together - usually, `<body>` will contain other server-rendered content which makes it impossible for Teleports to determine the correct starting location for hydration.

Instead, prefer a dedicated container, e.g. `<div id="teleported"></div>` which contains only teleported content.
:::
