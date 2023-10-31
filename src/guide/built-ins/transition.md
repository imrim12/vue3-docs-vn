<script setup>
import Basic from './transition-demos/Basic.vue'
import SlideFade from './transition-demos/SlideFade.vue'
import CssAnimation from './transition-demos/CssAnimation.vue'
import NestedTransitions from './transition-demos/NestedTransitions.vue'
import JsHooks from './transition-demos/JsHooks.vue'
import BetweenElements from './transition-demos/BetweenElements.vue'
import BetweenComponents from './transition-demos/BetweenComponents.vue'
</script>

# Transition {#transition}

Vue cung cấp hai component tích hợp sẵn có thể giúp làm việc với transition và animation trong phản hồi với việc thay đổi state:

- `<Transition>` cho việc áp dụng animation khi một phần tử được thêm vào hoặc xóa khỏi DOM.

- `<TransitionGroup>` cho việc áp dụng animation khi một phần tử được thêm, xóa, hoặc di chuyển trong `v-for`. Đây là một component đặc biệt được bàn tới trong [chương tiếp theo](/guide/built-ins/transition-group).

Bên cạnh hai component này, chúng ta cũng có thể áp dụng animation trong Vue bằng các kỹ thuật khác như toggle CSS class hoặc state-driven animations thông qua style bindings. Những kỹ thuật bổ sung này được bàn tới trong [Kỹ thuật Animation](/guide/extras/animation).

## `<Transition>` Component {#the-transition-component}

`<Transition>` là một component tích hợp sẵn: điều này có nghĩa là nó có sẵn trong mọi template component mà không cần phải đăng ký. Nó có thể được sử dụng để áp dụng animation vào các phần tử hoặc component được truyền vào thông qua slot mặc định của nó. Khi một phần tử trong `<Transition>` được thêm vào hoặc xóa khỏi DOM, điều gì sẽ xảy ra là:

- Render có điều kiện thông qua `v-if`
- Hiển thị có điều kiện thông qua `v-show`
- Toggle dynamic component thông qua `<component>`
- Thay đổi thuộc tính `key`

Dưới đây là một ví dụ cơ bản về cách sử dụng `<Transition>`:

```vue-html
<button @click="show = !show">Toggle</button>
<Transition>
  <p v-if="show">hello</p>
</Transition>
```

```css
/* chúng ta sẽ giải thích về những class này sau! */
.v-enter-active,
.v-leave-active {
  transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}
```

<Basic />

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNpVkEFuwyAQRa8yZZNWqu1sunFJ1N4hSzYUjRNUDAjGVJHluxcCipIV/OG/pxEr+/a+TwuykfGogvYEEWnxR2H17F0gWCHgBBtMwc2wy9WdsMIqZ2OuXtwfHErhlcKCb8LyoVoynwPh7I0kzAmA/yxEzsKXMlr9HgRr9Es5BTue3PlskA+1VpFTkDZq0i3niYfU6anRmbqgMY4PZeH8OjwBfHhYIMdIV1OuferQEoZOKtIJ328TgzJhm8BabHR3jeC8VJqusO8/IqCM+CnsVqR3V/mfRxO5amnkCPuK5B+6rcG2fydshks=)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNpVkMFuAiEQhl9lyqlNuouXXrZo2nfwuBeKs0qKQGBAjfHdZZfVrAmB+f/M/2WGK/v1vs0JWcdEVEF72vQWz94Fgh0OMhmCa28BdpLk+0etAQJSCvahAOLBnTqgkLA6t/EpVzmCP7lFEB69kYRFAYi/ROQs/Cij1f+6ZyMG1vA2vj3bbN1+b1Dw2lYj2yBt1KRnXRwPudHDnC6pAxrjBPe1n78EBF8MUGSkixnLNjdoCUMjFemMn5NjUGacnboqPVkdOC+Vpgus2q8IKCN+T+suWENwxyWJXKXMyQ5WNVJ+aBqD3e6VSYoi)

</div>

:::tip
`<Transition>` chỉ hỗ trợ một phần tử hoặc component duy nhất trong slot của nó. Nếu nội dung là một component, component đó cũng phải chỉ có một phần tử duy nhất làm root.
:::

Khi một element trong `<Transition>` được thêm vào hoặc xóa khỏi DOM, sẽ xảy ra những việc sau:

1. Vue sẽ tự động phát hiện xem phần tử đích có CSS transition hoặc animation được áp dụng hay không. Nếu có, một số [CSS transition classes](#transition-classes) sẽ được thêm vào/xóa khỏi phần tử đích tại thời điểm thích hợp.

2. Nếu như có những listeners cho [JavaScript hooks](#javascript-hooks), những hooks này sẽ được gọi tại thời điểm thích hợp.

3. Nếu không phát hiện bất kỳ CSS transitions/animation nào và cũng không có bất kỳ JavaScript hook nào được cung cấp, những thao tác DOM để thêm/xóa phần tử sẽ được thực hiện vào frame animation tiếp theo của trình duyệt.

## CSS-Based Transitions {#css-based-transitions}

### Transition Classes {#transition-classes}

Có tổng cộng sáu classes được áp dụng cho các transition enter / leave.

![Transition Diagram](./images/transition-classes.png)

<!-- https://www.figma.com/file/rlOv0ZKJFFNA9hYmzdZv3S/Transition-Classes -->

1. `v-enter-from`: Trạng thái bắt đầu của enter. Được thêm vào trước khi phần tử được gắn vào DOM, và bị gỡ bỏ trong một frame sau khi phần tử đã được thêm vào.

2. `v-enter-active`: Trạng thái hoạt động của enter. Được áp dụng trong suốt quá trình enter. Được thêm vào trước khi phần tử được gắn vào DOM, và bị gỡ bỏ khi transition/animation kết thúc. Class này có thể được sử dụng để định nghĩa thời gian, delay, và easing curve cho transition enter.

3. `v-enter-to`: Trạng thái kết thúc của enter. Được thêm vào một frame sau khi phần tử được gắn vào DOM (cùng thời điểm với việc `v-enter-from` bị gỡ bỏ), và bị gỡ bỏ khi transition/animation kết thúc.

4. `v-leave-from`: Trạng thái bắt đầu của leave. Được thêm vào ngay khi một transition leave được kích hoạt, và bị gỡ bỏ trong một frame sau khi phần tử đã được thêm vào.

5. `v-leave-active`: Trạng thái hoạt động của leave. Được áp dụng trong suốt quá trình leave. Được thêm vào ngay khi một transition leave được kích hoạt, và bị gỡ bỏ khi transition/animation kết thúc. Class này có thể được sử dụng để định nghĩa thời gian, delay, và easing curve cho transition leave.

6. `v-leave-to`: Trạng thái kết thúc của leave. Được thêm vào một frame sau khi một transition leave được kích hoạt (cùng thời điểm với việc `v-leave-from` bị gỡ bỏ), và bị gỡ bỏ khi transition/animation kết thúc.

`v-enter-active` và `v-leave-active` cho phép chúng ta định nghĩa chi tiết các [easing curves](https://developer.mozilla.org/en-US/docs/Web/CSS/easing-function) khác nhau cho transition enter và leave, như chúng ta sẽ thấy trong các phần tiếp theo.

### Named Transitions {#named-transitions}

Một transition có thể được đặt tên thông qua prop `name`:

```vue-html
<Transition name="fade">
  ...
</Transition>
```

Đối với một transition có tên, các transition classes của nó sẽ được thêm vào với tên của nó thay vì `v`. Ví dụ, class được áp dụng cho transition ở trên sẽ là `fade-enter-active` thay vì `v-enter-active`. CSS cho transition fade sẽ như sau:

```css
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
```

### CSS Transitions {#css-transitions}

`<Transition>` thường được sử dụng kết hợp với [native CSS transitions](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Transitions/Using_CSS_transitions), như ví dụ cơ bản ở trên. Thuộc tính CSS `transition` là một thuộc tính rút gọn cho phép chúng ta định nghĩa nhiều khía cạnh của transition, bao gồm các thuộc tính cần được animate, thời gian của transition, và [easing curves](https://developer.mozilla.org/en-US/docs/Web/CSS/easing-function).

Dưới đây là một ví dụ nâng cao hơn về cách transition nhiều thuộc tính, với các thời gian và easing curves khác nhau cho enter và leave:

```vue-html
<Transition name="slide-fade">
  <p v-if="show">hello</p>
</Transition>
```

```css
/*
  Enter và leave animations có thể 
  sử dụng các thời gian và easing curves khác nhau.
*/
.slide-fade-enter-active {
  transition: all 0.3s ease-out;
}

.slide-fade-leave-active {
  transition: all 0.8s cubic-bezier(1, 0.5, 0.8, 1);
}

.slide-fade-enter-from,
.slide-fade-leave-to {
  transform: translateX(20px);
  opacity: 0;
}
```

<SlideFade />

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNqFkc9uwjAMxl/F6wXQKIVNk1AX0HbZC4zDDr2E4EK0NIkStxtDvPviFQ0OSFzyx/m+n+34kL16P+lazMpMRBW0J4hIrV9WVjfeBYIDBKzhCHVwDQySdFDZyipnY5Lu3BcsWDCk0OKosqLoKcmfLoSNN5KQbyTWLZGz8KKMVp+LKju573ivsuXKbbcG4d3oDcI9vMkNiqL3JD+AWAVpoyadGFY2yATW5nVSJj9rkspDl+v6hE/hHRrjRMEdpdfiDEkBUVxWaEWkveHj5AzO0RKGXCrSHcKBIfSPKEEaA9PJYwSUEXPX0nNlj8y6RBiUHd5AzCOodq1VvsYfjWE4G6fgEy/zMcxG17B9ZTyX8bV85C5y1S40ZX/kdj+GD1P/zVQA56XStC9h2idJI/z7huz4CxoVvE4=)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNqFkc1uwjAMgF/F6wk0SmHTJNQFtF32AuOwQy+hdSFamkSJ08EQ776EbMAkJKTIf7I/O/Y+ezVm3HvMyoy52gpDi0rh1mhL0GDLvSTYVwqg4cQHw2QDWCRv1Z8H4Db6qwSyHlPkEFUQ4bHixA0OYWckJ4wesZUn0gpeainqz3mVRQzM4S7qKlss9XotEd6laBDu4Y03yIpUE+oB2NJy5QSJwFC8w0iIuXkbMkN9moUZ6HPR/uJDeINSalaYxCjOkBBgxeWEijnayWiOz+AcFaHNeU2ix7QCOiFK4FLCZPzoALnDXHt6Pq7hP0Ii7/EGYuag9itR5yv8FmgH01EIPkUxG8F0eA2bJmut7kbX+pG+6NVq28WTBTN+92PwMDHbSAXQhteCdiVMUpNwwuMassMP8kfAJQ==)

</div>

### CSS Animations {#css-animations}

[Native CSS animations](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Animations/Using_CSS_animations) được hỗ trợ tương tự như CSS transitions, với điểm khác biệt đó là `*-enter-from` class không được gỡ bỏ sau khi phần tử được thêm vào DOM. Thay vào đó, chúng sẽ được gỡ bỏ khi một sự kiện `animationend` được kích hoạt.

Đối với phần lớn CSS animations, chúng ta có thể đơn giản chỉ cần khai báo chúng trong các class `*-enter-active` và `*-leave-active`. Dưới đây là một ví dụ:

```vue-html
<Transition name="bounce">
  <p v-if="show" style="text-align: center;">
    Xin chào, đây là một vài đoạn text nảy!
  </p>
</Transition>
```

```css
.bounce-enter-active {
  animation: bounce-in 0.5s;
}
.bounce-leave-active {
  animation: bounce-in 0.5s reverse;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.25);
  }
  100% {
    transform: scale(1);
  }
}
```

<CssAnimation />

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNqNksGOgjAQhl9lJNmoBwRNvCAa97YP4JFLbQZsLG3TDqzG+O47BaOezCYkpfB9/0wHbsm3c4u+w6RIyiC9cgQBqXO7yqjWWU9wA4813KH2toUpo9PKVEZaExg92V/YRmBGvsN5ZcpsTGGfN4St04Iw7qg8dkTWwF5qJc/bKnnYk7hWye5gm0ZjmY0YKwDlwQsTFCnWjGiRpaPtjETG43smHPSpqh9pVQKBrjpyrfCNMilZV8Aqd5cNEF4oFVo1pgCJhtBvnjEAP6i1hRN6BBUg2BZhKHUdvMmjWhYHE9dXY/ygzN4PasqhB75djM2mQ7FUSFI9wi0GCJ6uiHYxVsFUGcgX67CpzP0lahQ9/k/kj9CjDzgG7M94rT1PLLxhQ0D+Na4AFI9QW98WEKTQOMvnLAOwDrD+wC0Xq/Ubusw/sU+QL/45hskk9z8Bddbn)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNqNUs2OwiAQfpWxySZ66I8mXioa97YP4LEXrNNKpEBg2tUY330pqOvJmBBgyPczP1yTb2OyocekTJirrTC0qRSejbYEB2x4LwmulQI4cOLTWbwDWKTeqkcE4I76twSyPcaX23j4zS+WP3V9QNgZyQnHiNi+J9IKtrUU9WldJaMMrGEynlWy2em2lcjyCPMUALazXDlBwtMU79CT9rpXNXp4tGYGhlQ0d7UqAUcXOeI6bluhUtKmhEVhzisgPFPKpWhVCTUqQrt6ygD8oJQajmgRhAOnO4RgdQm8yd0tNzGv/D8x/8Dy10IVCzn4axaTTYNZymsSA8YuciU6PrLL6IKpUFBkS7cKXXwQJfIBPyP6IQ1oHUaB7QkvjfUdcy+wIFB8PeZIYwmNtl0JruYSp8XMk+/TXL7BzbPF8gU6L95hn8D4OUJnktsfM1vavg==)

</div>

### Custom Transition Classes {#custom-transition-classes}

Bạn cũng có thể chỉ định các transition classes tùy chỉnh bằng cách truyền các prop sau vào `<Transition>`:

- `enter-from-class`
- `enter-active-class`
- `enter-to-class`
- `leave-from-class`
- `leave-active-class`
- `leave-to-class`

Những prop này sẽ ghi đè lên các class mặc định. Điều này rất hữu ích khi bạn muốn kết hợp hệ thống transition của Vue với một thư viện animation CSS hiện có, ví dụ như [Animate.css](https://daneden.github.io/animate.css/):

```vue-html
<!-- assuming Animate.css is included on the page -->
<Transition
  name="custom-classes"
  enter-active-class="animate__animated animate__tada"
  leave-active-class="animate__animated animate__bounceOutRight"
>
  <p v-if="show">hello</p>
</Transition>
```

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNqNUctuwjAQ/BXXF9oDsZB6ogbRL6hUcbSEjLMhpn7JXtNWiH/vhqS0R3zxPmbWM+szf02pOVXgSy6LyTYhK4A1rVWwPsWM7MwydOzCuhw9mxF0poIKJoZC0D5+stUAeMRc4UkFKcYpxKcEwSenEYYM5b4ixsA2xlnzsVJ8Yj8Mt+LrbTwcHEgxwojCmNxmHYpFG2kaoxO0B2KaWjD6uXG6FCiKj00ICHmuDdoTjD2CavJBCna7KWjZrYK61b9cB5pI93P3sQYDbxXf7aHHccpVMolO7DS33WSQjPXgXJRi2Cl1xZ8nKkjxf0dBFvx2Q7iZtq94j5jKUgjThmNpjIu17ZzO0JjohT7qL+HsvohJWWNKEc/NolncKt6Goar4y/V7rg/wyw9zrLOy)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNqNUcFuwjAM/RUvp+1Ao0k7sYDYF0yaOFZCJjU0LE2ixGFMiH9f2gDbcVKU2M9+tl98Fm8hNMdMYi5U0tEEXraOTsFHho52mC3DuXUAHTI+PlUbIBLn6G4eQOr91xw4ZqrIZXzKVY6S97rFYRqCRabRY7XNzN7BSlujPxetGMvAAh7GtxXLtd/vLSlZ0woFQK0jumTY+FJt7ORwoMLUObEfZtpiSpRaUYPkmOIMNZsj1VhJRWeGMsFmczU6uCOMHd64lrCQ/s/d+uw0vWf+MPuea5Vp5DJ0gOPM7K4Ci7CerPVKhipJ/moqgJJ//8ipxN92NFdmmLbSip45pLmUunOH1Gjrc7ezGKnRfpB4wJO0ZpvkdbJGpyRfmufm+Y4Mxo1oK16n9UwNxOUHwaK3iQ==)

</div>

### Sử dụng chung Transitions và Animations {#using-transitions-and-animations-together}

Vue cần phải gắn các event listener để biết khi nào một transition đã kết thúc. Nó có thể là `transitionend` hoặc `animationend`, phụ thuộc vào loại CSS rules được áp dụng. Nếu bạn chỉ sử dụng một trong hai, Vue có thể tự động xác định loại đúng.

Tuy nhiên, trong một số trường hợp bạn có thể muốn có cả hai trên cùng một phần tử, ví dụ như có một CSS animation được kích hoạt bởi Vue, cùng với một CSS transition được kích hoạt bởi hover. Trong những trường hợp như vậy, bạn sẽ phải khai báo rõ ràng loại mà bạn muốn Vue quan tâm bằng cách truyền prop `type` với giá trị là `animation` hoặc `transition`:

```vue-html
<Transition type="animation">...</Transition>
```

### Các Transition lồng nhau và thời gian Transition tường minh {#nested-transitions-and-explicit-transition-durations}

Mặc dù các classes transition chỉ được áp dụng cho phần tử con trực tiếp trong `<Transition>`, chúng ta vẫn có thể transition các phần tử lồng nhau bằng cách sử dụng các CSS selector lồng nhau:

```vue-html
<Transition name="nested">
  <div v-if="show" class="outer">
    <div class="inner">
      Xin chào!
    </div>
  </div>
</Transition>
```

```css
/* các rule CSS dưới đây sẽ được áp dụng cho phần tử .inner */
.nested-enter-active .inner,
.nested-leave-active .inner {
  transition: all 0.3s ease-in-out;
}

.nested-enter-from .inner,
.nested-leave-to .inner {
  transform: translateX(30px);
  opacity: 0;
}

/* ... những CSS cần thiết khác*/
```

Chúng ta cũng có thể thêm một transition delay vào phần tử lồng nhau khi enter, tạo ra một hiệu ứng staggered enter animation:

```css{3}
/* delay enter của các phần tử lồng nhau nhằm tạo staggered effect */
.nested-enter-active .inner {
  transition-delay: 0.25s;
}
```

Tuy nhiên, điều này tạo ra một vấn đề nhỏ. Theo mặc định, component `<Transition>` sẽ tự động xác định khi nào transition kết thúc bằng cách lắng nghe sự kiện `transitionend` hoặc `animationend` **đầu tiên** trên phần tử transition gốc. Với một transition lồng nhau, hành vi mong muốn lúc này nên là chờ đến khi tất cả các phần tử lồng nhau đều hoàn thành transition của chúng.

Đối với các trường hợp như vậy, bạn có thể chỉ định một thời gian transition tường minh (tính bằng mili giây) bằng prop `duration` trên component `<transition>`. Thời gian tổng cộng phải bằng tổng thời gian delay và thời gian transition của phần tử con:

```vue-html
<Transition :duration="550">...</Transition>
```

<NestedTransitions />

[Thử trong Playground](https://play.vuejs.org/#eNqVVMtu2zAQ/JWtekjiRo80cIGoStCil3yADy2gC02tJCIUKZCUncDwv3cpyrbstmgLGxC53J2ZnaW0i772fbIZMMqjwnIjegcW3dA/lUp0vTYOdmCwhj3URndwRalXpSoV18pSaqu38OgTrp0Z8KZURRpQqJ42DrteMoe0AyjWg3NawRcuBX95LKOp+p1/ltHTSjeNxCINaaFkZZiywgkqqwbD/IIKl8usjECxDmmj0DqsqN4XUEklNrCJRT0RUCKXzFra6sGhOSZOqYdDodTpsHT+94xS6mNyStkHjuO6SE8KKVCks45pa92b9MtkpL6FZGSBHR26NeMvjdGDqnJ4j4ifPV7PqkqoJof7rH8dI51QcYuiaV0Od1mI7v0BoU5otAQ4g+Ocz9KCQzEq0hAz7sQGScoUlcg2OEWDMHfsKAcmJWTJvQVkFmOSQo0E5HQBFUr2BiMA6Jq0G6IAlNj55yI9UV+SAJxI4hEmJ5qPSxuwLzX7q3d7ieb0DKnWpsvD0rv/49r7dzMaqHvGhfMEB3CSvkXgTFF7Vs+kQCA4tGBhsDSMQ9RSmDtt7Flrc1en+f4i9ex0mtd/ujzSeJfPJf5NyuVE/9HsPzVCnp9wf2/995n16WK8ge6Z7iaw8XICg28tMSA8fIL10IBQ0DJVyZnR08RmFtkkvHirVligv9KOkrGiZKrXriVFa6O3Fmk62hwpHj7Als4QKMOzBZSWWVgjKqjFK1YjtLdxflWSLLsL9tAHbXyJo/1PJETL1g==)

Nếu cần thiết bạn có thể chỉ định thời gian tường minh cho enter và leave riêng biệt bằng cách truyền vào một object:

```vue-html
<Transition :duration="{ enter: 500, leave: 800 }">...</Transition>
```

### Cân nhắc về Performance {#performance-considerations}

Bạn có thể thấy rằng các animation được sử dụng ở trên đều sử dụng các thuộc tính như `transform` và `opacity`. Những thuộc tính này được sử dụng để animate vì:

1. Chúng không ảnh hưởng đến layout của document trong suốt quá trình animation, do đó chúng không gây ra việc tính toán layout CSS đắt đỏ trên mỗi frame animation.

2. Phần lớn các trình duyệt hiện đại có thể tận dụng GPU hardware acceleration khi animate `transform`.

Để so sánh, các thuộc tính như `height` hoặc `margin` sẽ kích hoạt CSS layout, do đó chúng đắt đỏ hơn để animate và nên được sử dụng cẩn thận. Bạn có thể kiểm tra các resource như [CSS-Triggers](https://csstriggers.com/) để xem các thuộc tính nào sẽ kích hoạt layout nếu chúng ta animate chúng.

## JavaScript Hooks {#javascript-hooks}

Bạn có thể gắn các hành vi tùy chỉnh vào các transition bằng cách lắng nghe các sự kiện trên `<Transition>` component:

```html
<Transition
  @before-enter="onBeforeEnter"
  @enter="onEnter"
  @after-enter="onAfterEnter"
  @enter-cancelled="onEnterCancelled"
  @before-leave="onBeforeLeave"
  @leave="onLeave"
  @after-leave="onAfterLeave"
  @leave-cancelled="onLeaveCancelled"
>
  <!-- ... -->
</Transition>
```

<div class="composition-api">

```js
// được gọi trước khi phần tử được thêm vào DOM
// sử dụng hàm này để thiết lập trạng thái "enter-from" của phần tử
function onBeforeEnter(el) {}

// được gọi trong một frame sau khi phần tử được thêm vào DOM.
// sử dụng hàm này để bắt đầu animation.
function onEnter(el, done) {
  // gọi callback done để báo hiệu kết thúc transition
  // có thể bỏ qua nếu sử dụng cùng với CSS
  done()
}

// được gọi khi transition enter kết thúc.
function onAfterEnter(el) {}

// được gọi khi transition enter bị hủy trước khi hoàn thành.
function onEnterCancelled(el) {}

// được gọi trước khi leave hook.
// Phần lớn thời gian, bạn chỉ nên sử dụng leave hook
function onBeforeLeave(el) {}

// được gọi khi transition leave bắt đầu.
// sử dụng hàm này để bắt đầu animation.
function onLeave(el, done) {
  // gọi callback done để báo hiệu kết thúc transition
  // có thể bỏ qua nếu sử dụng cùng với CSS
  done()
}

// được gọi khi transition leave kết thúc và
// element đã được gỡ bỏ khỏi DOM.
function onAfterLeave(el) {}

// chỉ khả dụng với v-show transitions
function onLeaveCancelled(el) {}
```

</div>
<div class="options-api">

```js
export default {
  // ...
  methods: {
    // được gọi trước khi phần tử được thêm vào DOM
    // sử dụng hàm này để thiết lập trạng thái "enter-from" của phần tử
    onBeforeEnter(el) {},

    // được gọi trong một frame sau khi phần tử được thêm vào DOM.
    // sử dụng hàm này để bắt đầu animation.
    onEnter(el, done) {
      // gọi callback done để báo hiệu kết thúc transition
      // có thể bỏ qua nếu sử dụng cùng với CSS
      done()
    },

    // được gọi khi transition enter kết thúc.
    onAfterEnter(el) {},
    onEnterCancelled(el) {},

    // được gọi trước khi leave hook.
    // Phần lớn thời gian, bạn chỉ nên sử dụng leave hook.
    onBeforeLeave(el) {},

    // được gọi khi transition leave bắt đầu.
    // sử dụng hàm này để bắt đầu animation.
    onLeave(el, done) {
      // gọi callback done để báo hiệu kết thúc transition
    // có thể bỏ qua nếu sử dụng cùng với CSS
      done()
    },

    // được gọi khi transition leave kết thúc và
    // element đã được gỡ bỏ khỏi DOM.
    onAfterLeave(el) {},

    // chỉ khả dụng với v-show transitions
    onLeaveCancelled(el) {}
  }
}
```

</div>

Những hooks này có thể được sử dụng chung với CSS transitions / animations.

Khi sử dụng các transition bằng JavaScript thuần, thì việc thêm prop `:css="false"` là tốt nhất. Điều này sẽ báo cho Vue bỏ qua việc phát hiện CSS transition tự động. Ngoài việc hiệu suất tốt hơn một chút, điều này cũng ngăn các CSS rules vô tình can thiệp vào transition:

```vue-html{3}
<Transition
  ...
  :css="false"
>
  ...
</Transition>
```

Với `:css="false"`, chúng ta cũng hoàn toàn chịu trách nhiệm điều khiển khi nào transition kết thúc. Trong trường hợp này, các callback `done` là bắt buộc cho các hook `@enter` và `@leave`. Nếu không, các hook sẽ được gọi đồng bộ và transition sẽ kết thúc ngay lập tức.

Dưới đây là một ví dụ sử dụng [GreenSock library](https://greensock.com/) để thực hiện các animation. Bạn tất nhiên có thể sử dụng bất kỳ thư viện animation nào khác bạn muốn, ví dụ như [Anime.js](https://animejs.com/) hoặc [Motion One](https://motion.dev/).
<JsHooks />

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNqNVMtu2zAQ/JUti8I2YD3i1GigKmnaorcCveTQArpQFCWzlkiCpBwHhv+9Sz1qKYckJ3FnlzvD2YVO5KvW4aHlJCGpZUZoB5a7Vt9lUjRaGQcnMLyEM5RGNbDA0sX/VGWpHnB/xEQmmZIWe+zUI9z6m0tnWr7ymbKVzAklQclvvFSG/5COmyWvV3DKJHTdQiRHZN0jAJbRmv9OIA432/UE+jODlKZMuKcErnx8RrazP8woR7I1FEryKaVTU8aiNdRfwWZTQtQwi1HAGF/YB4BTyxNY8JpaJ1go5K/WLTfhdg1Xq8V4SX5Xja65w0ovaCJ8Jvsnpwc+l525F2XH4ac3Cj8mcB3HbxE9qnvFMRzJ0K3APuhIjPefmTTyvWBAGvWbiDuIgeNYRh3HCCDNW+fQmHtWC7a/zciwaO/8NyN3D6qqap5GfVnXAC89GCqt8Bp77vu827+A+53AJrOFzMhQdMnO8dqPpMO74Yx4wqxFtKS1HbBOMdIX4gAMffVp71+Qq2NG4BCIcngBKk8jLOvfGF30IpBGEwcwtO6p9sdwbNXPIadsXxnVyiKB9x83+c3N9WePN9RUQgZO6QQ2sT524KMo3M5Pf4h3XFQ7NwFyZQpuAkML0doEtvEHhPvRDPRkTfq/QNDgRvy1SuIvpFOSDQmbkWTckf7hHsjIzjltkyhqpd5XIVNN5HNfGlW09eAcMp3J+R+pEn7L)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNqNVFFvmzAQ/is3pimNlABNF61iaddt2tukvfRhk/xiwIAXsJF9pKmq/PedDTSwh7ZSFLjvzvd9/nz4KfjatuGhE0ES7GxmZIu3TMmm1QahtLyFwugGFu51wRQAU+Lok7koeFcjPDk058gvlv07gBHYGTVGALbSDwmg6USPnNzjtHL/jcBK5zZxxQwZavVNFNqIHwqF8RUAWs2jn4IffCfqQz+mik5lKLWi3GT1hagHRU58aAUSshpV2YzX4ncCcbjZDp099GcG6ZZnEh8TuPR8S0/oTJhQjmQryLUSU0rUU8a8M9wtoWZTQtIwi0nAGJ/ZB0BwKxJYiJpblFko1a8OLzbhdgWXy8WzP99109YCqdIJmgifyfYuzmUzfFF2HH56o/BjAldx/BbRo7pXHKMjGbrl1IcciWn9fyaNfC8YsIueR5wCFFTGUVAEsEs7pOmDu6yW2f6GBW5o4QbeuScLbu91WdZiF/VlvgEtujdcWek09tx3qZ+/tXAzQU1mA8mCoeicneO1OxKP9yM+4ElmLaEFr+2AecVEn8sDZOSrSzv/1qk+sgAOa1kMOyDlu4jK+j1GZ70E7KKJAxRafKzdazi26s8h5dm+NLpTeQLvP27S6+urz/7T5aaUao26TWATt0cPPsgcK3f6Q1wJWVY4AVJtcmHWhueyo89+G38guD+agT5YBf39s25oIv5arehu8krYkLAs8BeG86DfuANYUCG2NomiTrX7Msx0E7ncl0bnXT04566M4PQPykWaWw==)

</div>

## Tái sử dụng Transitions {#reusable-transitions}

Transition có thể được tái sử dụng thông qua hệ thống component của Vue. Để tạo một transition có thể tái sử dụng, chúng ta có thể tạo một component bao bọc `<Transition>` component và truyền nội dung slot xuống:

```vue{5}
<!-- MyTransition.vue -->
<script>
// JavaScript hooks logic...
</script>

<template>
  <!-- bọc component built-in Transition -->
  <Transition
    name="my-transition"
    @enter="onEnter"
    @leave="onLeave">
    <slot></slot> <!-- truyền slot content xuống -->
  </Transition>
</template>

<style>
/*
  Những CSS cần thiết...
  Lưu ý: tránh sử dụng <style scoped> ở đây vì nó sẽ
  không áp dụng cho slot content.
*/
</style>
```

Bây giờ chúng ta có thể sử dụng component `<MyTransition>` như một component built-in:


```vue-html
<MyTransition>
  <div v-if="show">Hello</div>
</MyTransition>
```

## Transition cho Appear {#transition-on-appear}

Nếu bạn muốn áp dụng một transition khi một phần tử xuất hiện lần đầu tiên, bạn có thể thêm prop `appear` vào `<Transition>` component:

```vue-html
<Transition appear>
  ...
</Transition>
```

## Transition giữa các Element {#transition-between-elements}

Bên cạnh việc toggle một phần tử với `v-if` / `v-show`, chúng ta cũng có thể transition giữa hai phần tử sử dụng `v-if` / `v-else` / `v-else-if`, miễn là chúng ta đảm bảo rằng chỉ có một phần tử được hiển thị tại mọi thời điểm:

```vue-html
<Transition>
  <button v-if="docState === 'saved'">Edit</button>
  <button v-else-if="docState === 'edited'">Save</button>
  <button v-else-if="docState === 'editing'">Cancel</button>
</Transition>
```

<BetweenElements />

[Thử trong Playground](https://play.vuejs.org/#eNqdk8tu2zAQRX9loI0SoLLcFN2ostEi6BekmwLa0NTYJkKRBDkSYhj+9wxJO3ZegBGu+Lhz7syQ3Bd/nJtNIxZN0QbplSMISKNbdkYNznqCPXhcwwHW3g5QsrTsTGekNYGgt/KBBCEsouimDGLCvrztTFtnGGN4QTg4zbK4ojY4YSDQTuOiKwbhN8pUXm221MDd3D11xfJeK/kIZEHupEagrbfjZssxzAgNs5nALIC2VxNILUJg1IpMxWmRUAY9U6IZ2/3zwgRFyhowYoieQaseq9ElDaTRrkYiVkyVWrPiXNdiAcequuIkPo3fMub5Sg4l9oqSevmXZ22dwR8YoQ74kdsL4Go7ZTbR74HT/KJfJlxleGrG8l4YifqNYVuf251vqOYr4llbXz4C06b75+ns1a3BPsb0KrBy14Aymnerlbby8Vc8cTajG35uzFITpu0t5ufzHQdeH6LBsezEO0eJVbB6pBiVVLPTU6jQEPpKyMj8dnmgkQs+HmQcvVTIQK1hPrv7GQAFt9eO9Bk6fZ8Ub52Qiri8eUo+4dbWD02exh79v/nBP+H2PStnwz/jelJ1geKvk/peHJ4BoRZYow==)

## Transition Modes {#transition-modes}

Trong những ví dụ trước, các phần tử đang enter và leave được animate cùng nhau. Điều này có nghĩa là chúng ta phải làm cho cả hai phần tử `position: absolute` để tránh vấn đề layout khi cả hai phần tử đều có mặt trong DOM.

Tuy nhiên, trong một số trường hợp chúng ta không thể thực hiện được việc này hoặc không muốn có hành vi như vậy. Chúng ta có thể muốn phần tử leave được animate trước, và phần tử enter chỉ được thêm vào DOM sau khi phần tử leave đã hoàn thành animation. Việc điều khiển các animation như vậy bằng cách thủ công sẽ rất phức tạp - may mắn là chúng ta có thể bật hành vi này bằng cách truyền prop `mode` vào `<Transition>`:

```vue-html
<Transition mode="out-in">
  ...
</Transition>
```

Dưới đây là một ví dụ về việc sử dụng `mode="out-in"`:

<BetweenElements mode="out-in" />

`<Transition>` cũng hỗ trợ `mode="in-out"`, mặc dù nó ít được sử dụng hơn.

## Transition giữa các Component {#transition-between-components}

`<Transition>` cũng có thể được sử dụng để transition giữa các [dynamic components](/guide/essentials/component-basics#dynamic-components):

```vue-html
<Transition name="fade" mode="out-in">
  <component :is="activeComponent"></component>
</Transition>
```

<BetweenComponents />

<div class="composition-api">

[Thử trong Playground](https://play.vuejs.org/#eNqtksFugzAMhl/F4tJNKtDLLoxWKnuDacdcUnC3SCGJiMmEqr77EkgLbXfYYZyI8/v77dinZG9M5npMiqS0dScMgUXqzY4p0RrdEZzAfnEp9fc7HuEMx063sPIZq6viTbdmHy+yfDwF5K2guhFUUcBUnkNvcelBGrjTooHaC7VCRXBAoT6hQTRyAH2w2DlsmKq1sgS8JuEwUCfxdgF7Gqt5ZqrMp+58X/5A2BrJCcOJSskPKP0v+K8UyvQENBjcsqTjjdAsAZe2ukHpI3dm/q5wXPZBPFqxZAf7gCrzGfufDlVwqB4cPjqurCChFSjeBvGRN+iTA9afdE+pUD43FjG/bSHsb667Mr9qJot89vCBMl8+oiotDTL8ZsE39UnYpRN0fQlK5A5jEE6BSVdiAdrwWtAAm+zFAnKLr0ydA3pJDDt0x/PrMrJifgGbKdFPfCwpWU+TuWz5omzfVCNcfJJ5geL8pqtFn5E07u7fSHFOj6TzDyUDNEM=)

</div>
<div class="options-api">

[Thử trong Playground](https://play.vuejs.org/#eNqtks9ugzAMxl/F4tJNamGXXVhWqewVduSSgStFCkkUDFpV9d0XJyn9t8MOkxBg5/Pvi+Mci51z5TxhURdi7LxytG2NGpz1BB92cDvYezvAqqxixNLVjaC5ETRZ0Br8jpIe93LSBMfWAHRBYQ0aGms4Jvw6Q05rFvSS5NNzEgN4pMmbcwQgO1Izsj5CalhFRLDj1RN/wis8olpaCQHh4LQk5IiEll+owy+XCGXcREAHh+9t4WWvbFvAvBlsjzpk7gx5TeqJtdG4LbawY5KoLtR/NGjYoHkw+PTSjIqUNWDkwOK97DHUMjVEdqKNMqE272E5dajV+JvpVlSLJllUF4+QENX1ERox0kHzb8m+m1CEfpOgYYgpqVHOmJNpgLQQa7BOdooO8FK+joByxLc4tlsiX6s7HtnEyvU1vKTCMO+4pWKdBnO+0FfbDk31as5HsvR+Hl9auuozk+J1/hspz+mRdPoBYtonzg==)

</div>

## Dynamic Transitions {#dynamic-transitions}

Các prop của `<Transition>` có thể được set bằng các giá trị động, điều này cho phép chúng ta áp dụng các transition khác nhau dựa trên các điều kiện khác nhau:

```vue-html
<Transition :name="transitionName">
  <!-- ... -->
</Transition>
```

Điều này có thể hữu ích khi bạn đã định nghĩa các CSS transitions / animations sử dụng các quy ước class của Vue và muốn chuyển đổi giữa chúng.

Bạn cũng có thể sử dụng cách hành vi khác nhau trong các JavaScript hooks dựa trạng thái hiện tại của component của bạn. Cuối cùng, cách tốt nhất để tạo các transition động là thông qua [reusable transition components](#reusable-transitions), nhận các prop để thay đổi bản chất của ác transition được sử dụng. Có thể nghe có vẻ hơi lạ, nhưng giới hạn thực sự chỉ nằm ở trí tưởng tượng của bạn.

---

**Xem thêm**

- [Tham khảo `<Transition>` API](/api/built-in-components#transition)
