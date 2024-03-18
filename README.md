# コーディングルール

作成日：2024.03.13 　　更新日：2024.03.13

## CSS 設計

大まかなベースは[FLOCSS](https://github.com/hiloki/flocss)を利用。

命名は FLOCSS に沿って [BEM](https://en.bem.info/methodology/quick-start) を利用。

### コーディング規約の必要性

- 予測しやすい
- 再利用しやすい
- 保守しやすい
- 拡張しやすい

上記の４つの条件を満たしたコードが良いとされています。<br>
簡単に言うと以下の通りです。

#### 予測

クラス名を見るだけでどこに使われているか、どのようなものなのか予測ができる

#### 再利用

同じようなパーツがデザインにあった時、同じものを別に用意せず書き直したり上書きせずに利用ができる

#### 保守

新しく機能を追加、更新しても既存のスタイル・ソースが破綻しにくい

#### 拡張

自分以外の作業者が利用・管理しやすいコードである。

### ◆ FLOCSS について※重要箇所のピックアップ

以下を見る前に一度[FLOCSS](https://github.com/hiloki/flocss)に目を通してください。

#### FLOCSS のレイヤー構成について

- **Foundation** ・・・ Reset.css や Normalize.css などを用いたブラウザのデフォルトスタイルの初期化や、プロジェクトにおける基本的なスタイルを定義
- **Layout** ・・・ ページを構成するヘッダーやメインのコンテンツエリア、サイドバーやフッターといったプロジェクト共通のコンテナーブロックのスタイルを定義
- **Object** ・・・ プロジェクトにおける繰り返されるビジュアルパターンをすべて Object と定義。次の 3 つのレイヤーに分ける。
  - **Component** ・・・ 再利用できるパターンとして、小さな単位のモジュールを定義
  - **Project** ・・・ プロジェクト固有のパターンであり、いくつかの Component と、それに該当しない要素によって構成されるもの
  - **Utility** ・・・ わずかなスタイルの調整のための便利クラスなどを定義

```
scss
 ├─Foundation
 │   ├─mixin
 │   └─variable
 ├─Layout
 ├─object
 │   ├─component
 │   ├─project
 │   └─Utility
 └─style.scss
```

#### FLOCSS の基本的な class の書き方※命名規則について

「Layout」の class には **.l-**<br>
「Component」の class には **.c-**<br>
「Project」には **.p-**<br>
「Utility」には **.u-** <br>
というように、フォルダの頭文字を class に記述する。

> [!WARNING]
> 「Object」レイヤーは Component、Project、Utility の親レイヤーのため、 ".o-"から始まるクラスは FLOCSS のルール違反になります。<br>
> 必ず".p-"か".c-"、".u-"のいずれかにしてください。

> [!IMPORTANT]
> また、基本的に「Object」ディレクトリ直下にファイルは作らないようにしてください。ここのディレクトリ直下にあるファイルはコンパイル用の style.scss のみにしてください。<br>
> 例外は".p-"などのファイル数が少なく、混在させても管理が容易（ディレクトリでの整理が不要）の場合のみです。

### BEM について

以下を見る前に一度[BEM](https://en.bem.info/methodology/quick-start) / [BEM (日本語翻訳版)](https://github.com/juno/bem-methodology-ja/blob/master/definitions.md)に目を通してください。

#### BEM の概念

BEM は **ブロック（Block）**、**エレメント（Element）**、**モディファイア（Modifier）** の頭文字をとったものです。<br>
Block はあるパーツ（コンポーネント）の親要素です。<br>
Block は Element と呼ばれる子孫要素を持つことができます。<br>
Modifier はバリエーションや状態を変化させるときに指定するもので、Block か Element と同階層に指定します。

- .**Block**
- .Block\_\_**Element**
- .Block-**Modifier**
- .Block\_\_Element-Modifier

上記の 4 パターンが基本です。

#### ID は使わない

CSS でスタイルを適用する際に ID を使用しない。<br>
ID は jQuery や javascript などの動的パーツに用いる。

```
<nav class="p-menu l-menu" id="js-menu">
  <div class="p-menu__cont">
    <!-- メニュー -->
  </div>
</nav>
```

#### スタイル（css）は全て class に対して指定する（要素に対して直にスタイルを指定しない）

要素を用いることによって、詳細度が上がってしまい他のスタイルを上書きしてしまう。<br>
BEM では詳細度を均一に保つというルールがあるため、極力要素は書かないこと。

#### MIX について

Block は使い回しが可能なパーツとして考えるため、レイアウトに関わる Block に指定しないこと。

例えばボタンだと、見た目は使い回したいが位置や余白の距離が違う等の場合がある。<br>
この場合、ボタンに 2 つのクラスを指定する。見た目のスタイルを Block、位置や余白のスタイルを Element に指定する。<br>
これは BEM では**Mix**と言う。

> [!TIP] > **Mix**とは Block 自体が持つべきではないレイアウトなどに関する指定を Element を利用して指定する、BEM にとって最も重要なテクニック。<br>
> 特に意識してコーディングをお願いします。

#### Modifier（バリエーション）

**Modifier**は Block と Element のバリエーションや状態の変化をつくるときに指定します。

```
// 例

<div class="p-box p-box-orange">
  <div class="p-box__txt">

  </div>
  <div class="p-box__btn p-box__btn-small">
    <a href="#" class="c-btn c-btn-small c-btn-red">button</a>
  </div>
</div>
```

```
.p-box{
  // ...
  border: 4px solid #333;
  &-orange{
    border-color: orange;
  }
  &__btn{
    margin-top: 80px;
    &-small{ // Modifier
      margin-top: 16px;
    }
  }
}

.c-btn{
  // ...
  width: 400px;
  &-red{
    background-color: red;
    color: #fff;
  }
  &-small{ // Modifier
    width: 240px;
  }
}

```

## ★ ハウスルール

ここでは上記の FLOCSS、BEM にはないルールを記述している。

上記ルールの場合 class 名が長くなってしまうため、追加したルール。

### 単語と単語を繋げたい場合、ケバブケース・キャメルケース（ローワーキャメルケース）を混在させない

※どちらを使っても構いませんが、混在させるのは**絶対 NG**です。

```
/* ◆ ケバブケース */

.p-hero-slider {}

.p-hero-catch {}


/* ◆ キャメルケース（ローワーキャメルケース） */

.p-heroSlider {}

.p-heroCatch {}
```

### アンダースコアは２つ（\_\_）、ハイフンは１つ（-）。

```
// 例

<a href="#" class="c-btn c-btn-red c-btn-kadomaru">
  <svg class="c-btn__ico c-bnt__ico-wh">
    <!-- アイコン -->
  </svg>
  ボタン
</a>
```

### エレメントの後にエレメントを繋ぐ書き方は NG

```
// ×　アンダースコアが2つ以上続いている

<nav class="p-header__nav">
  <ul class="p-header__nav__list">
    <li class="p-header__nav__list__item">
      <!--menu-->
    </li>
  </ul>
</nav>


// 〇　新しいBlockをつくりElementで続ける。

<nav class="p-headerNav">
  <ul class="p-headerNav__list">
    <li class="p-headerNav__listItem">
      <!--menu-->
    </li>
  </ul>
</nav>


// 〇　Blockの中でBlockを用意する

<nav class="p-headerNav">
  <ul class="p-headerNav__list p-hNavList">
    <li class="p-hNavList__item">
      <!--menu-->
    </li>
  </ul>
</nav>
```

### JS のクラス名は「is-」「js-」を使用

```
<header class="l-header js-header">
  <!-- header -->
</header>
```

```
.l-header{
  position: relative;
  &.is-fixed{
    position: fixed;
    top: 0;
    left: 0;
  }
}
```

```
const header = document.querySelector('.js-header');
window.addEventListener('scroll', () => {
  if (window.pageYOffset > 100) {
    header.classList.add('is-fixed');
  } else {
    header.classList.remove('is-fixed');
  }
});
```

## FLOCSS(+BEM)のコーディング例

```
<header class="l-header" id="js-header"> <!--※1-->
  <div class="p-header">
    <div class="p-header__inner">
      <h1 class="p-header__logo p-logo"> <!--※2-->
        <a href="/" class="p-logo__link">
          <!--LOGO-->
        </a>
      </h1>
      <nav class="p-header__nav p-gNav">
        <!--MENU-->
      </nav>
      <div class="p-header__contact">
        <a href="" class="p-contactBtn p-contactBtn-orange"> <!--※4-->
          <!-- CONTACT -->
        </a>
      </div>
    </div>
  </div>
</header>
```

#### ※1

##### Layout には位置など、レイアウトの指定のみ定義し、子要素を作らない

#####

```
// positionやmarginなど
.l-header{
  position: relative;
  &.is-fixed{
    position: fixed;
    left: 0;
    top: 0;
  }
}
```

#####

#### ※2

##### Block には「margin」「position:absolute」などのレイアウトに関わるスタイルを指定しない

##### Block の中で Block を用意する

```
// 背景色などのスタイルはObject側のパーツやブロックで指定する。
.p-header{
  background-color: #f6f6f6;
  &__inner{
    max-width: 1200px;
    margin: auto;
  }
}
```

## Sass(Dart-Sass)

2022 年ごろの Sass（LibSass）で使用されていた@import が廃止されるにあたり、Dart-Sass 用に Sass を書き換えました。

こちらでは@import ではなく、@use、@forward を用いてコンパイル等を行います。

## 参考サイト

https://zenn.dev/wagashi_osushi/books/94efd21a66ccaa

### CSS ガイドライン

https://github.com/manabuyasuda/styleguide/blob/master/css-styleguide.md

https://brachio.co.jp/blog/w6s7zd13hha7

https://qiita.com/super-mana-chan/items/644c6827be954c8db2c0

### FLOCSS

https://github.com/hiloki/flocss

https://tane-be.co.jp/knowledge/web-design/2270/

### BEM

https://fukulog.net/bem/

https://bagelee.com/design/about_flocss_and_bem/

https://tonoblog.yutaka01.net/2023/11/15/css_bem_element/

### クラス名参考

https://github.com/manabuyasuda/coding-guidelines/blob/master/css/css-naming-list.md
