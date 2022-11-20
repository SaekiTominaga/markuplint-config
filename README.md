# markuplint-config-w0s

[![npm version](https://badge.fury.io/js/markuplint-config-w0s.svg)](https://badge.fury.io/js/markuplint-config-w0s)

個人サイト [w0s.jp](https://w0s.jp/) で使用している markuplint の設定ファイルです。

ひとりで運営している個人サイトに特化した設定なので、実際のプロジェクトにそのまま導入するのはお勧めしません。多くのケースでは [`markuplint:recommended`](https://markuplint.dev/configuration/#properties/extends) を利用するのが良いでしょう。

一方で、このドキュメントでは `markuplint:recommended` と異なる部分について**なぜあえて変えているのか**を記しています。もし `markuplint:recommended` の設定に不満があるか、あるいはセカンドオピニオン的な意味で markuplint の開発者とは別の意見を見てみたいというのであれば本設定ファイルが参考になる部分もあるかもしれません。そのうえで、あなたのプロジェクトに適した部分があれば部分的に取り入れるのが良いと思います。

もちろん熟考のうえであれば本設定ファイルをそのまま `extends` しても構いませんし、 Issue や Pull Request による改善提案は歓迎します。

## `rules`

`markuplint:recommended`（バージョン 2.11.1 時点）との差異があるルールについて理由を記す。

### [`character-reference`](https://markuplint.dev/rules/character-reference)

当サイトでは [HTML のビルド](https://github.com/SaekiTominaga/w0s.jp/blob/main/node/src/build/html.ts)に際して [jsdom](https://www.npmjs.com/package/jsdom) を使用しているが、その過程で `&quot;` は `"` に変換され、このルールを有効にするとビルド後ファイルがチェックに引っかかってしまうので無効にしている。

### [`disallowed-element`](https://markuplint.dev/rules/disallowed-element)

`markuplint:recommended` では `<hgroup>` 要素が無効にされている。その理由は [ルール策定時の Issue](https://github.com/markuplint/markuplint/issues/276) に書かれているように、2021年時点のアウトラインアルゴリズムの実情に沿ったものらしいが、その後2022年7月に HTML 仕様が改訂され、アウトラインアルゴリズムは廃止された。

そのため、現在では `<hgroup>` 要素を使うデメリットはなくなった。もっとも現状は `role=generic` にマッピングされているため、逆に `<hgroup>` 要素を積極的に使うメリットもないと思う。 `role=group` に変更する議論もあるため、将来的にはメリットが生じる可能性もあるが、現状は古い仕様の解説をしている記事も多く、初心者は正確な使い方を学ぶのが難しいかもしれないため、引き続きプロジェクトルールで `<hgroup>` 要素を禁止し、グルーピングは `<div>` 要素を代替として使用する考え方もあるだろう。

本設定ファイルは基本的に私が自サイトで使うことを前提としているため、 `<hgroup>` 要素の誤用の恐れはなく、 `markuplint:recommended` とは異なり `<hgroup>` 要素の無効化はしないこととする。

一方、日本語サイト（のみ）を運営している関係上、 `<i>` 要素や `<u>` 要素を使う機会は皆無である。また `<embed>` 要素など他にも使わない要素はいくつか存在するが、管理するサイトごとに差異があるため、本設定ファイルでは設定せず、 `extends` する先での上書き指定で対応する。

### [`invalid-attr`](https://markuplint.dev/rules/invalid-attr)

`markuplint:recommended` では `autofocus` 属性が無効にされている。むやみに使うべきではないが、使用を禁止するほどではないと思うので本設定ファイルでは無効化していない。

`accesskey` 属性の無効化、および `tabindex` 属性の値を -1 と 0 のみに絞る設定は `markuplint:recommended` と同じとしている。

### [`no-use-event-handler-attr`](https://markuplint.dev/rules/no-use-event-handler-attr)

HTML にイベントハンドラー属性を使うことはないため無効にしている。

### [`attr-equal-space-after`](https://markuplint.dev/rules/attr-equal-space-after), [`attr-equal-space-before`](https://markuplint.dev/rules/attr-equal-space-before), [`attr-spacing`](https://markuplint.dev/rules/attr-spacing), [`attr-value-quotes`](https://markuplint.dev/rules/attr-value-quotes), [`case-sensitive-attr-name`](https://markuplint.dev/rules/case-sensitive-attr-name), [`case-sensitive-tag-name`](https://markuplint.dev/rules/case-sensitive-tag-name), [`end-tag`](https://markuplint.dev/rules/end-tag)

Prettier で自動整形しているため、必ずしも markuplint 側で気にする必要はないのだが、一応一般的なフォーマットを設定している。

これら7つのルールはおそらくほとんどのプロジェクトでそのまま導入できる設定であり、異論も少ないと思うので、根拠の説明は省略する。

### [`no-boolean-attr-value`](https://markuplint.dev/rules/no-boolean-attr-value)

真偽属性は `required=""` のように、空の値を明示するフォーマットとしたいため、このルールは無効設定にしている。これは以下の理由による。

- 前述のとおり当サイトでは [HTML のビルド](https://github.com/SaekiTominaga/w0s.jp/blob/main/node/src/build/html.ts)に際して [jsdom](https://www.npmjs.com/package/jsdom) を使用しているが、その過程でこのフォーマットに変換される。
- 個人的に XML 構文が好みであるため、ビルド前の編集ファイルも極力 Well Formed に近い状態にしておきたい。

### [`no-default-value`](https://markuplint.dev/rules/no-default-value)

個人的な好みであるが、 `<form method="get">` や `<input type="text" />` といったデフォルト値の属性は省略したい。

一昔前は CSS セレクターとの兼ね合いで省略しない方が都合良い時代もあったが、 `:not()` 疑似クラスが使える現在では省略して問題が起こるケースはないと思う。

### `markuplint:recommended` との差異一覧

#### Conformance checking

| ルール名 | 本設定ファイル | `markuplint:recommended` |
| - | - | - |
| `attr-duplication` | true | true |
| `character-reference` | false | true |
| `deprecated-attr` | true | true |
| `deprecated-element` | true | true |
| `disallowed-element` | [] | ["hgroup"] |
| `doctype` | true | true |
| `id-duplication` | true | true |
| `ineffective-attr` | true | true |
| `invalid-attr` | accesskey=disallowed & tabindex=-1,0 | autofocus=disallowed & accesskey=disallowed & tabindex=-1,0 |
| `permitted-contents` | true | true |
| `required-attr` | true | true |
| `required-element` | false | false |

#### Accessibility

| ルール名 | 本設定ファイル | `markuplint:recommended` |
| - | - | - |
| `landmark-roles` | true | true |
| `no-refer-to-non-existent-id` | true | true |
| `require-accessible-name` | true | true |
| `required-h1` | true | true |
| `use-list` | false | false |
| `wai-aria` | true | true |

#### Naming Convention

| ルール名 | 本設定ファイル | `markuplint:recommended` |
| - | - | - |
| `class-naming` | false | false |

#### Maintenability

| ルール名 | 本設定ファイル | `markuplint:recommended` |
| - | - | - |
| `no-hard-code-id` | false | false |
| `no-use-event-handler-attr` | true | false |

#### Style

| ルール名 | 本設定ファイル | `markuplint:recommended` |
| - | - | - |
| `attr-equal-space-after` | "never" | false |
| `attr-equal-space-before` | "never" | false |
| `attr-spacing` | width=1 | false |
| `attr-value-quotes` | "double" | false |
| `case-sensitive-attr-name` | "lower" | false |
| `case-sensitive-tag-name` | "lower" | false |
| `end-tag` | true | false |
| `indentation` | false | false |
| `no-boolean-attr-value` | false | true |
| `no-default-value` | true | false |

## `nodeRules`

`markuplint:recommended`（バージョン 2.11.1 時点）にない本設定ファイル独自の設定内容について理由を記す。

### `html`

`markuplint:recommended` では `lang` 属性を必須に設定している。本設定ファイルではそれに加えて [OGP](https://ogp.me/) で必要な `prefix` 独自属性を許可する設定をしている。

### `div`

一切属性のない `<div>` 要素ほど意味のないものはないため、 [`required-attr`](https://markuplint.dev/rules/required-attr) ルールにて `class` 属性を必須としている。本来は `<div lang="foo">` や `<div role="foo">` などもあり得るため、「何らかの属性が一つ以上あること」のチェックをしたいところだが、現状はたまたまそのようなことをしていないため `class` 属性の存在チェックで充分である。

ただし、以下の場合は `class` 属性なしとするため `required-attr` を無効にしている。

- `<dl>` 要素の子要素の場合
- `<object>` 要素の子要素の場合（`<object aria-labelledby="object-label"><div id="object-label">...</div></object>` のように `id` 属性のみを設定したい）

### `object`

`<object>` 要素は様々な外部リソースの埋め込みに使えるため、 `type` 属性と `role` 属性を必須とし、データのタイプを明示するようにしている。さらに `aria-labelledby` も必須として、前述のとおり `<object aria-labelledby="object-label"><div id="object-label">...</div></object>` のような形で支援技術が代替テキストを特定できるようにしている。

基本的に画像は `<img>` 要素、動画は `<video>` 要素といったように極力専用の要素を使うため、 `<object>` 要素の使用箇所は少ないが、複雑な図表を表す画像など、代替テキストをリストや表で構造化したい場合は `<object>` 要素が適していることもある。

### `thead th`, `tbody th`

`<thead>` 要素と `<tbody>` 要素内の `<th>` 要素には `scope` 属性を必須としている。複雑な表でなければ `scope` 属性を設定する必要性は薄いが、設定すること自体に大きなコストはかからないため、本サイトでは一律で設定するようにしている。

### `template *`

`<template><a href=""></a></template>` のようなマークアップをすることがあるため、`<template>` 要素内では [`require-accessible-name`](https://markuplint.dev/rules/require-accessible-name) を無効にしている。
