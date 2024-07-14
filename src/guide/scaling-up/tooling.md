# Tooling {#tooling}

## Try It Online {#try-it-online}

Bạn không cần phải cài đặt bất cứ thứ gì trên máy tính của bạn để thử Vue SFC - có những sân chơi trực tuyến cho phép bạn làm điều đó ngay trong trình duyệt:

- [Sân chơi Vue SFC](https://play.vuejs.org)
  - Luôn luôn triển khai từ commit mới nhất
  - Được thiết kế để kiểm tra kết quả biên dịch thành phần
- [Vue + Vite on StackBlitz](https://vite.new/vue)
  - Môi trường IDE giống như thực sự chạy Vite dev server trong trình duyệt
  - Gần giống với cài đặt cục bộ nhất

Việc sử dụng các sân chơi trực tuyến này cũng được khuyến khích khi báo cáo lỗi.

## Project Scaffolding {#project-scaffolding}

### Vite {#vite}

[Vite](https://vitejs.dev/) là một công cụ build nhẹ và nhanh với hỗ trợ Vue SFC tân tiến nhất. Nó được tạo ra bởi Evan You, là tác giả của Vue!

Để bắt đầu với Vite + Vue, chỉ cần chạy chạy:

<div class="language-sh"><pre><code><span class="line"><span style="color:var(--vt-c-green);">$</span> <span style="color:#A6ACCD;">npm create vue@latest</span></span></code></pre></div>

Câu lệnh này sẽ cài đặt và thực thi [create-vue](https://github.com/vuejs/create-vue), công cụ tạo project chính thức của Vue.

- Để tìm hiểu thêm về Vite, hãy xem [Vite docs](https://vitejs.dev).
- Để cấu hình các hành vi cụ thể của Vue trong một project Vite, ví dụ như truyền các tùy chọn cho trình biên dịch Vue, hãy xem tài liệu cho [@vitejs/plugin-vue](https://github.com/vitejs/vite-plugin-vue/tree/main/packages/plugin-vue#readme).

Cả hai sân chơi trực tuyến được đề cập ở trên cũng hỗ trợ tải xuống các file dưới dạng một project Vite.

### Vue CLI {#vue-cli}

[Vue CLI](https://cli.vuejs.org/) là công cụ tạo project chính thức của Vue. Hiện tại nó đang ở chế độ bảo trì và chúng tôi khuyến khích bạn sử dụng Vite cho các project mới. Vite sẽ cung cấp trải nghiệm phát triển tốt hơn trong hầu hết các trường hợp.

Để biết thêm thông tin về cách migrate từ Vue CLI sang Vite:

- [Vue CLI -> Hướng dẫn Vite Migration từ VueSchool.io](https://vueschool.io/articles/vuejs-tutorials/how-to-migrate-from-vue-cli-to-vite/)
- [Công cụ / Plugin hỗ trợ tự động migration](https://github.com/vitejs/awesome-vite#vue-cli)

### Lưu ý về việc biên dịch Template trực tiếp trong trình duyệt {#note-on-in-browser-template-compilation}

Khi sử dụng Vue mà không thông qua bước build, các component template được viết trực tiếp trong HTML của trang hoặc như là các chuỗi JavaScript được viết inline. Đối với những trường hợp như vậy, Vue cần phải gửi trình biên dịch template đến trình duyệt để thực hiện biên dịch template ngay lập tức. Trong trường hợp ngược lại, trình biên dịch sẽ không cần thiết nếu chúng ta biên dịch các template trước với bước build. Để giảm kích thước của bundle trên client, Vue cung cấp [những "builds" khác nhau](https://unpkg.com/browse/vue@3/dist/) được tối ưu hóa cho các trường hợp sử dụng khác nhau.

- Những build file bắt đầu với `vue.runtime.*` là những **bản buils chỉ dành cho runtime**: chúng không bao gồm trình biên dịch. Khi sử dụng những build này, tất cả các template phải được biên dịch sẵn từ trước thông qua bước build.

- Những build file không bao gồm `.runtime` là những **bản build đầy đủ**: chúng bao gồm trình biên dịch và hỗ trợ biên dịch template trực tiếp trong trình duyệt. Tuy nhiên, chúng sẽ làm tăng kích thước của bundle lên khoảng 14kb.

Các cài đặt mặc định của chúng tôi sử dụng bản build chỉ dành cho runtime vì tất cả các template trong SFC đều được biên dịch sẵn từ trước. Nếu, vì một lý do nào đó, bạn cần biên dịch template trực tiếp trong trình duyệt ngay cả khi có bước build, bạn có thể làm như vậy bằng cách cấu hình công cụ build để alias `vue` thành `vue/dist/vue.esm-bundler.js` thay vì `vue/dist/vue.runtime.esm-bundler.js`.

Nếu bạn đang tìm kiếm một giải pháp nhẹ hơn cho việc sử dụng Vue mà không thông qua bước build, hãy xem [petite-vue](https://github.com/vuejs/petite-vue).

## Hỗ trợ về IDE {#ide-support}

- Cấu hình IDE được khuyến khích là [VSCode](https://code.visualstudio.com/) + tiện ích/plugin [Vue Language Features (Volar)](https://marketplace.visualstudio.com/items?itemName=Vue.volar). Tiện ích này cung cấp các tính năng như đánh dấu cú pháp (syntax highlighting), hỗ trợ TypeScript, và intellisense cho các biểu thức template và các props của component.

  :::tip
  Volar thay thế cho [Vetur](https://marketplace.visualstudio.com/items?itemName=octref.vetur), tiện ích VSCode chính thức trước đây của chúng tôi cho Vue 2. Nếu bạn đã cài đặt Vetur, hãy chắc chắn tắt nó trong các project Vue 3.
  :::

- [WebStorm](https://www.jetbrains.com/webstorm/) cũng cung cấp những hỗ trợ tuyệt vời cho Vue SFCs.

- Những IDE khác có hỗ trợ cho [Language Server Protocol](https://microsoft.github.io/language-server-protocol/) (LSP) cũng có thể tận dụng những tính năng cốt lõi của Volar thông qua LSP:

  - Hỗ trợ Sublime Text thông qua [LSP-Volar](https://github.com/sublimelsp/LSP-volar).

  - Hỗ trợ vim / Neovim thông qua [coc-volar](https://github.com/yaegassy/coc-volar).

  - Hỗ trợ emacs thông qua [lsp-mode](https://emacs-lsp.github.io/lsp-mode/page/lsp-volar/).

## Devtools trong trình duyệt{#browser-devtools}

<VueSchoolLink href="https://vueschool.io/lessons/using-vue-dev-tools-with-vuejs-3" title="Free Vue.js Devtools Lesson"/>Standalone

Vue Devtools là một extension cho trình duyệt cho phép bạn khám phá cây component của một ứng dụng Vue, kiểm tra trạng thái của các component riêng lẻ, theo dõi các sự kiện quản lý trạng thái, và đo hiệu năng.

![devtools screenshot](https://raw.githubusercontent.com/vuejs/devtools/main/media/screenshot-shadow.png)

- [Documentation](https://devtools.vuejs.org/)
- [Tiện ích trên Chrome](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)
- [Tiện ích trên Firefox](https://addons.mozilla.org/en-US/firefox/addon/vue-js-devtools/)
- [Tiện ích trên Edge](https://microsoftedge.microsoft.com/addons/detail/vuejs-devtools/olofadcdnkkjdfgjcmjaadnlehnnihnl)
- [Ứng dụng độc lập cho Electron](https://devtools.vuejs.org/guide/installation.html#standalone)

## TypeScript {#typescript}

Bài viết chính: [Sử dụng Vue với TypeScript](/guide/typescript/overview).

- [Volar](https://github.com/johnsoncodehk/volar) cung cấp kiểm tra kiểu cho SFCs sử dụng các khối `<script lang="ts">`, bao gồm cả các biểu thức template và kiểm tra props giữa các component.

- Sử dụng [`vue-tsc`](https://github.com/vuejs/language-tools/tree/master/packages/vue-tsc) để thực hiện kiểm tra kiểu tương tự từ dòng lệnh, hoặc để tạo ra các file `d.ts` cho SFCs.

## Kiểm thử {#testing}

Bài viết chính: [Hướng dẫn Kiểm thử](/guide/scaling-up/testing).

- [Cypress](https://www.cypress.io/) được khuyến khích cho các kiểm thử E2E. Nó cũng có thể được sử dụng cho các kiểm thử component cho Vue SFCs thông qua [Cypress Component Test Runner](https://docs.cypress.io/guides/component-testing/introduction).

- [Vitest](https://vitest.dev/) là một trình chạy kiểm thử được tạo ra bởi các thành viên của Vue / Vite, tập trung vào tốc độ. Nó được thiết kế đặc biệt cho các ứng dụng dựa trên Vite để cung cấp vòng lặp phản hồi tức thì tương tự cho việc kiểm thử unit / component.

- [Jest](https://jestjs.io/) có thể hoạt động với Vite thông qua [vite-jest](https://github.com/sodatea/vite-jest). Tuy nhiên, điều này chỉ được khuyến khích nếu bạn có các bộ kiểm thử dựa trên Jest hiện có mà bạn cần phải chuyển sang một cài đặt dựa trên Vite, vì Vitest cung cấp các chức năng tương tự với một tích hợp hiệu quả hơn nhiều.

## Kiểm tra cú pháp {#linting}

Đội ngũ Vue duy trì [eslint-plugin-vue](https://github.com/vuejs/eslint-plugin-vue), một plugin [ESLint](https://eslint.org/) hỗ trợ các quy tắc kiểm tra cú pháp cụ thể cho SFC.

Những người dùng trước đây sử dụng Vue CLI có thể đã quen thuộc với việc cấu hình các linter thông qua webpack loaders. Tuy nhiên, khi sử dụng Vite, khuyến nghị chung của chúng tôi là:

1. `npm install -D eslint eslint-plugin-vue`, sau đó làm theo [hướng dẫn cấu hình](https://eslint.vuejs.org/user-guide/#usage)`eslint-plugin-vue`.

2. Cấu hình tiện ích/plugin ESLint trong IDE, ví dụ như [ESLint cho VSCode](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint), để bạn có được phản hồi linter ngay trong trình soạn thảo trong quá trình phát triển. Điều này cũng tránh chi phí lint không cần thiết khi bắt đầu máy chủ dev.

3. Chạy ESLint như một phần của lệnh build production, để bạn có được phản hồi linter đầy đủ trước khi gửi lên production.

4. (Tùy chọn) Cấu hình các công cụ như [lint-staged](https://github.com/okonet/lint-staged) để tự động lint các file đã sửa đổi trên git commit.

## Định dạng {#formatting}

- Tiện ích [Volar](https://github.com/johnsoncodehk/volar) trên VSCode cung cấp định dạng cho Vue SFCs ngay khi bạn viết mã.

- Một phương án thay thế khác đó là [Prettier](https://prettier.io/) - một công cụ định dạng mã tự hỗ trợ Vue SFCs.

## Tích hợp khối SFC tuỳ chỉnh {#sfc-custom-block-integrations}

Các khối tuỳ chỉnh được biên dịch thành các import trong cùng một file Vue SFC với những câu truy vấn khác nhau. Tùy thuộc vào công cụ build cụ thể, các import này sẽ được xử lý khác nhau:

- Nếu bạn đang sử dụng Vite, một plugin Vite tuỳ chỉnh nên được sử dụng để biến đổi các khối tuỳ chỉnh khớp thành JavaScript có thể thực thi. [Ví dụ](https://github.com/vitejs/vite-plugin-vue/tree/main/packages/plugin-vue#example-for-transforming-custom-blocks)

- Nếu bạn đang sử dụng Vue CLI hoặc webpack thuần, một webpack loader nên được cấu hình để biến đổi các khối tuỳ chỉnh khớp. [Ví dụ](https://vue-loader.vuejs.org/guide/custom-blocks.html)

## Các package cấp thấp {#lower-level-packages}

### `@vue/compiler-sfc` {#vue-compiler-sfc}

- [Docs](https://github.com/vuejs/core/tree/main/packages/compiler-sfc)

Package này là một phần của Vue core monorepo và luôn được xuất bản với cùng một phiên bản như package chính `vue`. Nó được bao gồm như là một dependency của package chính `vue` và được proxy dưới `vue/compiler-sfc` để bạn không cần phải cài đặt nó một cách riêng lẻ.

Bản thân package này cung cấp các tiện ích cấp thấp cho việc xử lý Vue SFCs và chỉ dành cho các tác giả công cụ cần hỗ trợ Vue SFCs trong các công cụ tùy chỉnh.

:::tip
Luôn luôn ưu tiên sử dụng package này thông qua `vue/compiler-sfc` deep import vì điều này đảm bảo phiên bản của nó được đồng bộ với runtime Vue.
:::

### `@vitejs/plugin-vue` {#vitejs-plugin-vue}

- [Docs](https://github.com/vitejs/vite-plugin-vue/tree/main/packages/plugin-vue)

Plugin chính thức cung cấp hỗ trợ Vue SFC cho Vite.

### `vue-loader` {#vue-loader}

- [Docs](https://vue-loader.vuejs.org/)

Loader chính thức cung cấp hỗ trợ Vue SFC cho webpack. Nếu bạn đang sử dụng Vue CLI, hãy xem [tài liệu về việc sửa đổi các tùy chọn `vue-loader` trong Vue CLI](https://cli.vuejs.org/guide/webpack.html#modifying-options-of-a-loader).

## Những sân chơi Online khác {#other-online-playgrounds}

- [Sân chơi VueUse](https://play.vueuse.org)
- [Vue + Vite trên Repl.it](https://replit.com/@templates/VueJS-with-Vite)
- [Vue trên CodeSandbox](https://codesandbox.io/s/vue-3)
- [Vue trên Codepen](https://codepen.io/pen/editor/vue)
- [Vue trên Components.studio](https://components.studio/create/vue3)
- [Vue trên WebComponents.dev](https://webcomponents.dev/create/cevue)

<!-- TODO ## Backend Framework Integrations -->
