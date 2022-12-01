# markuplint-config

[![npm version](https://badge.fury.io/js/%40w0s%2Fmarkuplint-config.svg)](https://www.npmjs.com/package/@w0s/markuplint-config)
[![test status](https://github.com/SaekiTominaga/markuplint-config/actions/workflows/test.yml/badge.svg)](https://github.com/SaekiTominaga/markuplint-config/actions/workflows/test.yml)

個人サイト [w0s.jp](https://w0s.jp/) で使用している markuplint の設定ファイルです。

ひとりで運営している個人サイトに特化した癖のある設定なので、実際のプロジェクトにそのまま導入するのはお勧めしません。多くのケースでは公式の [`markuplint:recommended`](https://next.markuplint.dev/configuration/#properties/extends) を利用するのが良いでしょう。

私はいくつかの理由から `markuplint:recommended` やそれ以外の[プリセット](https://github.com/markuplint/markuplint/tree/main/packages/%40markuplint/config-presets)をそのまま利用することはせず、有用なルールは部分的に取り入れながらも `rules` と `nodeRules` を独自に設定しています。

このドキュメントでは `markuplint:recommended` と異なる部分について**なぜあえて変えているのか**その理由を記しています。もし `markuplint:recommended` の適用に問題があるか、あるいは markuplint の開発者とは別の意見を見てみたいというのであれば本設定ファイルが参考になる部分もあるかもしれません。そのうえで、あなたのプロジェクトに適した部分があれば部分的に取り入れるのが良いと思います。

もちろん熟考のうえであれば本設定ファイルをそのまま `extends` しても構いませんし、 Issue や Pull Request による改善提案は歓迎します。

※ markuplint のバージョン 3  に対応しています。バージョン 2 とは一部互換性がありません。

- ▼ [`rules`](#rules)
- ▼ [`nodeRules`](#noderules)

## `rules`

`markuplint:recommended`（バージョン 3.0.0-rc.0 時点）との差異一覧と、その理由を記す。

### `markuplint:recommended` との差異一覧

#### Conformance checking

| ルール名 | 本設定ファイル | `markuplint:recommended` | 差異 |
| - | - | - | - |
| `attr-duplication` | true | true | |
| `deprecated-attr` | true | true | |
| `deprecated-element` | true | true | |
| `disallowed-element` | [] | ["hgroup"] | [⚠](#disallowed-element) |
| `doctype` | true | true | |
| `id-duplication` | true | true | |
| `no-empty-palpable-content` | true | ― | [⚠](#no-empty-palpable-content) |
| `permitted-contents` | true | true | |
| `required-attr` | true | true | |
| `required-element` | false | ― | |

#### Accessibility

| ルール名 | 本設定ファイル | `markuplint:recommended` | 差異 |
| - | - | - | - |
| `label-has-control` | true | true | |
| `landmark-roles` | true | true | |
| `no-refer-to-non-existent-id` | true | true | |
| `require-accessible-name` | true | true | |
| `required-h1` | true | true | |
| `use-list` | false | true | [⚠](#use-list) |
| `wai-aria` | true | true | |

#### Naming Convention

| ルール名 | 本設定ファイル | `markuplint:recommended` | 差異 |
| - | - | - | - |
| `class-naming` | false | ― | |

#### Maintenability

| ルール名 | 本設定ファイル | `markuplint:recommended` | 差異 |
| - | - | - | - |
| `no-hard-code-id` | false | ― | |
| `no-use-event-handler-attr` | true | ― | [⚠](#no-use-event-handler-attr) |

#### Style

| ルール名 | 本設定ファイル | `markuplint:recommended` | 差異 |
| - | - | - | - |
| `attr-value-quotes` | "double" | ― | [⚠](#attr-value-quotes-case-sensitive-attr-name-case-sensitive-tag-name-end-tag) |
| `case-sensitive-attr-name` | "lower" | ― | [⚠](#attr-value-quotes-case-sensitive-attr-name-case-sensitive-tag-name-end-tag) |
| `case-sensitive-tag-name` | "lower" | ― | [⚠](#attr-value-quotes-case-sensitive-attr-name-case-sensitive-tag-name-end-tag) |
| `character-reference` | false | true | [⚠](#character-reference) |
| `end-tag` | true | ― | [⚠](#attr-value-quotes-case-sensitive-attr-name-case-sensitive-tag-name-end-tag) |
| `ineffective-attr` | true | true | |
| `invalid-attr` | accesskey=disallowed & tabindex=-1,0 | autofocus=disallowed & accesskey=disallowed & tabindex=-1,0 | [⚠](#invalid-attr) |
| `no-boolean-attr-value` | false | ― | |
| `no-default-value` | true | ― | [⚠](#no-default-value) |

### 差異のあるルールの理由

#### [`disallowed-element`](https://next.markuplint.dev/rules/disallowed-element)

`markuplint:recommended` では [`markuplint:a11y`](https://github.com/markuplint/markuplint/blob/main/packages/%40markuplint/config-presets/src/preset.a11y.json) において `<hgroup>` 要素が無効にされている。その理由は [ルール策定時の Issue](https://github.com/markuplint/markuplint/issues/276) に書かれているように、2021年時点のアウトラインアルゴリズムの実情に沿ったものらしいが、その後2022年7月に HTML 仕様が改訂され、アウトラインアルゴリズムは廃止された。

そのため、現在では `<hgroup>` 要素を使うデメリットはなくなった。もっとも現状は `role=generic` にマッピングされているため、逆に `<hgroup>` 要素を積極的に使うメリットもないと思う。 `role=group` に変更する議論もあるため、将来的にはメリットが生じる可能性もあるが、現状は古い仕様の解説をしている記事も多く、初心者は正確な使い方を学ぶのが難しいかもしれないため、引き続きプロジェクトルールで `<hgroup>` 要素を禁止し、グルーピングは `<div>` 要素を代替として使用する考え方もあるだろう。

本設定ファイルは基本的に私が自サイトで使うことを前提としているため、 `<hgroup>` 要素の誤用の恐れはなく、 `markuplint:recommended` とは異なり `<hgroup>` 要素の無効化はしないこととする。

一方、日本語サイト（のみ）を運営している関係上、 `<i>` 要素や `<u>` 要素を使う機会は皆無である。また `<embed>` 要素など他にも使わない要素はいくつか存在するが、管理するサイトごとに差異があるため、本設定ファイルでは設定せず、 `extends` する先での上書き指定で対応する。

#### [`use-list`](https://next.markuplint.dev/rules/use-list/)

個人で運営している当サイトではとくに必要なルールではないため無効にしている。

#### [`no-empty-palpable-content`](https://next.markuplint.dev/rules/no-empty-palpable-content)

バージョン 3 で追加された新ルール。

`markuplint:recommended` ではこのルールは有効化されていないが、当サイトでは `<div></div>` のように `<div>` 要素や `<span>` 要素の中身を空にすることはないため有効にしている。

#### [`no-use-event-handler-attr`](https://next.markuplint.dev/rules/no-use-event-handler-attr)

当サイトでは HTML にイベントハンドラー属性を使うことはないため無効にしている。

#### [`attr-value-quotes`](https://next.markuplint.dev/rules/attr-value-quotes), [`case-sensitive-attr-name`](https://next.markuplint.dev/rules/case-sensitive-attr-name), [`case-sensitive-tag-name`](https://next.markuplint.dev/rules/case-sensitive-tag-name), [`end-tag`](https://next.markuplint.dev/rules/end-tag)

Prettier で自動整形しているため、必ずしも markuplint 側で気にする必要はないのだが、一応一般的なフォーマットを設定している。

これら4つのルールはおそらくほとんどのプロジェクトでそのまま導入できる設定であり、異論も少ないと思うので、根拠の説明は省略する。

#### [`character-reference`](https://next.markuplint.dev/rules/character-reference)

前述のとおり当サイトでは [HTML のビルド](https://github.com/SaekiTominaga/w0s.jp/blob/main/node/src/build/html.ts)に際して [jsdom](https://www.npmjs.com/package/jsdom) を使用しているが、その過程で `&quot;` は `"` に変換され、このルールを有効にするとビルド後ファイルがチェックに引っかかってしまうので無効にしている。

#### [`invalid-attr`](https://next.markuplint.dev/rules/invalid-attr)

`markuplint:recommended` では `autofocus` 属性が無効にされている。むやみに使うべきではないが、使用を禁止するほどではないと思うので本設定ファイルでは無効化していない。

`accesskey` 属性の無効化、および `tabindex` 属性の値を -1 と 0 のみに絞る設定は `markuplint:recommended` と同じとしている。

#### [`no-default-value`](https://next.markuplint.dev/rules/no-default-value)

個人的な好みであるが、 `<form method="get">` や `<input type="text" />` といったデフォルト値の属性は省略したいため、このルールは有効設定にしている。

一昔前は CSS セレクターとの兼ね合いで省略しない方が都合良い時代もあったが、 `:not()` 疑似クラスが使える現在では省略して問題が起こるケースはないと思う。

## `nodeRules`

`markuplint:recommended` での設定に関わらず、本設定ファイルで設定している内容についてすべて理由を記す。

### `html`

[`markuplint:a11y`](https://github.com/markuplint/markuplint/blob/main/packages/%40markuplint/config-presets/src/preset.a11y.json) では `lang` 属性が必須に設定されている。本設定ファイルではそれに加えて [OGP](https://ogp.me/) で必要な `prefix` 独自属性を許可する設定をしている。

### `meta[property]`

[OGP](https://ogp.me/) 用に `<meta>` 要素の `property` 属性を許容するようにしている。

[`markuplint:rdfa`](https://github.com/markuplint/markuplint/blob/main/packages/%40markuplint/config-presets/src/preset.rdfa.json) を `extends` しても良いのだが、当サイトでは OGP 用途以外で `property` 属性を使うことはないため、許可する属性値を正規表現で絞るために独自設定としている。

### `div`

一切属性のない `<div>` 要素を配置するべきではないと考えている（`<dl>` 要素の子要素の場合を除く）。複雑なスタイルを表現したい場合、 CSS の都合で wrapper や inner 用の `<div>` 要素を差し込むこともあるだろう。とくに inner 用の場合、 `.component > div` のように指定すればクラス名を設定せずとも機能するが、それはあくまで制作者視点であり、ユーザー視点すなわちユーザースタイルシートの設定やスクレイピング、あるいは単に技術的興味のために HTML ソースコードを閲覧するユーザーに意図が伝わらないのは宜しくない。よってその場合も装飾用であることを伝えるクラス名を設定するべきだ。

そのため [`required-attr`](https://next.markuplint.dev/rules/required-attr) ルールにて `class` 属性を必須としている。本来はクラス名に限らず `<div lang="foo">` や `<div role="foo">` などもあり得るため、「何らかの属性が一つ以上あること」のチェックをしたいところだが、そのようなルール設定ができないため、 `class` 属性の存在チェックとしている。

なお、以下の場合は `class` 属性なしのケースがあるため、上書き設定で `required-attr` を無効にしている。

- `<dl>` 要素の子要素の場合（例外的に一切属性のない `<div>` 要素を許容したい）
- `<object>` 要素の子要素の場合（`<object aria-labelledby="object-label"><div id="object-label">...</div></object>` のように `id` 属性のみを設定したい）

### `abbr`

[`markuplint:a11y`](https://github.com/markuplint/markuplint/blob/main/packages/%40markuplint/config-presets/src/preset.a11y.json) では `title` 属性が必須に設定されている。

HTML 仕様では `<abbr>` 要素における `title` 属性の設定は任意であるが、当サイトにおいては略語の完全表記を提供するケースでのみ `<abbr>` 要素を使用しているため、書き忘れのないよう本設定ファイルでは同じく `title` 属性を必須としている。

### `img`

HTML 仕様では `<img>` 要素の `alt` 属性は必須ではないが、省略できるケースは限定的である。当サイトでは省略するケースは存在しないため、 `alt` 属性を必須に設定している。

### `iframe`

Techniques for WCAG 2.1 の達成基準 4.1.2 における `<iframe>` 要素利用時の達成方法のひとつに [H64: Using the title attribute of the iframe element](https://www.w3.org/WAI/WCAG21/Techniques/html/H64) がある。それに倣い、当サイトでは `<iframe>` 要素の `title` 属性を必須に設定している。

### `object`

`<object>` 要素は様々な外部リソースの埋め込みに使えるため、 `type` 属性と `role` 属性を必須とし、データのタイプを明示するようにしている。さらに `aria-labelledby` も必須として、前述のとおり `<object aria-labelledby="object-label"><div id="object-label">...</div></object>` のような形で支援技術が代替テキストを特定できるようにしている。

基本的に画像は `<img>` 要素、動画は `<video>` 要素といったように極力専用の要素を使うため、 `<object>` 要素の使用箇所は少ないが、複雑な図表を表す画像など、代替テキストをリストや表で構造化したい場合は `<object>` 要素が適していることもある。

### `figcaption ~ table, table:has(~ figcaption)`

HTML 仕様では `<caption>` 要素の使い方として以下の記述がある。

> When a table element is the only content in a figure element other than the figcaption, the caption element should be omitted in favor of the figcaption.

そのため `<table>` 要素が `<figure>` 要素内にあるいくつかのケースについて `<caption>` 要素の使用を禁じる設定をしている。

[`markuplint:html-standard`](https://github.com/markuplint/markuplint/blob/main/packages/%40markuplint/config-presets/src/preset.html-standard.json) にも似たような設定があるが、[使用できるセレクター](https://markuplint.dev/configuration/#properties/node-rules-&-child-node-rules/selector)が限定的なこともあり、仕様どおりのチェックをすることが難しい。そのため条件設定については不正確ではあるが、当サイトに於いてはひとつの `<figure>` 要素内に複数の `<table>` 要素を配置することは今のところなため、とりあえずこれで妥協している。

### `thead th`, `tbody th`

`<thead>` 要素と `<tbody>` 要素内の `<th>` 要素には `scope` 属性を必須としている。複雑な表でなければ `scope` 属性を設定する必要性は薄いが、設定すること自体に大きなコストはかからないため、当サイトでは一律で設定するようにしている。

### `template *`

`<template><a href=""></a></template>` のようなマークアップをすることがあるため、`<template>` 要素内では [`no-empty-palpable-content`](https://next.markuplint.dev/rules/no-empty-palpable-content) と [`require-accessible-name`](https://next.markuplint.dev/rules/require-accessible-name) を無効にしている。
