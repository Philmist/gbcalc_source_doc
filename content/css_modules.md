+++
date = "2016-05-31T19:30:27+09:00"
draft = false
title = "CSS Modulesとは何か"
categories = ["技術解説"]

+++

この文書ではスタイルを指定するのに使用している`CSS Modules`という技術について解説します。

# 通常のCSSの問題点

通常のCSSでは全てのスタイルがグローバルに定義されており基本的にはスコープという概念がありません。そのために、ほとんど全てのスタイルはカスケーディングされ(継承され)てしまい、名前をユニークにすることでしか区別できません。

この場合、部品(コンポーネント)ごとにスタイルを分離して定義したい時に名前が重複しないかをいちいち確かめる必要があり不便になることがあります。idをいちいちユニークにするのは結構な手間です。

それを解決するのがCSS Modulesです。

# CSS Modulesの考えかた

CSS Modulesでは全てのスタイルがローカルなものとして定義されます。つまり名前の重複を気にする必要がありません。したがって部品ごとにスタイルを分離することが可能になります。逆に他の場所で使われているスタイルを使いたい時には明示的に引用しなければなりません。

実際にCSS Modulesで書かれたコードを見てみましょう。

```
/* tables.css */
.base {
    margin-top: 0.2em;
    margin-bottom: 0.2em;
    border-collapse: separate;
    border-spacing: 2px;
    font-size: 100%;
}

.cell_base {
    composes: base;
    padding: 3px 1px;
}

.header {
    composes: cell_base;
    border-bottom: solid 1px #B2B2B2;
    border-right: solid 1px #B2B2B2;
    background: #4D8CC9;
    text-align: center;
    white-space: nowrap;
    font-size: 92%;
    text-align: center;
}


/* summon.css */
.header {
    composes: header from "./tables.css";
}
```

これが基本的なCSS Modulesのコードです。`composes`は他のファイルないし他のクラスから要素を引用することを示しています。Reactで指定して(正確には[react-css-modules](https://github.com/gajus/react-css-modules)を経由して)使う場合には次のようにします。

```
import CSSModules from "react-css-modules";
import styles from "summon.css";

// 中略

class SummonTableHeader extends Component {
  render() {
    return (
      <tr styleName="header">
        <th>順</th>
        <th>選</th>
        <th>鍵</th>
        <th>召喚名</th>
        <th>攻撃力</th>
        <th colSpan="2">加護1</th>
        <th colSpan="2">加護2</th>
        <th>追加・削除</th>
      </tr>
    );
  }
}
SummonTableHeader = CSSModules(SummonTableHeader, styles);
```

こうするとあとはwebpackがよろしくやってくれます。

# どうしてもグローバルにしたい時

どうしてもCSSのクラス等をグローバルに使いたい時もたまに存在します。そういう時は単純にアプリの大元のJavascriptファイルで何も指定せずに`import`すれば良いです。具体的には次のようにします。

```
/* entry.jsx */
// 必要なスタイルシートを読みこむ
import "normalize.css/normalize.css";
import "balloon-css/balloon.css";
import "../css/mplus_webfonts.css";
import "../css/global.css";
```

これだけです。それぞれのファイルがどういう意味合いなのかは別の文書にゆずります。
