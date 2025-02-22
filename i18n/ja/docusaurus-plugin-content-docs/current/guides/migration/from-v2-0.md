---
sidebar_position: 3
---

# v2.0からv2.1への移行

v2.1の主な変更点は、インターフェースを分解するための「ページファースト」という新しいメンタルモデルです。

v2.0では、FSDは分解のためにエンティティ表現やインタラクティビティの最小部分まで考慮し、インターフェース内のエンティティとフィーチャーを特定することを推奨していました。そうしてから、エンティティとフィーチャーからウィジェットやページが構築されていきました。この分解モデルでは、ほとんどのロジックはエンティティとフィーチャーにあり、ページはそれ自体にはあまり重要性のない構成層に過ぎませんでした。

v2.1では、分解をページから始めること、または場合によってはページで止めることを推奨します。ほとんどの人はすでにアプリを個々のページに分ける方法を知っており、ページはコードベース内のコンポーネントを見つける際の一般的な出発点でもあります。この新しい分解モデルでは、各個別のページにほとんどのUIとロジックを保持し、Sharedに再利用可能な基盤を維持します。複数のページでビジネスロジックを再利用する必要が生じた場合は、それを下層のレイヤーに移動できます。

Feature-Sliced Designへのもう一つの追加は、`@x`表記を使用したエンティティ間のクロスインポートの標準化です。

## 移行方法 {#how-to-migrate}

v2.1には破壊的な変更はなく、FSD v2.0で書かれたプロジェクトもFSD v2.1の有効なプロジェクトです。しかし、新しいメンタルモデルがチームや特に新しい開発者のオンボーディングにとってより有益であると考えているため、分解に対して小さな調整を行うことを推奨します。

### スライスのマージ

移行を始めるための簡単な方法は、プロジェクトでFSDのリンターである[Steiger][steiger]を実行することです。Steigerは新しいメンタルモデルで構築されており、最も役立つルールは次のとおりです。

- [`insignificant-slice`][insignificant-slice] — エンティティ、またはフィーチャーが1ページでのみ使用されている場合、このルールはそのエンティティ、またはフィーチャーをページに完全にマージすることを提案します。
- [`excessive-slicing`][excessive-slicing] — レイヤーにスライスが多すぎる場合、通常は分解が細かすぎるサインです。このルールは、プロジェクトのナビゲーションを助けるためにいくつかのスライスをマージ、またはグループ化することを提案します。

```bash
npx steiger src
```

これにより、1回だけ使用されるスライスを特定できるため、それらが本当に必要か再考することができます。そのような考慮において、レイヤーはその内部のすべてのスライスのための何らかのグローバル名前空間を形成することを念頭に置いてください。1回だけ使用される変数でグローバル名前空間を汚染しないようにするのと同様に、レイヤーの名前空間内の場所を貴重なものとして扱い、慎重に使用するべきです。

### クロスインポートの標準化

以前にプロジェクト内でクロスインポートがあった場合、Feature-Sliced Designでのクロスインポートのための新しい表記法`@x`を活用できます。これは次のようになります。

```ts title="entities/B/some/file.ts"
import type { EntityA } from "entities/A/@x/B";
```

詳細については、リファレンスの[クロスインポートの公開API][public-api-for-cross-imports]セクションを参照してください。

[insignificant-slice]: https://github.com/feature-sliced/steiger/tree/master/packages/steiger-plugin-fsd/src/insignificant-slice
[steiger]: https://github.com/feature-sliced/steiger
[excessive-slicing]: https://github.com/feature-sliced/steiger/tree/master/packages/steiger-plugin-fsd/src/excessive-slicing
[public-api-for-cross-imports]: /docs/reference/public-api#public-api-for-cross-imports
