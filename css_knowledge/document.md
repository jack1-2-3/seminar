# CSSで必須の前提知識

## 前置き

今回のテーマは、普段フロントエンドに慣れ親しんでいる方からしたら知ってて当然だと思う内容です。  
ただ、これからフロントエンドに挑戦する方からしたら
- 知ってないとハマるであろう
- 知っていたらこの先の成長角度が明らかに高くなるであろう

というところを厳選しました。

## 目次

### CSS

1. セレクタ
   1. 詳細度
   2. 結合子
2. box-sizing プロパティ
3. display プロパティ
   1. block
   2. inline
   3. inline-block
   4. flex
   5. grid
4. position プロパティ
   1. static
   2. relative
   3. absolute
   4. fixed
   5. sticky
5. z-index プロパティ
   1. stack context(重ね合わせコンテキスト)

## 1.セレクタ

CSS のスタイルが適用される要素を定義したもの。  
  
参考: https://developer.mozilla.org/ja/docs/Web/CSS/CSS_Selectors

セレクタ一覧 ↓

- 全称セレクタ
- 要素型セレクタ
- ID セレクタ
- クラスセレクタ
- 属性セレクタ

#### 全称セレクタ

すべての要素を選択します。 \* (アスタリスク) ← これ

#### 要素型セレクタ

指定されたノード名を持つすべての要素を選択します。
「div, span, section」 ← みたいなタグの名前

#### ID セレクタ

id 属性の値に基づいて要素を選択します。
「#hoge」← みたいな「#」と一緒に書きます。

#### クラスセレクタ

指定された class 属性を持つすべての要素を選択します。
「.fuga」← みたいな「.」と一緒に書きます。

#### 属性セレクタ

指定された属性を持つ要素をすべて選択します。  
[attr] [attr=value] [attr~=value] [attr|=value] [attr^=value] [attr$=value] [attr*=value]  
↑ みたいに[属性="値"]で書きます。

### 1-1.詳細度

ある要素に最も関連性の高い CSS 宣言を決定するためにブラウザーが使用するアルゴリズムで、これによって、その要素に使用するプロパティ値が決定されます。  
詳細度のアルゴリズムは、CSS セレクターの重みを計算し、競合する CSS 宣言の中からどのルールを要素に適用するかを決定します。  
  
参考: https://developer.mozilla.org/ja/docs/Web/CSS/Specificity

#### セレクタの重み分類

- ID 列
  - ID セレクタ
- CLASS 列
  - クラスセレクタ
  - 属性セレクタ
  - 擬似クラスセレクタ
- TYPE 列
  - 要素型セレクタ
  - 疑似要素セレクタ
  - それ以外の「:(コロン)」二つの表記のセレクタ全て

#### セレクタの重みにおける計算方法

- **ID 列の数 - CLASS 列の数 - TYPE 列の数**

一致するセレクタの中で上記を計算し、
左から順に比べ、数が多いかどうかで強さが決まる

例 1:

```html:index.html
<span id="hoge-id" class="hoge-class">テスト</span>
```

```css:style.css
a {
    color: red;
}

#hoge-id {
    color: blue;
}

.hoge-class {
    color: green;
    border: 1px solid pink;
}
```

詳細度 ↓

```
a → 0-0-1
#hoge-id → 1-0-0
.hoge-class → 0-1-0
```

例 2:

```html:index.html
<div class="hoge-class">
    <a id="hoge-id" class="fuga-class" href="https://example.com">外部リンクです</a>
</div>
```

```css:style.css
.hoge-class .fuga-class {
    color: red;
    border: 1px solid pink;
}

.fuga-class {
    color: blue;
}

.hoge-class #hoge-id {
    color: green;
}

.fuga-class[href="https://example.com"] {
    border: 2px solid yellow;
}
```

詳細度 ↓

```
.hoge-class .fuga-class → 0-2-0
.fuga-class → 0-1-0
.hoge-class #hoge-id → 1-1-0
.fuga-class[href="https://example.com"] → 0-2-0
```

### !important キーワード
詳細度を無視して、スタイルが適用されます。

例:
```html:index.html
<div class="hoge-class">
    <a id="hoge-id" class="fuga-class" href="https://example.com">外部リンクです</a>
</div>
```

```css:style.css
.hoge-class .fuga-class {
    color: red;
    border: 1px solid pink;
}

.fuga-class {
    color: blue;
}

.hoge-class #hoge-id {
    color: green;
}

.fuga-class[href="https://example.com"] {
    border: 2px solid yellow;
}

a {
    color: pink !important;
    border: none !important;
}
```

### 1-2.結合子

前後に記述された合成セレクタの文書ツリーでの関係性を表します。  
  


#### 結合子一覧

- 子孫結合子(' ')(※半角空白)
- 子結合子(>)
- 隣接兄弟結合子(+)
- 一般兄弟結合子(~)
- セレクターリスト(,)

### 子孫結合子(' ')(※半角空白)

2 つの CSS セレクターを結合し、 1 つ目のセレクターに一致する要素が祖先 (親、親の親、親の親の親、など) となっている 2 つ目のセレクターに一致します。  
  
参考: https://developer.mozilla.org/ja/docs/Web/CSS/Descendant_combinator

```html:index.html
<div class="hoge">
  <p class="fuga">marginが適用されます</p>
</div>
```

```css:style.css
.hoge .fuga {
  margin: 10px;
}
```

### 子結合子(>)

2 つ目のセレクターが 1 つ目のセレクターの**直接**の子要素の場合にのみ一致します。  
  
https://developer.mozilla.org/ja/docs/Web/CSS/Child_combinator

```html:index.html
<div class="hoge">
  <div class="fuga">
    marginが適用されます
    <p class="fuga">marginが適用されません</p>
  </div>
</div>
```

```css:style.css
.hoge > .fuga {
  margin: 10px;
}
```

### 隣接兄弟結合子(+)

2 つ目の要素が 1 つ目の要素の**直後**にあって、両者が同じ親要素の子同士である場合に一致します。  
  
参考: https://developer.mozilla.org/ja/docs/Web/CSS/Adjacent_sibling_combinator

```html:index.html
<div class="hoge">
  <p class="fuga">border-topが適用されません</p>
  <p class="fuga">border-topが適用されます</p>
  <p class="fuga">border-topが適用されます</p>
</div>
```

```css:style.css
.fuga + .fuga {
  border-top: 1px solid blue;
}
```

### 一般兄弟結合子(~)

1 つ目の要素の後に 2 つ目の要素があり（**直後である必要はない**）、かつ両者が**同じ親要素の子**であるすべてのパターンに一致します。  
  
参考: https://developer.mozilla.org/ja/docs/Web/CSS/General_sibling_combinator

```html:index.html
<div class="hoge">
  <p class="fuga">border-topが適用されません</p>
  <p class="foo">border-topが適用されません</p>
  <p class="fuga">border-topが適用されます</p>
  <p class="fuga">border-topが適用されます</p>
</div>
```

```css:style.css
.fuga ~ .fuga {
  border-top: 1px solid blue;
}
```

### セレクターリスト(,)
すべての一致するノードを選択します。  
セレクターリストはカンマ区切りのセレクターのリストです。  
  
参考: https://developer.mozilla.org/ja/docs/Web/CSS/Selector_list

```html:index.html
<div class="hoge">
  marginが適用されます
  <p class="fuga">marginが適用されます</p>
  <p class="foo">marginが適用されます</p>
  <p class="fuga">marginが適用されます</p>
  <p class="fuga">marginが適用されます</p>
</div>
```

```css:style.css
.hoge,
.fuga,
.foo {
  margin: 10px;
}
```

## 2.box-sizing プロパティ
要素の全体の幅と高さをどのように計算するのかを設定します。  
  
参考: https://developer.mozilla.org/ja/docs/Web/CSS/box-sizing  
#### box-sizingに設定できる値
- content-box
- border-box
  
### content-box
ある要素の幅を 100 ピクセルに設定した場合、その要素のコンテンツ領域の幅が 100 ピクセルになり、境界やパディングを加えたものが最終的に表示される幅になり、要素が 100px よりも広くなります。

### border-box
境界およびパディングを、要素に指定した width および height の中で取るようブラウザーに指示します。要素の width を 100 ピクセルに設定した場合、100 ピクセルの中に追加した境界やパディングが含まれ、コンテンツ領域はそれらの幅を吸収するために縮小されます。これで普通は、要素に対するサイズ設定をもっと簡単になります。

## 3.display プロパティ
要素をブロック要素とインライン要素のどちらとして扱うか、およびその子要素のために使用されるレイアウト、例えば フローレイアウト、グリッド、フレックスなどを設定します。  
  
display のいくつかの値は、それ自身の個別の仕様書で完全に定義されています。  
  
参考: https://developer.mozilla.org/ja/docs/Web/CSS/display  
  
### display の値
- block
- inline
- inline-block
- flex
- grid
- table
- flow-root

### block
この要素はブロック要素のボックスを生成し、通常のフローでは要素の前後で改行を生成します。

### inline
この要素は、自身の前後に改行を生成しない 1 つ以上のインライン要素ボックスを生成します。通常のフローでは、次の要素は、空間があれば同じ行になります。

### flex
要素は、ブロック要素のように動作しつつ、そのコンテンツをフレックスボックスモデルに従ってレイアウトします。  
  
参考: https://developer.mozilla.org/ja/docs/Web/CSS/CSS_Flexible_Box_Layout  
  
#### 押さえておきたいプロパティ
- flex-direction
- justify-content
- align-items
- flex-grow
- flex-shrink
- flex-basis
- flex(一括指定プロパティ)
- gap(ブラウザの互換性に注意が必要)
- order

### grid
要素は、ブロック要素のように動作しつつ、そのコンテンツをグリッドモデルに従ってレイアウトします。  
  
参考: https://developer.mozilla.org/ja/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout  
  
#### 押さえておきたいプロパティ
- grid-template-columns
- grid-template-rows
- grid-template-areas
- justify-content
- align-items
- gap(ブラウザの互換性に注意が必要)
- grid-area

...etc

### table
HTMLの `<table>` 要素と同じように動作します。これは、ブロックレベルボックスを定義します。

### flow-root
要素は、新たなブロック整形コンテキストを確立するブロック要素ボックスを生成し、整形ルートがある場所を定義します。  
  
参考: https://developer.mozilla.org/ja/docs/Web/Guide/CSS/Block_formatting_context  
  

### ※display プロパティにおける注意点(個人的に)

#### 注意点1
`display: inline;` を設定している(もしくは規定値が`display: inline;`)ものは、  
  
横幅、高さ
- `width`
- `height`
  
と
  
縦方向の
- `padding`
- `margin`
  
上記が適用されません。  
  
一方、`display: inline-block` は上記が適用されます。  
また、親要素に`display: flex` or `display: grid` が適用されている場合の子要素も上記が適用されます。

#### 注意点2
`display: inline;` と `display: inline-block` は、
  
- `margin: auto;`
  
が適用されません。

ただ、親要素に`display: flex` or `display: grid` が適用されている場合の子要素は上記が適用されます。  
(子要素がspanタグなどの`display: inline`のものであったとしても)

## 4.position プロパティ
文書内で要素がどのように配置されるかを設定します。  
top, right, bottom, left の各プロパティが、配置された要素の最終的な位置を決めます。  
  
参考: https://developer.mozilla.org/ja/docs/Web/CSS/position

### 値一覧
- static
- relative
- absolute
- fixed
- sticky

※初期値は「static」

### static
要素は文書の通常のフローに従って配置されます。 top, right, bottom, left, z-index プロパティは効果がありません。これが既定値です。

### relative
要素は文書の通常のフローに従って配置され、 top, right, bottom, left の値に基づいて自分自身からの相対オフセットで配置されます。

### absolute
要素は文書の通常のフローから除外され、ページレイアウト内に要素のための空間が作成されません。直近の配置されている祖先があれば、それに対して相対配置されます。

### fixed
要素は文書の通常のフローから除外され、ページレイアウト内に要素のための空間が作成されません。ビューポートによって定められた初期の包含ブロックに対して相対配置されます。

### sticky
要素は文書の通常のフローに従って配置され、直近のスクロールする祖先および包含ブロック (直近のブロックレベル祖先、表関連要素を含む) に対して top, right, bottom, left の値に基づいて相対配置されます。オフセットは他の要素の配置には影響を与えません。

## 5.z-index プロパティ
位置指定要素とその子孫要素、またはフレックスアイテムの z 順を定義します。  
より大きな z-index を持つ要素はより小さな要素の上に重なります。  
  
参考: https://developer.mozilla.org/ja/docs/Web/CSS/z-index
  
※初期値は「auto」

### 5-1.stack context(重ね合わせコンテキスト)
ビューポートまたはウェブページに面していると想定されるユーザーに対する仮想的な Z 軸に沿って並べられた HTML 要素の三次元の概念化です。  
  
参考: https://developer.mozilla.org/ja/docs/Web/CSS/CSS_Positioning/Understanding_z_index/The_stacking_context
