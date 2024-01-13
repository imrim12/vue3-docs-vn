# Routing {#routing}

## Client-Side vs. Server-Side Routing {#client-side-vs-server-side-routing}

Điều hướng trên phía máy chủ có nghĩa là máy chủ gửi phản hồi dựa trên đường dẫn URL mà người dùng đang truy cập. Khi chúng ta nhấp vào một liên kết trong một ứng dụng web được phân phối trên máy chủ truyền thống, trình duyệt nhận phản hồi HTML từ máy chủ và tải lại toàn bộ trang với HTML mới.

Trong [Single-Page Application](https://developer.mozilla.org/en-US/docs/Glossary/SPA) (SPA), tuy nhiên, JavaScript phía máy khách có thể chặn điều hướng, tự động hoá việc lấy dữ liệu mới và cập nhật trang hiện tại mà không cần tải lại toàn bộ trang. Điều này thường dẫn đến một trải nghiệm người dùng nhanh hơn, đặc biệt là đối với các trường hợp sử dụng giống như các "ứng dụng" thực tế, nơi người dùng được mong đợi thực hiện nhiều tương tác trong một khoảng thời gian dài.

Trong SPA như vậy, "điều hướng" được thực hiện trên phía máy khách, trong trình duyệt. Một bộ định tuyến phía máy khách chịu trách nhiệm quản lý chế độ xem được hiển thị của ứng dụng bằng cách sử dụng các API trình duyệt như [History API](https://developer.mozilla.org/en-US/docs/Web/API/History) hoặc [sự kiện `hashchange`](https://developer.mozilla.org/en-US/docs/Web/API/Window/hashchange).

## Official Router {#official-router}

<!-- TODO update links -->
<div>
  <VueSchoolLink href="https://vueschool.io/courses/vue-router-4-for-everyone" title="Free Vue Router Course">
    Watch a Free Video Course on Vue School
  </VueSchoolLink>
</div>

Vue được thiết kế để dễ dàng xây dựng các ứng dụng SPA. Đối với hầu hết các SPA, nên sử dụng thư viện chính thức [Vue Router](https://github.com/vuejs/router). Để biết thêm chi tiết, hãy xem [tài liệu](https://router.vuejs.org/) của Vue Router.

## Thiết lập điều hướng đơn giản từ con số không {#simple-routing-from-scratch}

Nếu bạn chỉ cần điều hướng rất đơn giản và không muốn liên quan đến một thư viện định tuyến đầy đủ tính năng, bạn có thể làm như vậy với [Các thành phần động](/guide/essentials/component-basics#dynamic-components) và cập nhật trạng thái thành phần hiện tại bằng cách lắng nghe các sự kiện [`hashchange`](https://developer.mozilla.org/en-US/docs/Web/API/Window/hashchange_event) của trình duyệt hoặc sử dụng [History API](https://developer.mozilla.org/en-US/docs/Web/API/History).

Dưới đây là một ví dụ về điều hướng đơn giản sử dụng các thành phần động:

<div class="composition-api">

```vue
<script setup>
import { ref, computed } from 'vue'
import Home from './Home.vue'
import About from './About.vue'
import NotFound from './NotFound.vue'

const routes = {
  '/': Home,
  '/about': About
}

const currentPath = ref(window.location.hash)

window.addEventListener('hashchange', () => {
  currentPath.value = window.location.hash
})

const currentView = computed(() => {
  return routes[currentPath.value.slice(1) || '/'] || NotFound
})
</script>

<template>
  <a href="#/">Home</a> |
  <a href="#/about">About</a> |
  <a href="#/non-existent-path">Broken Link</a>
  <component :is="currentView" />
</template>
```

[Thử trong Playground](https://play.vuejs.org/#eNptUk1vgkAQ/SsTegAThZp4MmhikzY9mKanXkoPWxjLRpgly6JN1P/eWb5Eywlm572ZN2/m5GyKwj9U6CydsIy1LAyUaKpiHZHMC6UNnEDjbgqxyovKYAIX2GmVg8sktwe9qhzbdz+wga15TW++VWX6fB3dAt6UeVEVJT2me2hhEcWKSgOamVjCCk4RAbiBu6xbT5tI2ML8VDeI6HLlxZXWSOZdmJTJPJB3lJSoo5+pWBipyE9FmU4soU2IJHk+MGUrS4OE2nMtIk4F/aA7BW8Cq3WjYlDbP4isQu4wVp0F1Q1uFH1IPDK+c9cb1NW8B03tyJ//uvhlJmP05hM4n60TX/bb2db0CoNmpbxMDgzmRSYMcgQQCkjZhlXkPASRs7YmhoFYw/k+WXvKiNrTcQgpmuFv7ZOZFSyQ4U9a7ZFgK2lvSTXFDqmIQbCUJTMHFkQOBAwKg16kM3W6O7K3eSs+nbeK+eee1V/XKK0dY4Q3vLhR6uJxMUK8/AFKaB6k)

</div>

<div class="options-api">

```vue
<script>
import Home from './Home.vue'
import About from './About.vue'
import NotFound from './NotFound.vue'

const routes = {
  '/': Home,
  '/about': About
}

export default {
  data() {
    return {
      currentPath: window.location.hash
    }
  },
  computed: {
    currentView() {
      return routes[this.currentPath.slice(1) || '/'] || NotFound
    }
  },
  mounted() {
    window.addEventListener('hashchange', () => {
		  this.currentPath = window.location.hash
		})
  }
}
</script>

<template>
  <a href="#/">Home</a> |
  <a href="#/about">About</a> |
  <a href="#/non-existent-path">Broken Link</a>
  <component :is="currentView" />
</template>
```

[Thử trong Playground](https://play.vuejs.org/#eNptUstO6zAQ/ZVR7iKtVJKLxCpKK3Gli1ggxIoNZmGSKbFoxpEzoUi0/87YeVBKNonHPmfOmcdndN00yXuHURblbeFMwxtFpm6sY7i1NcLW2RriJPWBB8bT8/WL7Xh6D9FPwL3lG9tROWHGiwGmqLDUMjhhYgtr+FQEEKdxFqRXfaR9YrkKAoqOnocfQaDEre523PNKzXqx7M8ADrlzNEYAReccEj9orjLYGyrtPtnZQrOxlFS6rXqgZJdPUC5s3YivMhuTDCkeDe6/dSalvognrkybnIgl7c4UuLhcwuHgS3v2/7EPvzRruRXJ7/SDU12W/98l451pGQndIvaWi0rTK8YrEPx64ymKFQOce5DOzlfs4cdlkA+NzdNpBSRgrJudZpQIINdQOdyuVfQnVdHGzydP9QYO549hXIII45qHkKUL/Ail8EUjBgX+z9k3JLgz9OZJgeInYElAkJlWmCcDUBGkAsrTyWS0isYV9bv803x1OTiWwzlrWtxZ2lDGDO90mWepV3+vZojHL3QQKQE=)

</div>
