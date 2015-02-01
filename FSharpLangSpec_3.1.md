# F# 3.1 言語仕様(ワーキングドラフト)

> 原文： http://fsharp.org/specs/language-spec/3.1/FSharpSpec-3.1-working.docx

> 注釈：この文章はMicrosoftResearchおよびMicrosoftDeveloperDivisionによって
> 2013年6月に作成された、F# 3.1リリース向けの言語仕様です。

この言語仕様には3.1の実装と一致しない箇所がある場合があります。
文章中ではそれらの該当箇所にコメントの形でなるべく注釈を加えてあります。
言及のない不一致を見つけた場合は是非連絡してください。
そうすれば将来リリースされる言語仕様ではそれとわかるようにさせていただきます。
F#の開発チームはいつでもこの言語仕様、およびF#の設計や実装に対する
フィードバックを歓迎します。
フィードバックを送信するには、
http://github.com/fsharp/fsfoundation/docs/language-spec
にIssueをオープンしたり、コメントを追加したり、
Pull Requestを送信したりといった方法があります。

言語仕様の最新版は [fsharp.org](http://fsharp.org/) にあります。
これまでのドキュメントに対するF# ユーザーコミュニティからのフィードバックには
大変感謝しています。

この仕様書の一部ではC# 4.0やUnicode、IEEEといった仕様への言及があります。

**著者：**
Don Syme および補佐として Anar Alimov, Keith Battocchi, Jomo Fisher,
Michael Hale, Jack Hu, Luke Hoban, Tao Liu, Dmitry Lomov,
James Margetson, Brian McNamara, Joe Pamer, Penny Orwick,
Daniel Quirk, Kevin Ransom, Chris Smith, Matteo Taveggia,
Donna Malayeri, Wonseok Chae, Uladzimir Matsveyeu, Lincoln Atkinson
等による。

**警告**

_© 2005-2013 Microsoft Corporation and contributors. [Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0.html)ライセンスの下に利用出来ます。_

_Microsoft, Windows, Visual F#はアメリカ合衆国 Microsoft Corporationの商標、あるいはアメリカ合衆国内外における登録商標です。_

_文章内で言及されるその他の製品や会社名にはそれぞれ固有の所有者が存在する場合があります。_

**更新履歴**

* 2014年 5月 バージョン番号をF# 3.1に変更
* 2013年 6月 F# 3.1用の最初の更新 ([言語のアップデートに関するオンラインの議論](http://blogs.msdn.com/b/fsharpteam/archive/2013/06/27/announcing-a-pre-release-of-f-3-1-and-the-visual-f-tools-in-visual-studio-2013.aspx)を参照)
* 2012年 9月 F# 3.0用の更新
* 2012年 8月 書式の更新
* 2011年12月 文法の要約を更新
* 2011年 2月 用語集と索引の更新、書式の修正
* 2010年 8月 用語集と索引の更新、書式の修正

## 目次

// TODO

## 1. イントロダクション

F#はスケーラブルで簡潔かつ型安全で、型推論も行われ、実行効率もよい
関数型/宣言型/オブジェクト指向のプログラミング言語です。
この言語は.NET FrameworkおよびEcma 335 Common Language Infrastructure(CLI: 共通言語基盤)
の実装上で動作する、最初の型付き関数型プログラミング言語となるものです。
F#は部分的にOCaml言語から影響を受けており、一般的なコア部分においてOCamlと共通するものもあります。

### 1.1 最初のプログラム

以降のセクションでは、小さなF#プログラムを紹介して、
F#の重要な機能を説明していきます。
F#の最初の一歩として、以下のプログラムを見てみましょう：

```fsharp
let numbers = [ 1.. 10 ]

let square x = x * x

let squares = List.map square numbers

printfn "N^s = %A" squares
```

このプログラムは以下の方法で試すことができます：

* Visual Studioなどの開発環境上にて、プロジェクトとしてコンパイルする
* F#のコマンドラインコンパイラ`fsc.exe`を手動で実行する
* F#の配布物に同梱されている、動的コンパイラであるF# Interactiveを使用する

#### 1.1.1 軽量構文

F#言語では、軽量構文と呼ばれる、単純かつインデント重視の構文機能を使用します。
先ほどのセクションにあったサンプルのプログラムには一連の宣言文が並んでおり、
いずれも同じ列に揃えられていました。
たとえば以下のコードの2行はそれぞれ別の宣言文です：

```fsharp
let squares = List.map square numbers

printfn "N^2 = %A" squares
```

軽量構文はF#の構文の主要な部分すべてにおいて通用します。
次の例にあるコードは正しく整列されていません。
宣言文は1行目から始まって2行目以降に続いています。
そのため、2行目以降は同じ列に整列しなければいけません：

```text
let computeDerivative f x =
    let p1 = f (x - 0.05)
  let p2 = f (x + 0.05)
       (p2 - p1) / 0.1
```

正しく整列すると以下のようになります：

```fsharp
let computeDerivative f x =
    let p1 = f (x - 0.05)
    let p2 = f (x + 0.05)
    (p2 - p1) / 0.1
```

軽量構文は`.fs`, `.fsx`, `.fsi`, `.fsscript`の拡張子を持ったF#コードファイルにおける標準の構文です。

#### 1.1.2 データを簡単にする

サンプルの1行目では、単に1から10までの数のリストを宣言しています。

```fsharp
let numbers = [1 .. 10]
```

F#のリストは不変のリンクリストで、これは関数型プログラミングにおいて拡張可能なデータ型として使用されるものです。
この型には、リストの先頭に1つの要素を追加する`::`演算子や、
2つのリストを連結する`@`演算子といったものが用意されています。
F# Interactive上でこれらの演算子を試してみると以下のようになります：

```text
> let vowels = ['e'; 'i'; 'o'; 'u'];;
val vowels: char list = ['e'; 'i'; 'o'; 'u']

> ['a'] @ vowels;;
val it: char list = ['a'; 'e'; 'i'; 'o'; 'u']

> vowels @ ['y'];;
val it: char list = ['e'; 'i'; 'o'; 'u'; 'y']
```

F# Interactive上では2つのセミコロンで行が区切られることに注意してください。
また、それぞれの結果が変数ではなく不変の値だということを表す
_val_という文字がF# Interactive上で出力されている点にも注意してください。

F#にはモデル化処理やデータ操作を簡単に行うことが出来るように、
タプルやオプション、レコード、共用体、シーケンス式といった、
きわめて効率のよいテクニックをサポートする機能が揃えられています。
タプルは順序つきの値のコレクションで、アトミックな単位で扱う事ができます。
多くの言語において、関連のある一連の値を1つのエンティティとしてあちこちに渡したい場合、
クラスやレコードなどのような名前の付いた型を用意して、そこに値を格納することになります。
一方、タプルを利用する場合には関連する値をグループとしてまとめるだけでよく、
新しく型を用意する必要はありません。

タプルは各コンポーネントをカンマ区切りに記述して定義します。

```text
> let tuple = (1, false, "text");;
val tuple : int * bool * string = (1, false, "text")

> let getNumberInfo (x : int) = (x, x.ToString(), x * x);;
val getNumberInfo : int -> int * string *int

> getNumberInfo 42;;
val it : int * string * int = (42, "42", 1764)
```

F#の重要なコンセプトは**不変性(immutability)**です。
タプルやリストはF#における多数の不変な型のうちの1つです。
実際、F#における多くの要素が標準的には不変です。
不変性とは、一度値が作成されてそれに名前が付けられると、
その名前に関連づけられた値を変えることができないということです。
不変性には利点がいくつかあります。
最も顕著なものは、いくつかのパターンのバグを防ぐ事ができること、
そして不変なデータは本質的にスレッドセーフであり、
並列処理を行うコードを簡単に記述できるようになるという点です。

