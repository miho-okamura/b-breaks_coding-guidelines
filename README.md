# コーディングルール

作成日：2024.03.13 　　更新日：2024.03.13

## 開発環境

### コーディング

■Vite ■Sass（SCSS） ■Handlebars

## HTML

## Sass(Dart-Sass)

2022 年ごろの Sass（LibSass）で使用されていた@import が廃止されるにあたり、Dart-Sass 用に Sass を書き換えました。

こちらでは@import ではなく、@use、@forward を用いてコンパイル等を行います。

## CSS 設計

大まかなベースは[FLOCSS](https://github.com/hiloki/flocss)を利用。

命名は [BEM](https://en.bem.info/methodology/quick-start) を利用。

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



#### Layoutには位置など、レイアウトの指定のみ定義し、子要素を作らない

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

// 背景色などのスタイルはObject側のパーツやブロックで指定する。
.p-header{
  background-color: #f6f6f6;
  &__inner{
    max-width: 1200px;
    margin: auto;
  }
}
```

### BEM について

以下を見る前に一度[BEM](https://en.bem.info/methodology/quick-start) / [BEM (日本語翻訳版)](https://github.com/juno/bem-methodology-ja/blob/master/definitions.md)に目を通してください。

#### BEM の概念

BEM は **ブロック（Block）**、**エレメント（Element）**、**モディファイア（Modifier）** の頭文字をとったものです。<br>
Block はあるパーツ（コンポーネント）の親要素です。Block は Element と呼ばれる子孫要素を持つことができます。<br>
Modifier はバリエーションや状態を変化させるときに指定するもので、Block か Element と同階層に指定します。

- .**Block**
- .Block\_\_**Element**
- .Block-**Modifier**
- .Block\_\_Element-Modifier

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

<div class="p-box">
  <div class="p-box__btn">
    <a href="#" class="c-btn p-box__btn-item">button</a>
  </div>
</div>
```

## ★ ハウスルール

ここでは上記の FLOCSS、BEM にはないルールを記述している。

上記ルールの場合 class 名が長くなってしまうため、追加したルール。

### Block の命名で単語と単語を繋げたい場合、ケバブケースではなくキャメルケース（ローワーキャメルケース）で表記する。

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

### エレメントの後にエレメントを繋ぐ書き方はNG

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

## FLOCSS(+BEM)のコーディング例

#### 例 1 : 複数のクラス名をつけて管理する

```
<header class=l-header" id="js-nav">
  <div class="p-header">
    <h1 class="p-header__logo p-logo">
      <a href="/" class="p-logo__link">
        <!--LOGO-->
      </a>
    </h1>
    <nav class="p-header__nav p-gNav">
      <!--MENU-->
    </nav>
    <div class="p-header__contact">
      <a href="" class="p-contactBtn p-contactBtn-orange">
        <!-- CONTACT -->
      </a>
    </div>
  </div>
</header>
```


#### 例 2 : Block には「margin」「position:absolute」などのレイアウトに関わるスタイルを指定しない

```
.p-header{
  width: 100%;
  height: 100%;
  background-color: #f6f6f6;
}

.l-header{
  position: fixed;
  left: 0;
  top: 0;
}
```

上の例だと、.c-card が**Block**、.p-section__btn が**Elements**になる。<br>
この場合、 **Elements**である.p-section__btn 側で位置や余白の調整の記述を追加する。<br>
こうすることにより、**Block**の再利用性を高くする。


## 参考サイト

### CSS ガイドライン

https://github.com/manabuyasuda/styleguide/blob/master/css-styleguide.md

https://brachio.co.jp/blog/w6s7zd13hha7

### FLOCSS

https://github.com/hiloki/flocss

https://tane-be.co.jp/knowledge/web-design/2270/

### BEM

https://fukulog.net/bem/

https://bagelee.com/design/about_flocss_and_bem/

https://tonoblog.yutaka01.net/2023/11/15/css_bem_element/

### クラス名参考

https://github.com/manabuyasuda/coding-guidelines/blob/master/css/css-naming-list.md
