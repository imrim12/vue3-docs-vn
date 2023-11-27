# Xử lý sự kiện {#event-handling}

<div class="options-api">
  <VueSchoolLink href="https://vueschool.io/lessons/user-events-in-vue-3" title="Free Vue.js Events Lesson"/>
</div>

<div class="composition-api">
  <VueSchoolLink href="https://vueschool.io/lessons/vue-fundamentals-capi-user-events-in-vue-3" title="Free Vue.js Events Lesson"/>
</div>

## Lắng nghe sự kiện {#listening-to-events}

Chúng ta có thể sử dụng directive `v-on` (viết tắt là `@`) để lắng nghe các sự kiện DOM và chạy một số JavaScript khi chúng được kích hoạt. Cú pháp sẽ là `v-on:click="handler"` hoặc với cú pháp rút gọn, `@click="handler"`.

Giá trị của handler có thể là một trong các giá trị sau:

1. **Inline handlers:** Inline JavaScript để thực thi khi sự kiện được kích hoạt (tương tự như thuộc tính `onclick` thuần).

2. **Method handlers:** Tên của một phương thức được định nghĩa trong component.

## Inline Handler {#inline-handlers}

Inline handlers thường được sử dụng trong các trường hợp đơn giản, ví dụ:

<div class="composition-api">

```js
const count = ref(0)
```

</div>
<div class="options-api">

```js
data() {
  return {
    count: 0
  }
}
```

</div>

```vue-html
<button @click="count++">Thêm 1</button>
<p>Count hiện tại là: {{ count }}</p>
```

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNo9jssKgzAURH/lko0tgrbbEqX+Q5fZaLxiqHmQ3LgJ+fdqFZcD58xMYp1z1RqRvRgP0itHEJCia4VR2llPkMDjBBkmbzUUG1oII4y0JhBIGw2hh2Znbo+7MLw+WjZ/C4TaLT3hnogPkcgaeMtFyW8j2GmXpWBtN47w5PWBHLhrPzPCKfWDXRHmPsCAaOBfgSOkdH3IGUhpDBWv9/e8vsZZ/gFFhFJN)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNo9jcEKgzAQRH9lyKlF0PYqqdR/6DGXaLYo1RjiRgrivzepIizLzu7sm1XUzuVLIFEKObe+d1wpS183eYahtw4DY1UWMJr15ZpmxYAnDt7uF0BxOwXL5Evc0kbxlmyxxZLFyY2CaXSDZkqKZROYJ4tnO/Tt56HEgckyJaraGNxlsVt2u6teHeF40s20EDo9oyGy+CPIYF1xULBt4H6kOZeFiwBZnOFi+wH0B1hk)

</div>

## Method Handlers {#method-handlers}

Logic cho các event handler sẽ phức tạp hơn thế này, và có thể không khả thi với inline handlers. Đó là lý do tại sao `v-on` cũng có thể chấp nhận tên hoặc đường dẫn của một phương thức component mà bạn muốn gọi.

Ví dụ:

<div class="composition-api">

```js
const name = ref('Vue.js')

function greet(event) {
  alert(`Xin chào ${name.value}!`)
  // `event` là DOM event thuần
  if (event) {
    alert(event.target.tagName)
  }
}
```

</div>
<div class="options-api">

```js
data() {
  return {
    name: 'Vue.js'
  }
},
methods: {
  greet(event) {
    // `this` trỏ đến instance đang hoạt động hiện tại
    alert(`Xin chào ${this.name}!`)
    // `event` là DOM event thuần
    if (event) {
      alert(event.target.tagName)
    }
  }
}
```

</div>

```vue-html
<!-- `greet` là tên của phương thức được định nghĩa ở trên -->
<button @click="greet">Greet</button>
```

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNpVj0FLxDAQhf/KMwjtXtq7dBcFQS/qzVMOrWFao2kSkkkvpf/dJIuCEBgm771vZnbx4H23JRJ3YogqaM+IxMlfpNWrd4GxI9CMA3NwK5psbaSVVjkbGXZaCediaJv3RN1XbE5FnZNVrJ3FEoi4pY0sn7BLC0yGArfjMxnjcLsXQrdNJtFxM+Ys0PcYa2CEjuBPylNYb4THtxdUobj0jH/YX3D963gKC5WyvGZ+xR7S5jf01yPzeblhWr2ZmErHw0dizivfK6PV91mKursUl6dSh/4qZ+vQ/+XE8QODonDi)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNplUE1LxDAQ/StjEbYL0t5LXRQEvag3Tz00prNtNE1CMilC6X83SUkRhJDJfLz3Jm8tHo2pFo9FU7SOW2Ho0in8MdoSDHhlXhKsnQIYGLHyvL8BLJK3KmcAis3YwOnDY/XlTnt1i2G7i/eMNOnBNRkwWkQqcUFFByVAXUNPk3A9COXEgBkGRgtFDkgDTQjcWxuAwDiJBeMsMcUxszCJlsr+BaXUcLtGwiqut930579KST1IBd5Aqlgie3p/hdTIk+IK//bMGqleEbMjxjC+BZVDIv0+m9CpcNr6MDgkhLORjDBm1H56Iq3ggUvBv++7IhnUFZfnGNt6b4fRtj5wxfYL9p+Sjw==)

</div>

Method handler tự động nhận đối số là DOM Event object thuần đã kích hoạt nó - trong ví dụ trên, chúng ta có thể truy cập element đang kích hoạt sự kiện thông qua `event.target.tagName`.

<div class="composition-api">

Xem thêm: [Ép kiểu cho Event Handler](/guide/typescript/composition-api#typing-event-handlers) <sup class="vt-badge ts" />

</div>
<div class="options-api">

Xem thêm: [Ép kiểu cho Event Handler](/guide/typescript/options-api#typing-event-handlers) <sup class="vt-badge ts" />

</div>

### Phát hiện Method và Inline {#method-vs-inline-detection}

Trình biên dịch template phát hiện các method handler bằng cách kiểm tra xem chuỗi giá trị `v-on` có phải là một JavaScript identifier hay một đường dẫn truy cập thuộc tính. Ví dụ, `foo`, `foo.bar` và `foo['bar']` được coi là method handler, trong khi `foo()` và `count++` được coi là inline handler.

## Gọi Method trong Inline Handler {#calling-methods-in-inline-handlers}

Thay vì gọi trực tiếp một method name, chúng ta cũng có thể gọi method trong một inline handler. Điều này cho phép chúng ta truyền vào các hàm với các đối số có thể tuỳ biến thay vì event thuần:

<div class="composition-api">

```js
function say(message) {
  alert(message)
}
```

</div>
<div class="options-api">

```js
methods: {
  say(message) {
    alert(message)
  }
}
```

</div>

```vue-html
<button @click="say('hello')">Nói xin chào!</button>
<button @click="say('bye')">Nói tạm biệt!</button>
```

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNp9jTEOwjAMRa8SeSld6I5CBWdg9ZJGBiJSN2ocpKjq3UmpFDGx+Vn//b/ANYTjOxGcQEc7uyAqkqTQI98TW3ETq2jyYaQYzYNatSArZTzNUn/IK7Ludr2IBYTG4I3QRqKHJFJ6LtY7+zojbIXNk7yfmhahv5msvqS7PfnHGjJVp9w/hu7qKKwfEd1NSg==)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNptjUEKwjAQRa8yZFO7sfsSi57B7WzGdjTBtA3NVC2ldzehEFwIw8D7vM9f1cX742tmVSsd2sl6aXDgjx8ngY7vNDuBFQeAnsWMXagToQAEWg49h0APLncDAIUcT5LzlKJsqRBfPF3ljQjCvXcknEj0bRYZBzi3zrbPE6o0UBhblKiaKy1grK52J/oA//23IcmNBD8dXeVBtX0BF0pXsg==)

</div>

## Truy cập đối số Event trong Inline Handler {#accessing-event-argument-in-inline-handlers}

Đôi khi chúng ta cũng cần truy cập event object thuần của DOM trong inline handler. Chúng ta có thể truyền nó vào một method sử dụng biến đặc biệt `$event`, hoặc sử dụng một arrow function inline:

```vue-html
<!-- sử dụng biến đặc biệt $event -->
<button @click="warn('Form tạm thời vẫn chưa submit được.', $event)">
  Submit
</button>

<!-- sử dụng arrow function inline -->
<button @click="(event) => warn('Form tạm thời vẫn chưa submit được.', event)">
  Submit
</button>
```

<div class="composition-api">

```js
function warn(message, event) {
  // bây giờ chúng ta đã có thể truy cập vào biến event thuần
  if (event) {
    event.preventDefault()
  }
  alert(message)
}
```

</div>
<div class="options-api">

```js
methods: {
  warn(message, event) {
    // bây giờ chúng ta đã có thể truy cập vào biến event thuần
    if (event) {
      event.preventDefault()
    }
    alert(message)
  }
}
```

</div>

## Event Modifiers {#event-modifiers}

Việc gọi `event.preventDefault()` hoặc `event.stopPropagation()` trong event handler rất phổ biến. Mặc dù chúng ta có thể làm điều này dễ dàng trong method, nhưng nó sẽ tốt hơn nếu method chỉ đơn giản là logic xử lý dữ liệu thay vì phải xử lý chi tiết event DOM.

Để giải quyết vấn đề này, Vue cung cấp **event modifiers** cho `v-on`. Nhớ lại rằng modifiers là các hậu tố của directive được đánh dấu bằng dấu chấm.

- `.stop`
- `.prevent`
- `.self`
- `.capture`
- `.once`
- `.passive`

```vue-html
<!-- sự kiện click sẽ không lan truyền nữa -->
<a @click.stop="doThis"></a>

<!-- sự kiện submit sẽ không reload lại trang -->
<form @submit.prevent="onSubmit"></form>

<!-- modifier có thể được xếp chồng lên nhau -->
<a @click.stop.prevent="doThat"></a>

<!-- chỉ dùng modifier -->
<form @submit.prevent></form>

<!-- chỉ kích hoạt handler nếu event.target là chính element đó -->
<!-- mà không phải là element con -->
<div @click.self="doThat">...</div>
```

::: tip
Thứ tự modifier rất quan trọng vì code tương ứng được tạo ra theo thứ tự đó. Do đó sử dụng `@click.prevent.self` sẽ ngăn **hành động mặc định của click trên element đó và các element con của nó**, trong khi `@click.self.prevent` chỉ ngăn hành động mặc định của click trên element đó.
:::

`.capture`, `.once`, và `.passive` modifiers tương tự như [options của phương thức `addEventListener` thuần](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#options):

```vue-html
<!-- sử dụng capture mode khi thêm event listener -->
<!-- ví dụ một event nhắm đến một element con được xử lý ở đây trước khi được xử lý bởi element đó -->
<div @click.capture="doThis">...</div>

<!-- sự kiện click chỉ có thể kích hoạt 1 lần duy nhất -->
<a @click.once="doThis"></a>

<!-- hành động mặc định của sự kiện scroll sẽ được thực hiện ngay lập tức -->
<!-- thay vì phải đợi cho `onScroll` hoàn thành -->
<!-- trong trường hợp nó chứa `event.preventDefault()` -->
<div @scroll.passive="onScroll">...</div>
```

`.passive` modifier thường được sử dụng với touch event listener để [cải thiện hiệu năng trên các thiết bị di động](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#improving_scrolling_performance_with_passive_listeners).

::: tip
Không sử dụng `.passive` và `.prevent` cùng nhau, vì `.passive` đã chỉ ra cho trình duyệt rằng bạn **không** muốn ngăn hành động mặc định của event, và bạn sẽ thấy một cảnh báo từ trình duyệt nếu làm như vậy.
:::

## Key Modifier {#key-modifiers}

Khi lắng nghe các sự kiện phím, chúng ta thường cần kiểm tra các phím cụ thể. Vue cho phép thêm key modifiers cho `v-on` hoặc `@` khi lắng nghe các sự kiện bàn phím:

```vue-html
<!-- chỉ gọi `submit` khi phím `Enter` được nhấn -->
<input @keyup.enter="submit" />
```

Bạn có thể sử dụng bất kỳ tên key nào được hỗ trợ thông qua [`KeyboardEvent.key`](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key/Key_Values) như modifier bằng cách chuyển chúng thành kebab-case.

```vue-html
<input @keyup.page-down="onPageDown" />
```

Trong ví dụ trên, handler chỉ được gọi khi `$event.key` là `'PageDown'`.

### Bí danh cho Phím {#key-aliases}

Vue cung cấp sẵn các bí danh cho các key phổ biến nhất:

- `.enter`
- `.tab`
- `.delete` (bắt phím "Delete" và "Backspace")
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

### Modifiers cho Phím hệ thống {#system-modifier-keys}

Bạn có thể sử dụng các modifier sau để kích hoạt các event listener cho chuột hoặc bàn phím chỉ khi các modifier key tương ứng được nhấn:

- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

::: tip Lưu ý
Đối với bàn phím Macintosh, meta là phím command (⌘). Đối với bàn phím Windows, meta là phím Windows (⊞). Đối với bàn phím Sun Microsystems, meta được đánh dấu là một hình kim cương đặc (◆). Đối với một số bàn phím, đặc biệt là bàn phím MIT và Lisp machine và các bản kế nhiệm, chẳng hạn như bàn phím Knight, bàn phím space-cadet, meta được đánh dấu là “META”. Đối với bàn phím Symbolics, meta được đánh dấu là “META” hoặc “Meta”.
:::

Ví dụ:

```vue-html
<!-- Alt + Enter -->
<input @keyup.alt.enter="clear" />

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```

::: tip
Lưu ý rằng các modifier key khác với các key thông thường và khi sử dụng với `keyup` events, chúng phải được nhấn khi event được phát ra. Nói cách khác, `keyup.ctrl` chỉ sẽ kích hoạt nếu bạn nhả phím khi đang giữ phím `ctrl`. Nó sẽ không kích hoạt nếu bạn nhả phím `ctrl` mà không nhấn phím nào khác.
:::

### `.exact` Modifier {#exact-modifier}

`.exact` modifier cho phép kiểm soát sự kết hợp chính xác của các modifier hệ thống cần thiết để kích hoạt một event.

```vue-html
<!-- button này sẽ kích hoạt ngay cả khi Alt hoặc Shift được nhấn -->
<button @click.ctrl="onClick">A</button>

<!-- button này sẽ chỉ kích hoạt khi Ctrl và không có phím nào khác được nhấn -->
<button @click.ctrl.exact="onCtrlClick">A</button>

<!-- button này sẽ chỉ kích hoạt khi không có modifier hệ thống nào được nhấn -->
<button @click.exact="onClick">A</button>
```

## Modifier cho các phím chuột {#mouse-button-modifiers}

- `.left`
- `.right`
- `.middle`

Các modifier này hạn chế handler chỉ xử lý các sự kiện được kích hoạt bởi một phím chuột cụ thể. 
