+++
date = "2016-03-30T11:48:45+09:00"
draft = false
title = "ビルドで何をしているか"
categories = ["ビルド", "ソース解説"]

+++

この文書では *ビルド* で実際はいったい何をしているのかを解説します。

# コードの分割、CommonJSのやり方とES6のやり方

コンパイル型の言語、たとえばC++ではコードを分割して書いたのち、
最終的に *コンパイル* と *リンク* をしてひとつの生成物にしています。

Javascriptでも同じことが行なえます。
そのうちのリンク、つまり複数のものをひとつにまとめるツールを *bundler* と呼んでいます。
bundlerで有名なツールには`browserify`と`webpack`の2つがあって
どちらも人気があるようですが、
私は`webpack`の方を選びました。

ところで`webpack`を使って、
あるコードから別のファイルにあるコードを読みこむときにはいくつかの方法があります。
そのうち2つを今回のコードでは使っています。

1つは`CommonJS`のやり方です。これは`module.exports`と`require`を使います。
`module.exports`に外へ出したいもの(変数とかオブジェクトとか)を代入すると、
`require`を使って出されたものを参照できます。
例を示します。

```
// hello.js
var Hello = "Hello!";
module.exports = Hello;

// world.js
var Hello = require("./hello");
console.log(Hello + "World!");
```

もう1つは`ES6`(`ES2015`)のやり方です。
`ES6`という名称は`ECMAScript`の第6版ということから来ています。
これは`import`と`export`を使います。

まず、単純な例から。

```
// hello.js
export var Hello = "Hello!";

// world.js
import { Hello } from "./hello";
console.log(Hello + "World!");
```

ちょっと難しい例。
[MDNのimport](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Statements/import)
を見ながらどうぞ。

```
// hello.js
export var Hello = "Hello!";

// world.js
export default function print_world(s) {
  console.log(s + "World!");
}

// print.js
import * as H from "./hello";
import world from "./world";

world(H.Hello);
```

# JSX、ES6/7、そしてBabel

リンクに相当する`bundler`については説明しました。
今度はコンパイルについて説明します。

今回使用しているreactというライブラリでは`JSX`という文法が推奨されています。
これは次のようにXMLっぽい構文をJavascriptに紛れこませられるものです。
```
class PlayerStats extends Component {
  render() {
    return (
      <table className="grbr" id="info_table">
        <tbody>
          <Rank />
          <ShipBonus />
          <AttributeBonus />
          <HPPercent />
        </tbody>
      </table>
    );
  }
}
```

もちろん、こんな構文をブラウザが理解できるわけがありません。
そこで必要なのが *コンパイラ* です。
コンパイラはこのような構文をブラウザが解釈できる構文に変換します。

`Babel`というコンパイラは、
まだブラウザで実装されていないけれど最新の仕様では実装されている(`ES6`とか`ES7`)、
というような構文を対象のブラウザが解釈できる構文に変換することも出来ます。
`Babel`は`webpack`から呼びだして実行することが可能なので、私はそうしています。

# 結局webpackは何をしているか

`webpack`それ自体はリンクとコンパイルをしています。つまり次のようなことをしています。

1. エントリポイントとして指定されたJavascriptファイルを探す
1. 指定されたファイルから呼びだされているファイルを順々に探す
1. Javascriptファイルを`Babel`(正確には`babel-loader`)に渡してコンパイルする
1. 全てのファイルを1つにまとめる

こうすることで、好きな構文を使って色々なブラウザで動くJavascriptを簡単に書けるわけです。

なお、`webpack`の設定ファイルは`webpack.config.js`になります。
