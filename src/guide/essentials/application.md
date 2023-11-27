# Tạo một ứng dụng Vue {#creating-a-vue-application}

## Đối tượng ứng dụng {#the-application-instance}

Mọi ứng dụng Vue đều bắt đầu bằng việc tạo một **đối tượng ứng dụng** mới với hàm [`createApp`](/api/application#createapp):

```js
import { createApp } from 'vue'

const app = createApp({
  /* các tuỳ chọn của component gốc */
})
```

## Component gốc {#the-root-component}

Object mà chúng ta truyền vào `createApp` thực sự là một component. Mọi ứng dụng đều yêu cầu một "component gốc" có thể chứa các component khác là con của nó.

Nếu như bạn đang sử dụng Single-File Components, chúng ta thường import component gốc từ một file khác:

```js
import { createApp } from 'vue'
// import component gốc App từ một single-file component.
import App from './App.vue'

const app = createApp(App)
```

Trong khi nhiều ví dụ trong hướng dẫn này chỉ cần một component duy nhất, hầu hết các ứng dụng thực tế được tổ chức thành một cây các component lồng nhau có thể tái sử dụng. Ví dụ, một ứng dụng Todo có thể có cây component như sau:

```
App (root component)
├─ TodoList
│  └─ TodoItem
│     ├─ TodoDeleteButton
│     └─ TodoEditButton
└─ TodoFooter
   ├─ TodoClearButton
   └─ TodoStatistics
```

Trong những phần sau của hướng dẫn, chúng ta sẽ thảo luận về cách định nghĩa và kết hợp nhiều component với nhau. Trước đó, chúng ta sẽ tập trung vào những gì xảy ra bên trong một component duy nhất.

## Lắp đặt App {#mounting-the-app}

Một đối tượng ứng dụng sẽ không hiển thị bất cứ thứ gì cho đến khi phương thức `.mount()` được gọi. Nó mong đợi một đối số "container", có thể là một phần tử DOM thực sự hoặc một chuỗi selector:

```html
<div id="app"></div>
```

```js
app.mount('#app')
```

Nội dung của component gốc sẽ được hiển thị bên trong phần tử container. Chính phần tử container không được coi là một phần của ứng dụng.

Phương thức `.mount()` nên luôn được gọi sau khi tất cả các cấu hình ứng dụng và đăng ký tài nguyên được thực hiện. Lưu ý rằng giá trị trả về của nó, khác với các phương thức đăng ký tài nguyên, là một đối tượng component gốc thay vì đối tượng ứng dụng.

### Component Template gốc trong DOM {#in-dom-root-component-template}

Template cho component gốc thường là một phần của component chính nó, nhưng cũng có thể được cung cấp riêng bằng cách viết trực tiếp vào container:

```html
<div id="app">
  <button @click="count++">{{ count }}</button>
</div>
```

```js
import { createApp } from 'vue'

const app = createApp({
  data() {
    return {
      count: 0
    }
  }
})

app.mount('#app')
```

Vue sẽ tự động sử dụng `innerHTML` của container làm template nếu component gốc không có tùy chọn `template`.

Template trong DOM thường được sử dụng trong các ứng dụng [sử dụng Vue mà không có bước xây dựng](/guide/quick-start.html#using-vue-from-cdn). Chúng cũng có thể được sử dụng kết hợp với các framework phía server, trong đó template gốc có thể được tạo ra động bởi server.

## Cấu hình App {#app-configurations}

Đối tượng ứng dụng cũng cung cấp một object `.config` cho phép chúng ta cấu hình một số tùy chọn cấp ứng dụng, ví dụ như định nghĩa một error handler cấp ứng dụng để bắt lỗi từ tất cả các component con:

```js
app.config.errorHandler = (err) => {
  /* xử lý error */
}
```

Đối tượng ứng dụng cũng cung cấp một số phương thức để đăng ký các tài nguyên cấp ứng dụng. Ví dụ, đăng ký một component:

```js
app.component('TodoDeleteButton', TodoDeleteButton)
```

Điều này khiến `TodoDeleteButton` khả dụng ở bất cứ đâu trong ứng dụng của chúng ta. Chúng ta sẽ thảo luận về đăng ký component và các loại tài nguyên khác trong các phần sau của hướng dẫn. Bạn cũng có thể duyệt qua danh sách đầy đủ các API của đối tượng ứng dụng trong [API reference](/api/application).

Hãy nhớ rằng tất cả các cấu hình ứng dụng phải được áp dụng trước app được lắp đặt!

## Đa đối tượng ứng dụng {#multiple-application-instances}

Bạn không chỉ bị giới hạn bởi một đối tượng ứng dụng duy nhất trên cùng một trang. API `createApp` cho phép nhiều ứng dụng Vue tồn tại trên cùng một trang, mỗi ứng dụng có phạm vi riêng cho cấu hình và tài nguyên toàn cục:

```js
const app1 = createApp({
  /* ... */
})
app1.mount('#container-1')

const app2 = createApp({
  /* ... */
})
app2.mount('#container-2')
```

Nếu bạn đang sử dụng Vue để tăng cường HTML được render trên server và chỉ cần Vue để điều khiển các phần cụ thể của một trang lớn, hãy tránh lắp đặt một đối tượng ứng dụng duy nhất trên toàn bộ trang. Thay vào đó, hãy tạo nhiều đối tượng ứng dụng nhỏ và lắp đặt chúng trên các phần tử mà chúng có trách nhiệm:
