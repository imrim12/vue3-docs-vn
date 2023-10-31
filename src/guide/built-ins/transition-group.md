<script setup>
import ListBasic from './transition-demos/ListBasic.vue'
import ListMove from './transition-demos/ListMove.vue'
import ListStagger from './transition-demos/ListStagger.vue'
</script>

# TransitionGroup {#transitiongroup}

`<TransitionGroup>` là một component tích hợp sẵn được thiết kế để tạo hiệu ứng cho việc chèn, xóa và thay đổi thứ tự của các phần tử hoặc component được render trong một danh sách.

## Sự khác biệt đối với `<Transition>` {#differences-from-transition}

`<TransitionGroup>` hỗ trợ các props, CSS transition class và JavaScript hook listener tương tự như `<Transition>`, với những khác biệt sau:

- Theo mặc định, nó không render một wrapper element. Nhưng bạn có thể chỉ định một element để render với prop `tag`.

- [Transition modes](./transition#transition-modes) không khả dụng, vì chúng ta không còn thay đổi giữa các element độc lập.

- Các element trong danh sách **luôn phải** có một `key` duy nhất.

- CSS transition class sẽ được áp dụng cho các element trong danh sách, **không** phải cho group / container.

:::tip
Khi sử dụng trong [in-DOM templates](/guide/essentials/component-basics#in-dom-template-parsing-caveats), `<TransitionGroup>` nên được tham chiếu như `<transition-group>`.
:::

## Enter / Leave Transitions {#enter-leave-transitions}

Dưới đây là một ví dụ về việc áp dụng enter / leave transition vào một danh sách `v-for` sử dụng `<TransitionGroup>`:

```vue-html
<TransitionGroup name="list" tag="ul">
  <li v-for="item in items" :key="item">
    {{ item }}
  </li>
</TransitionGroup>
```

```css
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
```

<ListBasic />

## Move Transitions {#move-transitions}

Phần demo ở trên có một số khuyết điểm rõ ràng: khi một item được chèn hoặc xóa, các item xung quanh nó sẽ "nhảy" ngay lập tức vào vị trí mới thay vì di chuyển một cách mượt mà. Chúng ta có thể sửa chữa điều này bằng cách thêm một vài CSS rules:

```css{1,13-17}
.list-move, /* apply transition to moving elements */
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}

/* chắc chắn rằng các leaving item được loại bỏ khỏi layout flow
   để tính toán animation di chuyển một cách chính xác. */
.list-leave-active {
  position: absolute;
}
```

Bây giờ các item xung quanh sẽ di chuyển một cách mượt mà để điều chỉnh vị trí của item bị chèn hoặc xóa:

<ListMove />

[Ví dụ đầy đủ](/examples/#list-transition)

## Staggering List Transitions {#staggering-list-transitions}

Thông qua việc giao tiếp với JavaScript transitions thông qua các data attribute, chúng ta cũng có thể stagger transitions trong một danh sách. Đầu tiên, chúng ta render index của một item như một data attribute trên DOM element:

```vue-html{11}
<TransitionGroup
  tag="ul"
  :css="false"
  @before-enter="onBeforeEnter"
  @enter="onEnter"
  @leave="onLeave"
>
  <li
    v-for="(item, index) in computedList"
    :key="item.msg"
    :data-index="index"
  >
    {{ item.msg }}
  </li>
</TransitionGroup>
```

Sau đó, trong các hook JavaScript, chúng ta animate element với một delay dựa trên data attribute. Ví dụ này sử dụng [GreenSock library](https://greensock.com/) để thực hiện animation:

```js{5}
function onEnter(el, done) {
  gsap.to(el, {
    opacity: 1,
    height: '1.6em',
    delay: el.dataset.index * 0.15,
    onComplete: done
  })
}
```

<ListStagger />

<div class="composition-api">

[Ví dụ đầy đủ trên Playground](https://play.vuejs.org/#eNqlVMuu0zAQ/ZVRNklRm7QLWETtBW4FSFCxYkdYmGSSmjp28KNQVfl3xk7SFyvEponPGc+cOTPNOXrbdenRYZRHa1Nq3lkwaF33VEjedkpbOIPGeg6lajtnsYIeaq1aiOlSfAlqDOtG3L8SUchSSWNBcPrZwNdCAqVqTZND/KxdibBDjKGf3xIfWXngCNs9k4/Udu/KA3xWWnPz1zW0sOOP6CcnG3jv9ImIQn67SvrpUJ9IE/WVxPHsSkw97gbN0zFJZrB5grNPrskcLUNXac2FRZ0k3GIbIvxLSsVTq3bqF+otM5jMUi5L4So0SSicHplwOKOyfShdO1lariQo+Yy10vhO+qwoZkNFFKmxJ4Gp6ljJrRe+vMP3yJu910swNXqXcco1h0pJHDP6CZHEAAcAYMydwypYCDAkJRdX6Sts4xGtUDAKotIVs9Scpd4q/A0vYJmuXo5BSm7JOIEW81DVo77VR207ZEf8F23LB23T+X9VrbNh82nn6UAz7ASzSCeANZe0AnBctIqqbIoojLCIIBvoL5pJw31DH7Ry3VDKsoYinSii4ZyXxhBQM2Fwwt58D7NeoB8QkXfDvwRd2XtceOsCHkwc8KCINAk+vADJppQUFjZ0DsGVGT3uFn1KSjoPeKLoaYtvCO/rIlz3vH9O5FiU/nXny/pDT6YGKZngg0/Zg1GErrMbp6N5NHxJFi3N/4dRkj5IYf5ULxCmiPJpI4rIr4kHimhvbWfyLHOyOzQpNZZ57jXNy4nRGFLTR/0fWBqe7w==)

</div>
<div class="options-api">

[Ví dụ đầy đủ trên Playground](https://play.vuejs.org/#eNqtVE2P0zAQ/SujXNqgNmkPcIjaBbYCJKg4cSMcTDJNTB07+KNsVfW/M3aabNpyQltViT1vPPP8Zian6H3bJgeHURatTKF5ax9yyZtWaQuVYS3stGpg4peTXOayUNJYEJwea/ieS4ATNKbKYPKoXYGwRZzAeTYGPrNizxE2NZO30KZ2xR6+Kq25uTuGFrb81vrFyQo+On0kIJc/PCV8CmxL3DEnLJy8e8ksm8bdGkCjdVr2O4DfDvWRgtGN/JYC0SOkKVTTOotl1jv3hi3d+DngENILkey4sKinU26xiWH9AH6REN/Eqq36g3rDDE7jhMtCuBLN1NbcJIFEHN9RaNDWqjQDAyUfcac0fpA+CYoRCRSJsUeBiWpZwe2RSrK4w2rkVe2rdYG6LD5uH3EGpZI4iuurTdwDNBjpRJclg+UlhP914UnMZfIGm8kIKVEwciYivhoGLQlQ4hO8gkWyfD1yVHJDKgu0mAUmPXLuxRkYb5Ed8H8YL/7BeGx7Oa6hkLmk/yodBoo21BKtYBZpB7DikroKDvNGUeZ1HoVmyCNIO/ibZtJwy5X8pJVru9CWVeTpRB51+6wwhgw7Jgz2tnc/Q6/M0ZeWwKvmGZye0Wu78PIGexC6swdGxEnw/q6HOYUkt9DwMwhKxfS6GpY+KPHc45G8+6EYAV7reTjucf/uwUtSmvvTME1wDuISlVTwTqf0RiiyrtKR0tEs6r5l84b645dRkr5zoT8oXwBMHg2Tlke+jbwhj2prW5OlqZPtvkroYqnH3lK9nLgI46scnf8Cn22kBA==)

</div>

---

**Xem thêm**

- [Tham khảo `<TransitionGroup>` API](/api/built-in-components#transitiongroup)
