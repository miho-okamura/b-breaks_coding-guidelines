# コーディングルール

作成日：2024.03.13 　　更新日：2024.03.13

## Sass(Dart-Sass)

2022 年ごろの Sass（node-sass、LibSass）で使用されていた@import が廃止されるにあたり、Dart-Sass 用に Sass を書き換えました。

### 以前の Sass（node-sass、LibSass）と違う点

- @import ではなく、**@use**、**@forward** を用いてコンパイル等を行います。

- 除算処理（割り算）で/（スラッシュ）を用いるとエラーになるため、**math.div**を利用する。

### @use

**@use**は@import と同様に呼び出し先を読み込む機能です。<br>
@use には以下の機能があります。

- ファイル名を名前空間にする
- 任意の名前空間を設定する(as)
- 名前空間をなくす（as \*）
- 変数を上書きする（!default と with）

主に重要な点として、名前空間の宣言

#### ■ ファイル名を名前空間にする

@use で読み込むファイルパスを指定するだけです。

```
@use "ディレクトリパス/ファイル名";
```

こうすることでファイル名がそのまま名前空間となります。

また、名前空間は as 節によって変更可能です。

```
@use "font-size" as fs;
@use "breakpoint" as bp;
```

#### 読み込んだファイルの変数などを使う方法

@use で読み込んだファイルの中に記述されている変数や mixin などを使うには、名前空間を使って呼び出す必要があります。<br>
変数はそのまま使えるのではなく、`ファイル名.変数名の形`での記載が必要です。

```
// as節でファイル名を「var」に変更
@use "../../Foundation/import" as var;


// 別ファイルで設定したmixinを呼び出す際に名前空間を使う
.p-text{
  font-family: var.$ff-DM;
  font-size: var.fs(24,32);

  @include var.mq(max,sp){
    // ..
  }
}
```

### @forward 

@forwardは呼び出し先の変数を1つのファイルにまとめて読み込める機能です。<br>
変数を記載したファイル（_variable.scss）やmixinを記載したファイル（_mixin.scss）など、いろんなファイルでよく読み込むファイルをまとめて読み込みたいときに使用します。<br>
@forwardは、Sassのライブラリ作成用のルールと思っていただければ問題ありません。

FLOCSSにおいては、主に**Foundation**にのみ利用する形が基本になります。

#### Foundation内で使用されている@forwardで読み込みしている_index.scssの一例

##### Foundation/_index.scss
```
@forward "reset";

@forward "css-variable";
@forward "base";
@forward "keyframes";
```
##### Foundation/mixin/_index.scss
```
@forward "animation";
@forward "breakpoint";
@forward "common";
@forward "scroll";
@forward "svg";
@forward "easing";
@forward "font-size";
```

### math.div の使い方

除算処理（割り算）で/（スラッシュ）を用いるとエラーになるため、**math.div**を利用する。

```
// NG！ エラーになります。
.p-test{
   width: (100% / 4);
}
```

```
// sassで用意されているmathモジュールを@useで呼び出す
@use "sass:math";

.p-test{
   width: math.div(100%, 4);
}

```

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

#### リセットCSSについて

リセットCSSはブラウザによるデフォルトのスタイルをリセットするcssを定義したものです。

このSCSSでは[destyle.css](https://nicolas-cusan.github.io/destyle.css/)を用いてコーディングを進めています。


#### FLOCSS の基本的な class の書き方※命名規則について

「Layout」の class には **.l-**<br>
「Component」の class には **.c-**<br>
「Project」には **.p-**<br>
「Utility」には **.u-** <br>
というように、対応したプレフィックス（接頭語）をclass に記述する。

これにより、クラス名・ファイル名だけ見ただけでもでもどのようなブロックなのか判断しやすくする。

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

> [!TIP] 
> **Mix**とは Block 自体が持つべきではないレイアウトなどに関する指定を Element を利用して指定する、BEM にとって最も重要なテクニック。<br>
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


### JavaScriptから参照、操作されるクラス名

JavaScriptやjQueryを用いる際には専用のクラス名を用意することで、

#### JavaScriptから参照されるクラス名には「.js-***」

例として、一定距離スクロールした段階で追従するヘッダーを実装する場合は以下のようになります。

##### プレフィックスに「js-」を付けてJavaScriptで操作されることを明示する。
```
<header class="l-header js-header">
  <!-- header -->
</header>
```

```
// .js-headerを参照に処理を与える
const header = document.querySelector('.js-header');
window.addEventListener('scroll', () => {
  if (window.pageYOffset > 100) {
    header.classList.add('is-fixed');
  } else {
    header.classList.remove('is-fixed');
  }
});
```

#### JavaScriptから操作されるクラス名には「.is-***」


```
.l-header{
  position: relative;
  &.is-fixed{
    position: fixed;
    top: 0;
    left: 0;
  }
}

// NG!
.is-***{
  /*.is-*** 単体に直接スタイルは指定しないこと！*/
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
        <a href="" class="p-contactBtn p-contactBtn-orange"> <!--※3-->
          <!-- CONTACT -->
        </a>
      </div>
    </div>
  </div>
</header>
```

### ※1

#### ◆ Layout には位置など、レイアウトの指定のみ定義し、子要素を作らない

#### ◆ JS のクラス名をスタイルで指定しない

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

### ※2

#### ◆ Block には「margin」「position:absolute」などのレイアウトに関わるスタイルを指定せず、Elementに指定する。

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

#### ◆ Block の中で Block を用意する

```
.p-header{
  &__logo{
    margin: 8px 16px;
    width: 240px;
  }
}

.p-logo{
  &__link{
    color: red;
    &:hover{
      color: blue;
    }
  }
}
```
### ※3

#### ◆ Modifierでバリエーションや状態の変化をつくるときに指定する。

```
.p-contactBtn{
  width: 320px;
  height: 48px;
  border-radius: 9999px;
  &-orange{
    background-color: orange;
  }
} 
```

## 参考サイト

https://zenn.dev/wagashi_osushi/books/94efd21a66ccaa

### Dart-Sass

https://www.kannart.co.jp/blog/web-hacks/web-coding/7353/

https://prograshi.com/design/css/use-forward/

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
