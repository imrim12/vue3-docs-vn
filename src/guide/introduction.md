---
footer: false
---

# Giới thiệu {#introduction}

:::info Bạn đang đọc tài liệu dành cho Vue 3!

- Vue 2 sẽ ngừng hỗ trợ vào ngày 31 tháng 12 năm 2023. Tìm hiểu thêm về [Vue 2 Extended LTS](https://v2.vuejs.org/lts/).
- Tài liệu Vue 2 đã được chuyển sang [v2.vuejs.org](https://v2.vuejs.org/).
- Nâng cấp từ Vue 2? Hãy xem [Hướng dẫn migration](https://v3-migration.vuejs.org/).
  :::

<style src="@theme/styles/vue-mastery.css"></style>
<div class="vue-mastery-link">
  <a href="https://www.vuemastery.com/courses/" target="_blank">
    <div class="banner-wrapper">
      <img class="banner" alt="Vue Mastery banner" width="96px" height="56px" src="https://storage.googleapis.com/vue-mastery.appspot.com/flamelink/media/vuemastery-graphical-link-96x56.png" />
    </div>
    <p class="description">Học Vue với các video hướng dẫn trên <span>VueMastery.com</span></p>
    <div class="logo-wrapper">
        <img alt="Vue Mastery Logo" width="25px" src="https://storage.googleapis.com/vue-mastery.appspot.com/flamelink/media/vue-mastery-logo.png" />
    </div>
  </a>
</div>

## Vue là gì? {#what-is-vue}

Vue (phát âm /vjuː/, giống như **view**) là một framework JavaScript xây dựng giao diện người dùng. Vue được xây dựng dựa trên HTML, CSS và JavaScript chuẩn; cung cấp một mô hình lập trình dựa trên component và declarative giúp bạn phát triển giao diện người dùng hiệu quả, dù đơn giản hay phức tạp.

Dưới đây là một ví dụ nhỏ:

<div class="options-api">

```js
import { createApp } from 'vue'

createApp({
  data() {
    return {
      count: 0
    }
  }
}).mount('#app')
```

</div>
<div class="composition-api">

```js
import { createApp, ref } from 'vue'

createApp({
  setup() {
    return {
      count: ref(0)
    }
  }
}).mount('#app')
```

</div>

```vue-html
<div id="app">
  <button @click="count++">
    Count is: {{ count }}
  </button>
</div>
```

**Kết quả**

<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<div class="demo">
  <button @click="count++">
    Count is: {{ count }}
  </button>
</div>

Ví dụ trên thể hiện hai tính năng cốt lõi của Vue:

- **Declarative Rendering**: Vue mở rộng HTML chuẩn với cú pháp template cho phép chúng ta mô tả HTML dựa trên state của JavaScript.

- **Reactivity**: Vue tự động theo dõi các thay đổi state của JavaScript và cập nhật DOM một cách hiệu quả khi có thay đổi.

Bạn có thể có những thắc mắc - đừng lo lắng. Chúng tôi sẽ bao quát tất cả các chi tiết trong phần còn lại của tài liệu. Bây giờ, hãy đọc tiếp để có thể hiểu được những gì Vue cung cấp.

:::tip Yêu cầu tiên quyết
Phần còn lại của tài liệu giả định rằng bạn đã quen thuộc với HTML, CSS và JavaScript. Nếu bạn hoàn toàn mới với lập trình frontend, có lẽ việc nhảy ngay vào một framework từ bước đầu tiên không phải là ý tốt nhất - hãy nắm vững những kiến thức cơ bản và quay lại sau! Bạn có thể kiểm tra trình độ của mình với [tổng quan về JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript). Có kinh nghiệm trước với các framework khác sẽ có ích, nhưng không bắt buộc.
:::

## Một framework hiện đại {#the-progressive-framework}

Vue là một framework và hệ sinh thái bao gồm hầu hết các tính năng cần thiết trong lập trình frontend. Nhưng web rất đa dạng - những thứ chúng ta xây dựng trên web có thể khác nhau một cách đáng kể về hình thức và quy mô. Suy nghĩ về điều đó, Vue được thiết kế để linh hoạt và có thể áp dụng theo từng bước. Tùy thuộc vào trường hợp sử dụng, Vue có thể được sử dụng theo nhiều cách khác nhau:

- Tăng cường HTML tĩnh mà không cần bước build
- Nhúng như Web Components trên bất kỳ trang nào
- Single-Page Application (SPA)
- Fullstack / Server-Side Rendering (SSR)
- Jamstack / Static Site Generation (SSG)
- Mục tiêu là desktop, mobile, WebGL, và thậm chí là terminal

Nếu bạn thấy những khái niệm này khó hiểu, đừng lo lắng! Hướng dẫn này chỉ yêu cầu kiến thức cơ bản về HTML và JavaScript, bạn có thể theo dõi mà không cần phải là một chuyên gia về bất kỳ thứ gì.

Nếu bạn là một nhà phát triển có kinh nghiệm quan tâm đến cách tích hợp Vue vào stack của bạn, hoặc bạn tò mò về những thuật ngữ này có nghĩa là gì, chúng tôi sẽ thảo luận chi tiết hơn trong [Cách sử dụng Vue](/guide/extras/ways-of-using-vue).

Mặc dù linh hoạt, nhưng kiến thức cốt lõi về cách Vue hoạt động được mô tả trong tất cả các trường hợp sử dụng này. Ngay cả khi bạn chỉ là một người mới bắt đầu, kiến thức được học dần theo thời gian sẽ rất hữu ích khi bạn phát triển để đạt được nhiều mục tiêu tham vọng hơn trong tương lai. Nếu bạn là một người có kinh nghiệm, bạn có thể chọn cách tối ưu để tận dụng Vue dựa trên các vấn đề bạn đang cố gắng giải quyết, đồng thời vẫn giữ nguyên hiệu suất làm việc. Đây là lý do tại sao chúng tôi gọi Vue là "Framework hiện đại": đó là một framework có thể phát triển cùng bạn và thích nghi với nhu cầu của bạn.

## Single-File Components {#single-file-components}

Trong hầu hết các dự án Vue có sử dụng build tool, chúng ta sẽ viết Vue components bằng một định dạng file giống HTML gọi là **Single-File Component** (còn được gọi là file `*.vue`, viết tắt là **SFC**). Một Vue SFC, như tên gọi của nó, đóng gói logic của component (JavaScript), template (HTML) và styles (CSS) trong một file duy nhất. Dưới đây là ví dụ trước, được viết dưới định dạng SFC:

<div class="options-api">

```vue
<script>
export default {
  data() {
    return {
      count: 0
    }
  }
}
</script>

<template>
  <button @click="count++">Count is: {{ count }}</button>
</template>

<style scoped>
button {
  font-weight: bold;
}
</style>
```

</div>
<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<template>
  <button @click="count++">Count is: {{ count }}</button>
</template>

<style scoped>
button {
  font-weight: bold;
}
</style>
```

</div>

SFC là một tính năng quan trọng của Vue và là cách được khuyến khích để viết Vue components **nếu** trường hợp sử dụng của bạn đòi hỏi một build setup. Bạn có thể tìm hiểu thêm về [cách sử dụng và lý do dùng SFC](/guide/scaling-up/sfc) trong phần được dành riêng cho nó - nhưng bây giờ, chỉ cần biết rằng Vue sẽ xử lý tất cả các thiết lập build tool cho bạn.

s## Loại API {#api-styles}

Vue components có thể được viết bằng hai loại API khác nhau: **Options API** và **Composition API**.

### Options API {#options-api}

Với Options API, chúng ta định nghĩa logic của một component bằng một object các options như `data`, `methods` và `mounted`. Các thuộc tính được định nghĩa bởi options được truy cập thông qua `this` bên trong các hàm, `this` trỏ tới thực thể của component:

```vue
<script>
export default {
  // Các thuộc tính được trả về từ data() trở thành state reactive
  // và sẽ được truy cập thông qua `this`.
  data() {
    return {
      count: 0
    }
  },

  // Methods là các hàm mutate state và trigger updates.
  // Chúng có thể được gắn vào như event handlers trong template.
  methods: {
    increment() {
      this.count++
    }
  },

  // Lifecycle hooks được gọi ở các giai đoạn khác nhau
  // trong vòng đời của một component.
  // Hàm này sẽ được gọi khi component được mounted.
  mounted() {
    console.log(`The initial count is ${this.count}.`)
  }
}
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
```

[Thử nó trong Playground](https://play.vuejs.org/#eNptkMFqxCAQhl9lkB522ZL0HNKlpa/Qo4e1ZpLIGhUdl5bgu9es2eSyIMio833zO7NP56pbRNawNkivHJ25wV9nPUGHvYiaYOYGoK7Bo5CkbgiBBOFy2AkSh2N5APmeojePCkDaaKiBt1KnZUuv3Ky0PppMsyYAjYJgigu0oEGYDsirYUAP0WULhqVrQhptF5qHQhnpcUJD+wyQaSpUd/Xp9NysVY/yT2qE0dprIS/vsds5Mg9mNVbaDofL94jZpUgJXUKBCvAy76ZUXY53CTd5tfX2k7kgnJzOCXIF0P5EImvgQ2olr++cbRE4O3+t6JxvXj0ptXVpye1tvbFY+ge/NJZt)

### Composition API {#composition-api}

Với Composition API, chúng ta định nghĩa logic của một component bằng cách import các hàm API. Trong SFCs, Composition API thường được sử dụng với [`<script setup>`](/api/sfc-script-setup). Thuộc tính `setup` là một gợi ý giúp Vue thực hiện các biến đổi compile-time cho phép chúng ta sử dụng Composition API với ít boilerplate hơn. Ví dụ, các import và các biến / hàm cấp cao được khai báo trong `<script setup>` có thể được sử dụng trực tiếp trong template.

Dưới đây là cùng một component, với cùng một template, nhưng sử dụng Composition API và `<script setup>` thay vì Options API:

```vue
<script setup>
import { ref, onMounted } from 'vue'

// reactive state
const count = ref(0)

// hàm mutate state và trigger updates
function increment() {
  count.value++
}

// lifecycle hooks
onMounted(() => {
  console.log(`The initial count is ${count.value}.`)
})
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
```

[Thử nó trong Playground](https://play.vuejs.org/#eNpNkMFqwzAQRH9lMYU4pNg9Bye09NxbjzrEVda2iLwS0spQjP69a+yYHnRYad7MaOfiw/tqSliciybqYDxDRE7+qsiM3gWGGQJ2r+DoyyVivEOGLrgRDkIdFCmqa1G0ms2EELllVKQdRQa9AHBZ+PLtuEm7RCKVd+ChZRjTQqwctHQHDqbvMUDyd7mKip4AGNIBRyQujzArgtW/mlqb8HRSlLcEazrUv9oiDM49xGGvXgp5uT5his5iZV1f3r4HFHvDprVbaxPhZf4XkKub/CDLaep1T7IhGRhHb6WoTADNT2KWpu/aGv24qGKvrIrr5+Z7hnneQnJu6hURvKl3ryL/ARrVkuI=)

### Chọn cái nào? {#which-to-choose}

Cả hai loại API đều có khả năng hoàn toàn đáp ứng các trường hợp sử dụng phổ biến. Chúng là các giao diện khác nhau được hỗ trợ bởi cùng một hệ thống cơ bản. Trên thực tế, Options API được triển khai trên Composition API! Các khái niệm và kiến thức cơ bản về Vue được thể hiện trong cả hai loại API.

Options API tập trung vào khái niệm của một "component instance" (`this` được xem là một ví dụ), điều này thường phù hợp hơn với mô hình tư duy dựa trên class cho người dùng có kinh nghiệm với các ngôn ngữ OOP. Nó cũng thân thiện hơn với người mới bắt đầu bằng cách trừu tượng hóa các chi tiết về reactivity và áp dụng tổ chức code thông qua các nhóm options.

Composition API tập trung vào việc khai báo các biến reactive state trực tiếp trong phạm vi của một hàm và kết hợp state từ nhiều hàm với nhau để xử lý tính phức tạp. Nó tự do hơn và yêu cầu người dùng hiểu cách reactivity hoạt động trong Vue để sử dụng hiệu quả. Đổi lại, tính linh hoạt của nó cho phép các pattern mạnh mẽ hơn để tổ chức và tái sử dụng logic.

Bạn có thể tìm hiểu thêm về sự so sánh giữa hai loại API và các lợi ích tiềm năng của Composition API trong [Composition API FAQ](/guide/extras/composition-api-faq).

Nếu bạn mới bắt đầu với Vue, đây là đề xuất chung của chúng tôi:

- Với mục đích học tập, hãy chọn loại API dễ hiểu nhất với bạn. Một lần nữa, hầu hết các khái niệm cốt lõi được thể hiện trong cả hai loại API. Bạn luôn có thể chọn loại API khác sau này.

- Với mục đích sử dụng trong production:

  - Sử dụng Options API nếu bạn không sử dụng build tools, hoặc dự định sử dụng Vue chủ yếu trong các trường hợp đơn giản, ví dụ như progressive enhancement.

  - Sử dụng Composition API + Single-File Components nếu bạn dự định xây dựng các ứng dụng đầy đủ với Vue.

Bạn không cần phải lo lắng về việc phải chọn một loại API trong giai đoạn học tập. Phần còn lại của tài liệu sẽ cung cấp các ví dụ code trong cả hai loại API, và bạn có thể chuyển đổi giữa chúng bất cứ lúc nào bằng cách sử dụng **Chuyển đổi API Preference** ở đầu của thanh bên trái.

## Vẫn còn thắc mắc? {#still-got-questions}

Hãy xem [FAQ](/about/faq).

## Chọn con đường học tập của bạn {#pick-your-learning-path}

Các nhà phát triển khác nhau có các phong cách học tập khác nhau. Hãy chọn một con đường học tập phù hợp với sở thích của bạn - mặc dù chúng tôi khuyến khích bạn nên xem qua tất cả nội dung, nếu có thể!

<div class="vt-box-container next-steps">
  <a class="vt-box" href="/tutorial/">
    <p class="next-steps-link">Thử tutorial</p>
    <p class="next-steps-caption">Dành cho những người thích học bằng cách thực hành.</p>
  </a>
  <a class="vt-box" href="/guide/quick-start.html">
    <p class="next-steps-link">Đọc hướng dẫn</p>
    <p class="next-steps-caption">Hướng dẫn sẽ trình bày mọi khía cạnh của framework một cách chi tiết.</p>
  </a>
  <a class="vt-box" href="/examples/">
    <p class="next-steps-link">Xem các ví dụ</p>
    <p class="next-steps-caption">Khám phá các ví dụ về các tính năng cốt lõi và các nhiệm vụ UI phổ biến.</p>
  </a>
</div>
