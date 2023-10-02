---
footer: false
---

# Bắt đầu nhanh {#quick-start}

## Thử Vue online {#try-vue-online}

- Để nhanh chóng có một cái nhìn tổng quan về Vue, bạn có thể thử nó trực tiếp trên [Playground](https://play.vuejs.org/#eNo9jcEKwjAMhl/lt5fpQYfXUQfefAMvvRQbddC1pUuHUPrudg4HIcmXjyRZXEM4zYlEJ+T0iEPgXjn6BB8Zhp46WUZWDjCa9f6w9kAkTtH9CRinV4fmRtZ63H20Ztesqiylphqy3R5UYBqD1UyVAPk+9zkvV1CKbCv9poMLiTEfR2/IXpSoXomqZLtti/IFwVtA9A==).

- Nếu bạn thích thiết lập HTML đơn giản mà không có bất kỳ bước build nào, bạn có thể sử dụng [JSFiddle](https://jsfiddle.net/yyx990803/2ke1ab0z/) này làm điểm bắt đầu.

- Nếu bạn đã quen thuộc với Node.js và khái niệm về các công cụ build, bạn cũng có thể thử thiết lập build hoàn chỉnh ngay trong chính trình duyệt của bạn trên [StackBlitz](https://vite.new/vue).

## Tạo một ứng dụng Vue {#creating-a-vue-application}

:::tip Yêu cầu tiên quyết

- Quen thuộc với dòng lệnh
- Cài đặt [Node.js](https://nodejs.org/) phiên bản 16.0 hoặc cao hơn
  :::

Trong phần này, chúng tôi sẽ giới thiệu cách tạo một [Single Page Application](/guide/extras/ways-of-using-vue#single-page-application-spa) Vue trên máy cục bộ của bạn. Dự án sẽ thiết lập build dựa trên [Vite](https://vitejs.dev) và cho phép chúng ta sử dụng [Single-File Components](/guide/scaling-up/sfc) (SFCs) Vue.

Đảm bảo bạn đã cài đặt phiên bản [Node.js](https://nodejs.org/) mới nhất và thư mục làm việc hiện tại của bạn là thư mục mà bạn dự định tạo dự án. Chạy lệnh sau trong command line của bạn (không có dấu `>`):

<div class="language-sh"><pre><code><span class="line"><span style="color:var(--vt-c-green);">&gt;</span> <span style="color:#A6ACCD;">npm create vue@latest</span></span></code></pre></div>

Lệnh này sẽ cài đặt và thực thi [create-vue](https://github.com/vuejs/create-vue), công cụ tạo cấu trúc dự án Vue chính thức. Bạn sẽ được yêu cầu nhập một số lựa chọn như TypeScript và hỗ trợ test:

<div class="language-sh"><pre><code><span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Project name: <span style="color:#888;">… <span style="color:#89DDFF;">&lt;</span><span style="color:#888;">your-project-name</span><span style="color:#89DDFF;">&gt;</span></span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add TypeScript? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add JSX Support? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Vue Router for Single Page Application development? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Pinia for state management? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Vitest for Unit testing? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add an End-to-End Testing Solution? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Cypress / Playwright</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add ESLint for code quality? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Prettier for code formatting? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span></span>
<span style="color:#A6ACCD;">Scaffolding project in ./<span style="color:#89DDFF;">&lt;</span><span style="color:#888;">your-project-name</span><span style="color:#89DDFF;">&gt;</span>...</span>
<span style="color:#A6ACCD;">Done.</span></code></pre></div>

Nếu bạn không chắc về một lựa chọn nào đó, chỉ cần chọn `No` bằng cách nhấn enter. Sau khi dự án được tạo, làm theo hướng dẫn để cài đặt các dependency và start server dev:

<div class="language-sh"><pre><code><span class="line"><span style="color:var(--vt-c-green);">&gt; </span><span style="color:#A6ACCD;">cd</span><span style="color:#A6ACCD;"> </span><span style="color:#89DDFF;">&lt;</span><span style="color:#888;">your-project-name</span><span style="color:#89DDFF;">&gt;</span></span>
<span class="line"><span style="color:var(--vt-c-green);">&gt; </span><span style="color:#A6ACCD;">npm install</span></span>
<span class="line"><span style="color:var(--vt-c-green);">&gt; </span><span style="color:#A6ACCD;">npm run dev</span></span>
<span class="line"></span></code></pre></div>

Bây giờ bạn đã có dự án Vue đầu tiên của mình! Lưu ý rằng các component ví dụ trong dự án được viết bằng [Composition API](/guide/introduction#composition-api) và `<script setup>`, thay vì [Options API](/guide/introduction#options-api). Dưới đây là một số mẹo bổ sung:

- IDE được đề xuất sử dụng là [Visual Studio Code](https://code.visualstudio.com/) + [Volar extension](https://marketplace.visualstudio.com/items?itemName=Vue.volar). Nếu bạn sử dụng các trình soạn thảo khác, hãy xem [Phần hỗ trợ IDE](/guide/scaling-up/tooling#ide-support).
- Chi tiết về các công cụ, bao gồm tích hợp với các framework backend, được thảo luận trong [Hướng dẫn công cụ](/guide/scaling-up/tooling).
- Để tìm hiểu thêm về công cụ build Vite, hãy xem [Tài liệu Vite](https://vitejs.dev).
- Nếu bạn chọn sử dụng TypeScript, hãy xem [Hướng dẫn sử dụng TypeScript](typescript/overview).

Khi bạn đã sẵn sàng deploy ứng dụng của mình lên production, hãy chạy lệnh sau:

<div class="language-sh"><pre><code><span class="line"><span style="color:var(--vt-c-green);">&gt; </span><span style="color:#A6ACCD;">npm run build</span></span>
<span class="line"></span></code></pre></div>

Việc này sẽ tạo ra một bộ build sẵn dành cho production của ứng dụng trong thư mục `./dist` của dự án. Hãy xem [Hướng dẫn deploy production](/guide/best-practices/production-deployment) để tìm hiểu thêm về việc deploy ứng dụng của bạn lên production.

[Những bước tiếp theo >](#next-steps)

## Sử dụng Vue từ CDN {#using-vue-from-cdn}

Bạn có thể sử dụng Vue trực tiếp từ CDN thông qua một thẻ script:

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
```

Đây là ví dụ sử dụng [unpkg](https://unpkg.com/), nhưng bạn cũng có thể sử dụng bất kỳ CDN nào cung cấp các package npm, ví dụ như [jsdelivr](https://www.jsdelivr.com/package/npm/vue) hoặc [cdnjs](https://cdnjs.com/libraries/vue). Tất nhiên, bạn cũng có thể tải xuống file này và tự sử dụng.

Khi sử dụng Vue từ CDN, không có "bước build" nào được thực hiện. Việc này làm cho công việc thiết lập đơn giản hơn rất nhiều và phù hợp cho HTML tĩnh hoặc tích hợp với một framework backend. Tuy nhiên, bạn sẽ không thể sử dụng cú pháp Single-File Component (SFC).

### Sử dụng Global Build {#using-the-global-build}

Liên kết trên tải _global build_ của Vue, trong đó tất cả các API cấp cao nhất được thể hiện như các thuộc tính trên đối tượng `Vue` global. Dưới đây là một ví dụ đầy đủ sử dụng global build:

<div class="options-api">

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>

<div id="app">{{ message }}</div>

<script>
  const { createApp } = Vue

  createApp({
    data() {
      return {
        message: 'Hello Vue!'
      }
    }
  }).mount('#app')
</script>
```

[Codepen demo](https://codepen.io/vuejs-examples/pen/QWJwJLp)

</div>

<div class="composition-api">

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>

<div id="app">{{ message }}</div>

<script>
  const { createApp, ref } = Vue

  createApp({
    setup() {
      const message = ref('Hello vue!')
      return {
        message
      }
    }
  }).mount('#app')
</script>
```

[Codepen demo](https://codepen.io/vuejs-examples/pen/eYQpQEG)

:::tip
Những ví dụ về Composition API trong suốt hướng dẫn sẽ sử dụng cú pháp `<script setup>`, yêu cầu các công cụ build. Nếu bạn dự định sử dụng Composition API mà không có bước build, hãy xem cách sử dụng [option `setup()`](/api/composition-api-setup).
:::

</div>

### Sử dụng ES Module Build {#using-the-es-module-build}

Trong phần còn lại của tài liệu, chúng ta sẽ chủ yếu sử dụng cú pháp [ES modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules). Hầu hết các trình duyệt hiện đại bây giờ hỗ trợ các native ES module, vì vậy chúng ta có thể sử dụng Vue từ CDN thông qua các native ES module như sau:

<div class="options-api">

```html{3,4}
<div id="app">{{ message }}</div>

<script type="module">
  import { createApp } from 'https://unpkg.com/vue@3/dist/vue.esm-browser.js'

  createApp({
    data() {
      return {
        message: 'Hello Vue!'
      }
    }
  }).mount('#app')
</script>
```

</div>

<div class="composition-api">

```html{3,4}
<div id="app">{{ message }}</div>

<script type="module">
  import { createApp, ref } from 'https://unpkg.com/vue@3/dist/vue.esm-browser.js'

  createApp({
    setup() {
      const message = ref('Hello Vue!')
      return {
        message
      }
    }
  }).mount('#app')
</script>
```

</div>

Lưu ý rằng chúng ta đang sử dụng `<script type="module">`, và URL CDN được import đang trỏ đến **ES modules build** của Vue.

<div class="options-api">

[Codepen demo](https://codepen.io/vuejs-examples/pen/VwVYVZO)

</div>
<div class="composition-api">

[Codepen demo](https://codepen.io/vuejs-examples/pen/MWzazEv)

</div>

### Kích hoạt Import maps {#enabling-import-maps}

Trong ví dụ trên, chúng ta đang import từ URL CDN đầy đủ, nhưng trong phần còn lại của tài liệu bạn sẽ thấy code như sau:

```js
import { createApp } from 'vue'
```

Chúng ta có thể dạy trình duyệt nơi đặt `vue` import bằng cách sử dụng [Import Maps](https://caniuse.com/import-maps):

<div class="options-api">

```html{1-7,12}
<script type="importmap">
  {
    "imports": {
      "vue": "https://unpkg.com/vue@3/dist/vue.esm-browser.js"
    }
  }
</script>

<div id="app">{{ message }}</div>

<script type="module">
  import { createApp } from 'vue'

  createApp({
    data() {
      return {
        message: 'Hello Vue!'
      }
    }
  }).mount('#app')
</script>
```

[Codepen demo](https://codepen.io/vuejs-examples/pen/wvQKQyM)

</div>

<div class="composition-api">

```html{1-7,12}
<script type="importmap">
  {
    "imports": {
      "vue": "https://unpkg.com/vue@3/dist/vue.esm-browser.js"
    }
  }
</script>

<div id="app">{{ message }}</div>

<script type="module">
  import { createApp, ref } from 'vue'

  createApp({
    setup() {
      const message = ref('Hello Vue!')
      return {
        message
      }
    }
  }).mount('#app')
</script>
```

[Codepen demo](https://codepen.io/vuejs-examples/pen/YzRyRYM)

</div>

Bạn cũng có thể thêm các mục khác cho các dependency vào map import - nhưng hãy đảm bảo chúng trỏ đến phiên bản ES modules của thư viện bạn dự định sử dụng.

:::tip Hỗ trợ trình duyệt Import Maps
Import Maps là một tính năng trình duyệt tương đối mới. Hãy đảm bảo sử dụng trình duyệt trong [phạm vi hỗ trợ](https://caniuse.com/import-maps). Đặc biệt, nó chỉ được hỗ trợ trong Safari 16.4+.
:::

Các ví dụ cho đến nay đang sử dụng development build của Vue - nếu bạn dự định sử dụng Vue từ CDN trong production, hãy đảm bảo kiểm tra [hướng dẫn deploy production](/guide/best-practices/production-deployment#without-build-tools).
:::

### Chia các Modules {#splitting-up-the-modules}

Khi chúng ta đào sâu hơn vào hướng dẫn, chúng ta có thể cần phải chia code thành các file JavaScript riêng biệt để chúng dễ quản lý hơn. Ví dụ:

```html
<!-- index.html -->
<div id="app"></div>

<script type="module">
  import { createApp } from 'vue'
  import MyComponent from './my-component.js'

  createApp(MyComponent).mount('#app')
</script>
```

<div class="options-api">

```js
// my-component.js
export default {
  data() {
    return { count: 0 }
  },
  template: `<div>count is {{ count }}</div>`
}
```

</div>
<div class="composition-api">

```js
// my-component.js
import { ref } from 'vue'
export default {
  setup() {
    const count = ref(0)
    return { count }
  },
  template: `<div>count is {{ count }}</div>`
}
```

</div>

Nếu bạn mở trực tiếp `index.html` trên trong trình duyệt của bạn, bạn sẽ thấy nó báo lỗi vì ES modules không thể hoạt động trên giao thức `file://`, đó là giao thức mà trình duyệt sử dụng khi bạn mở một file local.

Vì lý do bảo mật, ES modules chỉ có thể hoạt động trên giao thức `http://`, đó là giao thức mà các trình duyệt sử dụng khi mở các trang web. Để ES modules có thể hoạt động trên máy cục bộ của chúng ta, chúng ta cần phải phục vụ `index.html` qua giao thức `http://`, với một local HTTP server.

Để bắt đầu một server local HTTP, trước tiên đảm bảo bạn đã cài đặt [Node.js](https://nodejs.org/en/), sau đó chạy `npx serve` từ command line trong cùng thư mục với file HTML của bạn. Bạn cũng có thể sử dụng bất kỳ server HTTP nào khác có thể phục vụ các file tĩnh với các MIME type chính xác.

Bạn có thể nhận thấy rằng template của component được import trên một dòng như một chuỗi JavaScript. Nếu bạn đang sử dụng VSCode, bạn có thể cài đặt extension [es6-string-html](https://marketplace.visualstudio.com/items?itemName=Tobermory.es6-string-html) và thêm prefix `/*html*/` vào đầu chuỗi để có được syntax highlighting cho chúng.

## Những bước tiếp theo {#next-steps}

Nếu bạn bỏ qua [Giới thiệu](/guide/introduction), chúng tôi khuyến khích bạn nên đọc nó trước khi chuyển sang phần còn lại của tài liệu.

<div class="vt-box-container next-steps">
  <a class="vt-box" href="/guide/essentials/application.html">
    <p class="next-steps-link">Tiếp tục với Hướng dẫn</p>
    <p class="next-steps-caption">Hướng dẫn sẽ đưa bạn đi qua mọi khía cạnh của framework một cách chi tiết đầy đủ.</p>
  </a>
  <a class="vt-box" href="/tutorial/">
    <p class="next-steps-link">Thử xem video hướng dẫn</p>
    <p class="next-steps-caption">Dành cho những người thích học bằng cách thực hành.</p>
  </a>
  <a class="vt-box" href="/examples/">
    <p class="next-steps-link">Xem các ví dụ</p>
    <p class="next-steps-caption">Khám phá các ví dụ về các tính năng cốt lõi và các tác vụ UI chung.</p>
  </a>
</div>
