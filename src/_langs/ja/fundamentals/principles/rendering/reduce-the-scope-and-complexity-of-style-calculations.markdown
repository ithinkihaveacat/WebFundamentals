---
layout: article
title: "Reduce the scope and complexity of style calculations"
description: "要素の追加と削除を通じた DOM の変更、アニメーションを通じた属性とクラスの変更を行うと、ブラウザで要素スタイルの再計算が発生するほか、多くの場合、ページ全体またはページの一部のレイアウト（またはリフロー）が発生します。 このプロセスはスタイル計算と呼ばれます。"
introduction: "要素の追加と削除を通じた DOM の変更、アニメーションを通じた属性とクラスの変更を行うと、ブラウザで要素スタイルの再計算が発生するほか、多くの場合、ページ全体またはページの一部のレイアウト（またはリフロー）が発生します。 このプロセスは<em>スタイル計算</em>と呼ばれます。"
article:
  written_on: 2015-03-20
  updated_on: 2015-03-20
  order: 1
collection: rendering-performance
priority: 0
authors:
  - paullewis
notes:
  components:
    - "ウェブ コンポーネントの場合、スタイル計算の動作が若干異なることに十分留意してください。デフォルトでは、スタイルは Shadow DOM の境界をまたがず、その適用範囲はツリー全体ではなく個々のコンポーネントに制限されるためです。 ただし、全般的には同じ概念が適用されます。つまり、小さいツリーと単純なルールは、大きいツリーまたは複雑なルールよりも効率的に処理されます。"
  bodystylechange:
    - "従来、たとえば、ボディ要素上のクラスを変更した場合、ページ内のすべての子要素のスタイルを再計算する必要がありました。 好都合なことに、現在は状況が異なります。一部のブラウザは各要素に固有の小さいルール集合を保守しており、要素が変更された場合、その要素のスタイルが再計算されます。 つまり、ツリー内での要素の位置、および変更された実際の対象に応じて、要素を再計算する必要のない場合があります。"

key-takeaways:
  - セレクターの複雑性を軽減します。BEM のようなクラス中心の方法論を使用してください。
  - スタイル計算を実行する必要のある要素の数を減らします。

---
{% wrap content%}

{% include modules/takeaway.liquid list=page.key-takeaways %}

スタイル計算の最初の段階では、適合セレクターの集合を作成します。つまり、特定の要素に適用されるクラス、疑似セレクター、および ID をブラウザによって確定します。

このプロセスの次の段階では、適合セレクターからすべてのスタイル ルールを取り出し、要素の最終的なスタイルを確定します。 Blink (Chrome と Opera のレンダリング エンジン) では、現在少なくとも、これらのプロセスのコストはほぼ同じです。

<div class="quote" style="margin-top: 30px;">
  <div class="container">
    <blockquote class="quote__content g-wide--push-1 g-wide--pull-1 g-medium--push-1">要素のスタイル計算に必要な時間の約 50% は適合セレクターを見つけるのに使用され、残りの時間は適合ルールから RenderStyle (計算されたスタイル表現) を構築するのに使用されます。
    <p>Rune Lillesveen, Opera / <a href="https://docs.google.com/document/d/1vEW86DaeVs4uQzNFI5R-_xS9TcS1Cs_EUsHRSgCHGu8/edit">Style Invalidation in Blink</a></p>
    </blockquote>
  </div>
</div>


## セレクターの複雑性の軽減

もっとも単純な場合、CSS 内でクラスのみを使用して要素を参照します。

{% highlight css %}
.title {
  /* styles */
}
{% endhighlight %}

しかしながら、プロジェクトの拡張に伴って、CSS はより複雑になり、最終的には次のようなセレクターを使用することになります。

{% highlight css %}
.box:nth-last-child(-n+1) .title {
  /* styles */
}
{% endhighlight %}

スタイルを適用する必要があることを認識するために、実際、ブラウザは「これはタイトルのクラスを持つ要素のなのか。そのタイトルは n 番下の子である親と、ボックスのクラスがある 1 つの要素を持っているのか。」といった質問をしなければなりません。このような質問に答えるには、使用するセレクターと問題のブラウザに応じて、長い時間がかかる可能性があります。 セレクターの意図する動作はクラスに変更されることもあります。

{% highlight css %}
.final-box-title {
  /* styles */
}
{% endhighlight %}

クラスの名前を変更することもできますが、処理はブラウザにとってより単純になりました。 以前のバージョンでは、たとえば、特定の要素がその型の最後の要素であることを知るために、ブラウザはその他の全要素に関する情報を認識し、それらの要素が特定の要素に後続する要素 (n 番目の最後の子要素) かどうかを確定する必要がありました。この処理は、クラスが一致することに基づいて単にセレクターを要素に適合するよりも、ずっと高価なものです。

##スタイル計算が実行される要素の数を減らす
パフォーマンスに関するもう 1 つの考慮事項 (通常、数多くのスタイル更新にとってより重要な要因) は、要素の変更時に実行する必要のある作業の総量です。

スタイル計算では、各要素を少なくとも 1 回は全スタイルとチェックして、スタイルが一致するかどうかを確認する必要があります。そのため、一般的に、要素のスタイル計算を実行するときの最悪ケースのコストは、要素の数にセレクターの数を掛けた積に比例します。

{% include modules/remember.liquid title="Note" list=page.notes.bodystylechange %}

ページ全体を無効にする代わりに、通常、少数の要素をスタイル計算の直接対象とすることができます。 ブラウザは必ずしも変更によって潜在的に影響を受けるすべての要素をチェックする必要はないため、最近のブラウザでは、これは大きな問題にはなりません。 一方、古いブラウザはこのような処理に対して最適化されていない場合があります。 可能な場合は、**無効化される要素の数を減らす**必要があります。

{% include modules/remember.liquid title="Note" list=page.notes.components %}

## スタイル再計算コストの計測
 スタイル再計算のコストを計測する最も容易で最適な方法は、Chrome DevTool のタイムライン モードを使用することです。 最初に、DevTool を開き、タイムライン タブに移動し、レコーディングを開始してサイトを操作します。 レコーディングを停止すると、下記のような画像がが表示されます。

<img src="images/reduce-the-scope-and-complexity-of-style-calculations/long-running-style.jpg" class="g--centered" alt="長時間実行されているスタイル計算を示す DevTool">

最上部の細長い帯は 1 秒あたりのフレーム数を示します。バーが下部の線 (60fps の線) より上に表示されている場合、長時間実行されているフレームがあります。

<img src="images/reduce-the-scope-and-complexity-of-style-calculations/frame-selection.jpg" class="g--centered" alt="Chrome DevTool でのトラブル領域の拡大表示">

スクロールのような特定のインタラクション、または他のインタラクションの間に、長時間実行されているフレームがある場合、そのフレームを詳細に調査する必要があります。

上記の例のように、大きい紫色のブロックがある場合は、[レコード] をクリックすると、詳細情報が表示されます。

<img src="images/reduce-the-scope-and-complexity-of-style-calculations/style-details.jpg" class="g--centered" alt="長時間実行されているスタイル計算の詳細の表示">

この例では、長時間 (18ms 超にわたり) 実行されているスタイル再計算イベントがあります。偶然、このイベントはスクロールの最中に発生しており、ユーザは明確な激しい震動を感じることになります。

イベント自身をクリックすると、コールスタックが表示され、JavaScript 内でスタイル変更を発生させている部分が指摘されます。 その他に、変更によって影響された要素の数 (この例では 400 要素以上)、およびスタイル計算の実行にかかった時間も表示されます。 この情報を使用して、コード修正の試行を開始できます。

## BEM (Block, Element, Modifier) の使用
[BEM (Block, Element, Modifier)] 方式のコーディングへのアプローチ(https://bem.info/) は実際にセレクターに埋め込まれ、上記のパフォーマンス利点の実現に役立ちます。その理由は、すべてのものが単一のクラスを持つことが推奨され、階層が必要な場合は、階層がクラス名にも埋め込まれるためです。

{% highlight css %}
.list { }
.list__list-item { }
{% endhighlight %}

若干の修飾子が必要である場合は、最後の子クラスに特別な処理を実行する上記の例のように、そのような修飾子を追加することができます。

{% highlight css %}
.list__list-item--last-child {}
{% endhighlight %}

CSS を編成する適切な方法を探しているならば、スタイル検索の簡略化のためにも、構造の観点からも、BEM は実に最適な出発点になります。

BEM を好まない場合は、CSS にアプローチする他の方法がありますが、パフォーマンスに関する考慮事項をアプローチの動作工学とともに評価する必要があります。

## リソース

* [Style invalidation in Blink](https://docs.google.com/document/d/1vEW86DaeVs4uQzNFI5R-_xS9TcS1Cs_EUsHRSgCHGu8/edit)
* [BEM (Block, Element, Modifier)](https://bem.info/)

{% include modules/nextarticle.liquid %}

{% endwrap%}
