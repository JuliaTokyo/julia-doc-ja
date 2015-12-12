Julia 公式ドキュメント翻訳
==========================

プログラミング言語「Julia」公式ドキュメントの日本語翻訳プロジェクトです。

原文は [docs.julialang.org](http://docs.julialang.org) 、邦訳は [docs.julia.tokyo](http://docs.julia.tokyo) で見ることができます。翻訳は現在、 *release-0.4* のものを元に行っています。翻訳文と直接対応する原文は `_original/` にあります。

ドキュメントは、Read The Docsを使って公開されています （[プロジェクトページ](https://readthedocs.org/projects/julia-doc-ja)）。

## あなたも翻訳してみませんか？

翻訳される方、いつでも募集中です！一部分でも大歓迎です。

もちろん、誤訳や分かりづらい表現に対する指摘も、とてもありがたいです。ぜひ気軽に[イシューを立ててください](https://github.com/JuliaTokyo/julia-doc-ja/issues)！

翻訳は、より多くの方が情報を得るための助けになりますし、また、翻訳者自身の知識習得という面でも非常に有効だと思います。

### 翻訳の手順

まずこのレポジトリを「Fork」し、自分のレポジトリ上で「編集・翻訳」、そしてそれを「Pull Request」として送っていただければ、こちらでチェックをして取り込みます。

現在進行中の翻訳作業については、[Pull Requests](https://github.com/JuliaTokyo/julia-doc-ja/pulls)で確認することができます。

ドキュメントはreStructuredText（rST）形式で書かれています。rSTについては、[Python開発者ガイドのドキュメンテーションに関する章](https://docs.python.org/devguide/documenting.html)（英語）が参考になるでしょう。

プロジェクトのルート・ディレクトリで以下のコマンドを実行すると、rSTファイルをHTMLファイルに変換して、実際の見た目を確認することができます。生成されたHTMLファイルは `_build/html` に置かれます。

```
$ make html
```


### スタイル・ガイド

ある程度の一貫性を保つため、簡単な[翻訳スタイル・ガイド](https://github.com/JuliaTokyo/julia-doc-ja/wiki/%E7%BF%BB%E8%A8%B3%E3%82%B9%E3%82%BF%E3%82%A4%E3%83%AB%E3%83%BB%E3%82%AC%E3%82%A4%E3%83%89)をWikiに用意しています。
