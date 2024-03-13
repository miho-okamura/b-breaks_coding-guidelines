# Viteを用いたコーディング用テンプレート

作成日：2024.01.15 　　更新日：2024.01.15

## BEAUTY-CHAOO開発の上での注意
> [!CAUTION]
> 三号機で静的ソースを確認するうえで、リンクを設置する際にパスが**絶対パス**だとうまくいかないため、**相対パス**で設定してください。


## 開発環境

### コーディング

■Vite ■Sass（SCSS） ■Handlebars


## ディレクトリ構造

```
root
 ├─ dist  //←←←←←←←←←←# dist先  基本に触らない
 │   ├─assets
 │   │   ├─css
 │   │   ├─images
 │   │   └─js
 │   ~~~~~~~~~~~~~~~~~~
 │   └─index.html
 │
 ├─ src
 │   ├─components　//←←←←←# コンポーネントを管理するディレクトリ（handlebars）
 │   │   ├─header.html
 │   │   └─footer.html
 │   ├─scss
 │   │   ├─Foundation
 │   │   │   └─mixin
 │   │   ├─Layout
 │   │   ├─object
 │   │   │   ├─component
 │   │   │   ├─project
 │   │   │   └─Utility
 │   │   └─style.scss
 │   ├─public //←←←←←←←←←# build時無変換で出力される
 │   │   ├─css
 │   │   ├─images //←←←←←←# build時ディレクトリを維持できる
 │   │   └─js
 │   ├─js
 │   │   └─main.js
 │   └─index.html
 │
 ├─ .gitignore
 ├─ package.json //←←←←←←←←←# （プロジェクトのjsonファイル）
 ├─ postcss.config.cjs //←←←←←# （PostCSSの設定ファイル）
 └─ vite.config.js //←←←←←←←# （viteの設定ファイル）
```


## Vite関連

### Viteを用いて行っていること
- 複数のHTMLページ生成
- Handlebarsによる共通パーツの管理（ハンドルバー化）
- SCSSの書き出し
- PostCSSによるCSS最適化
- jsの結合


### Viteの使用方法
1. コントロールパネルなどからこのテンプレートを開き、「npm i」でパッケージをインストールしてください。
2. 「npm vite dev」で開発環境が起動します。
3. 「npm vite build」で「dist」ディレクトリに本番用の静的アセットが生成されます。


#### ■ HTMLを複数出力したい場合
Viteの初期設定では「npm run build」するとHTMLファイルは1つにまとめられてしまうため、HTMLを複数出力したい時には、vite.config.jsに出力するページを追記していく必要がある。

複数出力の詳細⇒[公式リファレンスのマルチページアプリ](https://ja.vitejs.dev/guide/build.html#multi-page-app)

```
export default defineConfig({
  build: {
    rollupOptions: {
      input: { 
        main: resolve(root, 'index.html'),
        nested: resolve(root, 'nested', 'index.html'),
        //以下のように追記していく※重複はしないように
        [任意の名前]: resolve(root, '[ディレクトリ名]', '[ファイル名].html'),
      },
    },
  },
})
```

## HTML


## Sass(Dart-Sass)

2022年ごろのSass（LibSass）で使用されていた@importが廃止されるにあたり、Dart-Sass用にSassを書き換えました。

こちらでは@importではなく、@use、@forwardを用いてコンパイル等を行います。


## CSS 設計

大まかなベースは[FLOCSS](https://github.com/hiloki/flocss)を利用。

命名は [BEM](https://en.bem.info/methodology/quick-start) を利用。



### ◆ FLOCSSについて※重要箇所のピックアップ

以下を見る前に一度[FLOCSS](https://github.com/hiloki/flocss)に目を通してください。


#### FLOCSSのレイヤー構成について
- **Foundation** ・・・ Reset.cssやNormalize.cssなどを用いたブラウザのデフォルトスタイルの初期化や、プロジェクトにおける基本的なスタイルを定義
- **Layout** ・・・ ページを構成するヘッダーやメインのコンテンツエリア、サイドバーやフッターといったプロジェクト共通のコンテナーブロックのスタイルを定義
- **Object** ・・・ プロジェクトにおける繰り返されるビジュアルパターンをすべてObjectと定義。次の3つのレイヤーに分ける。
  - **Component** ・・・ 再利用できるパターンとして、小さな単位のモジュールを定義
  - **Project** ・・・ プロジェクト固有のパターンであり、いくつかのComponentと、それに該当しない要素によって構成されるもの
  - **Utility** ・・・ わずかなスタイルの調整のための便利クラスなどを定義


#### FLOCSSの基本的なclassの書き方※命名規則について

「Layout」のclassには __.l-__<br>
「Component」のclassには __.c-__<br>
「Project」には __.p-__<br>
「Utility」には __.u-__ <br>
というように、フォルダの頭文字をclassに記述する。

>  [!WARNING]
> 「Object」レイヤーはComponent、Project、Utilityの親レイヤーのため、 ".o-"から始まるクラスはFLOCSSのルール違反になります。<br>
> 必ず".p-"か".c-"、".u-"のいずれかにしてください。


> [!IMPORTANT]
>また、基本的に「Object」ディレクトリ直下にファイルは作らないようにしてください。ここのディレクトリ直下にあるファイルはコンパイル用のstyle.scssのみにしてください。<br>
> 例外は".p-"などのファイル数が少なく、混在させても管理が容易（ディレクトリでの整理が不要）の場合のみです。


### BEMについて

以下を見る前に一度[BEM](https://en.bem.info/methodology/quick-start) / [BEM (日本語翻訳版)](https://github.com/juno/bem-methodology-ja/blob/master/definitions.md)に目を通してください。

#### BEMの概念
BEMはブロック（Block）、エレメント（Element）、モディファイア（Modifier）の頭文字をとったものです。<br>
Blockはあるパーツ（コンポーネント）の親要素です。BlockはElementと呼ばれる子孫要素を持つことができます。<br>
Modifierはバリエーションや状態を変化させるときに指定するもので、BlockかElementと同階層に指定します。
- .Block
- .Block__Element
- .Block-Modifier
- .Block__Element-Modifier

#### IDは使わない
CSSでスタイルを適用する際にIDを使用しない。<br>
IDはjQueryやjavascriptなどの動的パーツに用いる。
```
<nav class="p-gNav l-gNav" id="js-nav">
  <div class="p-gNav__cont">
    <!-- メニュー -->
  </div>
</nav>
```
```
.p-gNav{
  width: 100%;
  height: 100%;
  background-color: #f6f6f6;
}

.l-gNav{
  position: fixed;
  left: 0;
  top: 0;
}
```

#### スタイル（css）は全てclassに対して指定する（要素に対して直にスタイルを指定しない）
要素を用いることによって、詳細度が上がってしまい他のスタイルを上書きしてしまう。<br>
BEMでは詳細度を均一に保つというルールがあるため、極力要素は書かないこと。

#### Blockには「margin」「position:absolute」などのレイアウトに関わるスタイルを指定しない ※MIXについて

Blockは使い回しが可能なパーツとして考えるため、レイアウトに関わるBlockに指定しないこと。

例えばボタンだと、見た目は使い回したいが位置や余白の距離が違う等の場合がある。<br>
この場合、ボタンに2つのクラスを指定する。見た目のスタイルをBlock、位置や余白のスタイルをElementに指定する。<br>
これはBEMでは**Mix**と言う。

> [!TIP]
> **Mix**とはBlock自体が持つべきではないレイアウトなどに関する指定をElementを利用して指定する、BEMにとって最も重要なテクニック。<br>
>特に意識してコーディングをお願いします。
```
// 例

<section class="p-section"><!-- ☆Block -->
  <div class="p-section__wrap"><!-- ★Element -->
    <div class="p-section__txt"><!-- ★Element -->
      <p>テストテキスト</p>
    </div>
    <div class="c-card p-section__btn"><!-- ☆Block + ★Element の Mix -->
      <h2 class="c-btn__ttl"><!-- ★Element -->
        
      </h2>
    </div>
  </div>
</section>
```

上の例だと、.c-btnが**Block**、.p-section__btnが**Elements**になる。<br>
この場合、 **Elements**である.p-section__btn側で位置や余白の調整の記述を追加する。<br>
こうすることにより、**Block**の再利用性を高くする。

#### Modifier（バリエーション）
**Modifier**はBlockとElementのバリエーションや状態の変化をつくるときに指定します。
```
// 例

<div class="p-box">
  <div class="p-box__btn">
    <a href="#" class="c-btn p-box__btn-item">button</a>
  </div>
</div>
```

### ★ ハウスルール

ここでは上記のFLOCSS、BEMにはないルールを記述している。

上記ルールの場合class名が長くなってしまうため、追加したルール。



#### Blockの命名で単語と単語を繋げたい場合、ケバブケースではなくキャメルケース（ローワーキャメルケース）で表記する。


##### ◆ ケバブケース
```
/* △  BlockかElementか分かりにくい */

.p-hero-slider {}

.p-hero-catch-txt {}
```
##### ◆ キャメルケース（ローワーキャメルケース）
```
/* 〇　一目でBlockと分かる */

.p-heroSlider {}

.p-heroCatch {}
```


#### アンダースコアは２つ（\_\_）、ハイフンは１つ（-）。

```
/*  */
.p-header__container {}

/*  */
.p-header-logo {}
```


#### 極力、3 つ以上単語をつなげるのは控える。

```
/* △　アンスコ2つが続いているので冗長かつ読みにくい。 */

.p-header__nav__list-item {}


/* 〇　新しいBlockをつくる。 */

.p-headerNav__list-item {}


/* ◎　わかりやすければ単語を略しても良い。 */

.p-hNav__list-item {}
```





## 参考サイト

### CSSガイドライン

https://github.com/manabuyasuda/styleguide/blob/master/css-styleguide.md

### FLOCSS

https://github.com/hiloki/flocss


### BEM

https://fukulog.net/bem/