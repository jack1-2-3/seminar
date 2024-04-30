# スライダーに関して

## 大きく分けて2パターンある(自分の経験の中では)
- 要素を横か縦に並べるパターン
- 要素を重ねるパターン

---

## 並べるパターンの場合
並べるパターンは割と単純。  
ただ、無限カルーセルになると少し複雑になる。

- flexboxで横に並べる
- `margin` もしくは `left` もしくは `transform` でスライドさせる

```js
function slider() {
  const sliderItems = document.querySelectorAll(".p-slider__item");
  const sliderItemWidth = sliderItems[0].clientWidth;

  let counter = 0;

  setInterval(startSlider, 2000);

  function startSlider() {
    counter++;
    if (counter > sliderItems.length - 1) {
      counter = 0;
    }

    const marginLeft = counter * sliderItemWidth

    sliderItems[0].style.marginLeft = `-${marginLeft}px`;
  }
}
```

---

## 重ねるパターンの場合
少し難しい。  
ただ、理屈さえ分かれば結構単純。

- `position: absolute;` で要素を重ねる
- `z-index` をJSで動的に操作する
- ↑の`z-index`の操作が肝

### 考え方
- 重ねた要素を**3層**に分けて考える

1. **frontView** ・・・ 一番上にある要素
2. **middleView** ・・・ 上から2番目にある要素
3. **backView** ・・・ 上から3番目以降にある要素

**frontView**の`z-index`を**3**  
**middleView**の`z-index`を**2**  
**backView**の`z-index`を**1**  
↑のように定義してJSの`setInterval()`で操作する

※ スライダーを手動で進めたり戻したりのボタンを追加する時は、少し複雑になるので注意

デモ → https://jack1-2-3.github.io/stack_slider/

```js
function slider() {
  const sliderItems = document.querySelectorAll('.p-slider__item')

  let counter = 0

  const frontViewZIndex = 3
  const middleViewZIndex = 2
  const backViewZIndex = 1

  setInterval(startSlider, 2000)

  function startSlider() {
    const activeItem = document.querySelector('.p-slider__item.is-active')
    if (activeItem) {
      activeItem.classList.remove('is-active')
    }

    const frontViewCounter = counter
    
    let middleViewCounter = counter + 1
    if (middleViewCounter > sliderItems.length - 1) {
      middleViewCounter = 0
    }
    
    sliderItems.forEach((item) => {
      item.style.zIndex = backViewZIndex
    })
    sliderItems[frontViewCounter].style.zIndex = frontViewZIndex
    sliderItems[middleViewCounter].style.zIndex = middleViewZIndex
    
    sliderItems[frontViewCounter].classList.add('is-active')

    counter++
    if (counter > sliderItems.length - 1) {
      counter = 0
    }
  }
}
```

---
## おまけ
下記の2つのコードで表示される値はなんでしょう？

```js
for (var i = 0; i < 3; i++) {
  setTimeout(() => {
    console.log(i);
  }, 10)
}
```

```js
for (let i = 0; i < 3; i++) {
  setTimeout(() => {
    console.log(i);
  }, 10)
}
```
