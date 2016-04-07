+++
date = "2016-03-30T20:49:09+09:00"
draft = false
title = "短いreact紹介と補足"
categories = ["技術解説"]

+++

この文書では計算機アプリの根幹で使われているReactについて簡単に解説するとともに、
公式チュートリアルでは触れられておらず、今回使用した部分について少しだけ解説します。
なお、ちゃんとした解説は
[公式のチュートリアル](https://facebook.github.io/react/docs/tutorial-ja-JP.html)
を見たほうが良いです。

# Reactとは

Reactとはhtmlな画面をいくつかの部品に分けて書くことができるライブラリです。
それだけなら単に文字列を組みあわせて表示させればいいのですが、
Reactのすごいところは **変更された部分だけを再描画する** ことです。

例えばある部品(コンポーネント)に何か引数を与えて描画するとします。
その時、従来の文字列を組みあわせた手法ではhtmlとして表わされた文字列を
全て変更することになってしまいます。
ですが、Reactの場合は変更された部分を計算し、
ブラウザで表示されている部分(DOMツリー)を直接いじることによって
描画を高速にできるとされています。

# JSXとコンポーネントとprops(プロパティ)

公式ページから例を引用します。

```
var HelloMessage = React.createClass({
  render: function() {
    return <div>Hello {this.props.name}</div>;
  }
});

ReactDOM.render(<HelloMessage name="John" />, mountNode);
```

このコードではJSXというhtml(xml)に良く似た文法がJavascriptの中で使われています。
JSXはreactを楽に使うための文法です。これはコンパイラを通すことで
(おおよそ)次のように変換されます。

```
var HelloMessage = React.createClass({
  displayName: "HelloMessage",

  render: function render() {
    return React.createElement(
      "div",
      null,
      "Hello ",
      this.props.name
    );
  }
});

ReactDOM.render(React.createElement(HelloMessage, { name: "John" }), mountNode);
```

古いJavascript(ES5)にはC++などで言うクラスは無いので、
reactはcreateClassという関数を用意しています。
こうして定義されたクラス(など)で、パラメータを与えられて
renderメソッドで *1つの* 要素を返すものをコンポーネントと言います。

reactはこういったコンポーネントを組みあわせていくことで
画面を構成していくという点が重要です。
そしてそのコンポーネントは呼びだしている要素から渡された引数を使うことで
表示を変更することができるわけです。

この渡された引数のことを **props** と呼びます。
propsは親(呼びだす側)から子(呼びだされる側)への一方通行で、
親が持っているデータを変更する場合には
*親からデータを変更するための関数をpropsとして渡してもらう*
という方法をとります。

# state(状態)

コンポーネント自身が値を保持しておきたい場合があります。
こういう場合は *state* を利用することが可能です。
参考に公式の例を引用します。
この例での`componentDidMount`と`componentWillUnmount`は
描画される直前と消される直前に呼ばれる関数です。

```
var Timer = React.createClass({
  getInitialState: function() {
    return {secondsElapsed: 0};
  },
  tick: function() {
    this.setState({secondsElapsed: this.state.secondsElapsed + 1});
  },
  componentDidMount: function() {
    this.interval = setInterval(this.tick, 1000);
  },
  componentWillUnmount: function() {
    clearInterval(this.interval);
  },
  render: function() {
    return (
      <div>Seconds Elapsed: {this.state.secondsElapsed}</div>
    );
  }
});

ReactDOM.render(<Timer />, mountNode);
```

# ES6のclassを使ったコンポーネントの書き方

さて、計算機のコードでこういう書き方をしてもいいのですが、
(なんとなくわかりやすいので)実際にはES6のクラスを使っています。

最初のこのコンポーネントを例にします。

```
var HelloMessage = React.createClass({
  render: function() {
    return <div>Hello {this.props.name}</div>;
  }
});
```

これはES6のクラスを使うと次のように書けます。

```
class HelloMessage extends React.Component {
  render() {
    return <div>Hello {this.props.name}</div>;
  }
}
```

ただし、この書き方をする時には注意が必要です。
[MdNのbindの項](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
を引用します。

```
var x = 9;
var module = {
  x: 81,
  getX: function() { return this.x; }
};

module.getX(); // 81

var getX = module.getX;
getX(); // 9, この場合 "this" はグローバルオブジェクトを参照するため

// 'this' を module に結びつけた新しい関数を生成
var boundGetX = getX.bind(module);
boundGetX(); // 81
```

これと同じ問題が先のクラスで書かれたコンポーネントにも存在します。
stateの例に挙げられていたカウンターをクラスで書きなおしたものが
react公式にあるのでそれを引用します。

```
export class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {count: props.initialCount};
    this.tick = this.tick.bind(this);
  }
  tick() {
    this.setState({count: this.state.count + 1});
  }
  render() {
    return (
      <div onClick={this.tick}>
        Clicks: {this.state.count}
      </div>
    );
  }
}
Counter.defaultProps = { initialCount: 0 };
```

注目したいのはconstructor(そのまんまコンストラクタ関数のことです)にある
`this.tick.bind(this)`です。
thisは実行されている場所で変化するので、
こうやって関数をオブジェクトのthisにbindしないと悲しみを生みます。

なお、コンストラクタでbindしなくとも、次のような書き方をすることも可能です。
reactのページから引用します。

```
<div onClick={this.tick.bind(this)}>  // ES5
<div onClick={() => this.tick()}>  // ES6
<div onClick={::this.tick}>  // ES7
```

ES6のアロー関数はその部分でのthisを保持します( https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/arrow_functions )
。

最後のES7の例は
[若干面倒な問題がある]( https://stackoverflow.com/questions/31220078/javascript-double-colon-es7-proposal/31221199 )
んですが、
表記が簡単になるので活用しています。

# 備考: さらに実行速度をあげるコンポーネントの書き方

stateが必要ないコンポーネントならば、次の書き方でコンポーネントを定義できます(公式の例)。

```
function HelloMessage(props) {
  return <div>Hello {props.name}</div>;
}
```

アロー関数を使って次のようにも書けます。

```
const HelloMessage = (props) => <div>Hello {props.name}</div>;
```

つまり、単純にpropsを受けとって要素を返す関数(関数型のオブジェクト)です。
このように書かれたコンポーネントは将来的に早くなる可能性があるそうです。
ただし、最初にも書きましたが、
stateを持つ可能性のあるコンポーネントなどはこの書き方ができません。

# リストとkey

リストの要素(`<li></li>`)などで
複数の要素を配列(ないし類似オブジェクト)として扱い、
その要素をコンポーネント内で展開できると便利なことがあります。
そのようにする場合、公式例によれば書き方は以下の通りです。

```
var ListItemWrapper = React.createClass({
  render: function() {
    return <li>{this.props.data.text}</li>;
  }
});
var MyComponent = React.createClass({
  render: function() {
    return (
      <ul>
        {this.props.results.map(function(result) {
           return <ListItemWrapper key={result.id} data={result}/>;
        })}
      </ul>
    );
  }
});
```

`results.map()`は`results`が配列の時に新しい配列を作って返すための関数です。
[MdNのArray.mapの説明](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Array/map)を参考にしてください。

重要なのは`key`です。この場合reactは`key`を見て要素を判別します。
`key`は親コンポーネントが子コンポーネントの各々を判別するためにあるので、
子コンポーネント側で設定するのではなく、親コンポーネント側で設定しなければなりません
(上の例では`ListItemWrapper`の中で指定すると失敗する)。

このパターンは武器や召喚の表示などで使われています。
武器の表示部分で関連するところを(一部変更して)抜きだしてみます。

```
// 武器の並び全体を表わすクラス
class WeaponTableBody extends Component {
  render() {
    // weaponは配列なのでmapを使って要素を生成する
    return (
      <tbody>
        {this.props.weapon.map((val, index) => { return <WeaponRow key={"wr_"+String(index)} index={index} />; })}
      </tbody>
    );
  }
}
// 表示に使うための変数群
const WEAPON_KIND = [
  ["sword", "剣"],
  ["dagger", "短剣"],
  ["spear", "槍"],
  ["axe", "斧"],
  ["stuff", "杖"],
  ["gun", "銃"],
  ["knuckle", "格闘"],
  ["bow", "弓"],
  ["instrument", "楽器"],
  ["blade", "刀"]
];
const SKILL_TYPE = [
  ["none", "無し"],
  ["kj1", "攻刃(小)"],
  ... // 略
];
const SKILL_LV = [
  ["0", "無し"],
  ["1", "1"],
  ... // 略
];
// 武器の1行を表わすコンポーネント
// フォームはControlled Componentsにしているので割と面倒くさい
class WeaponRow extends Component {
  // 武器のoptionを生成するための関数
  create_optfunc(key) {
    return (
      <option value={key[0]} key={key[0]}>{key[1]}</option>
    );
  }

  /* 注意: クラス変数はES7の要素 */
  // 武器種別
  e_kind = WEAPON_KIND.map(this.create_optfunc);
  // スキル種別
  e_skill_type = SKILL_TYPE.map(this.create_optfunc);
  // スキルレベル
  e_skill_lv = SKILL_LV.map(this.create_optfunc);

  /* このあたりに値が変更された時に呼ばれる関数 */

  // 実際にレンダリングされる要素を返す関数
  // 名前は固定
  render() {
    // 必要な要素をpropsから変数に取りだす
    const { isDragging, index } = this.props;
    const { selected, name, atk, skill_level, skill_type, cosmos, type } = this.props;
    // つかむところに適用されるスタイルを作る
    let style_hundle = { cursor: 'move' };
    style_hundle.color = isOver ? "red" : "blue";
    style_hundle.color = isDragging ? "green" : style_hundle.color;
    // レンダリングされる要素を返す
    // その際、どれがドラッグ&ドロップの対象になるかを指定している
    return (
      <tr>
        <td style={ style_hundle }>■</td>
        <td>
          <input type="checkbox" className="weapon_select" checked={selected} onChange={::this.change_select} />
        </td>
        <td>
          <input type="text" className="weapon_name width150" value={name} onChange={::this.change_name} />
        </td>
        <td>
          <input type="text" className="weapon_atk width50" value={atk} onChange={::this.change_atk} />
        </td>
        <td>
          <input type="checkbox" className="cosmos" checked={cosmos} onChange={::this.change_cosmos} />
        </td>
        <td>
          <select className="weapon_kind" value={type} onChange={::this.change_kind} >
            {this.e_kind}
          </select>
        </td>
        <td>
          <select className="weapon_skill_type1" value={skill_type[0]} onChange={::this.change_skill_type1} >
            {this.e_skill_type}
          </select>
        </td>
        <td>
          <select className="weapon_skill_type2" value={skill_type[1]} onChange={::this.change_skill_type2} >
            {this.e_skill_type}
          </select>
        </td>
        <td>
          <select className="weapon_skill_lv" value={skill_level} onChange={::this.change_skill_lv} >
            {this.e_skill_lv}
          </select>
        </td>
        <td>
          <input type="button" id="ins" value="+" onClick={::this.push_insert} />
          <input type="button" id="del" value="-" onClick={::this.push_delete} />
        </td>
      </tr>
    );
  }
}
```

render関数の最初にあるpropsのローカル変数への展開に関しては
[MdNに説明があります](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)。

ソース中でも書いてありますが、クラス変数はES7からの要素なので注意してください。
ES6ではクラスを定義したあとにクラスのプロパティとして追加する
(この例では`WeaponRow.e_kind = 中身`としなければならない)必要があります。

# JSX雑多なこと

JSXはhtmlではなくJavascriptのショートカット(糖衣構文)なので、
Javascriptの予約語を使えません。
そのため、見た目を指定する`class`は`className`と書く必要があります。

他にもクリックされた時などのイベントを指定する部分は
camelCase(先頭小文字単語の区切りで大文字)を使う必要があったり、
htmlの文字実体参照(`&amp;`とか)は使えなかったりなどの制限があります。

# フォームについて

reactのフォームについては面倒な部分が多いので[別の文書](/react_form)で説明します。

# まとめ

reactを使う点で重要なのは以下です。

- reactはコンポーネントの組みあわせで画面を描画する
- データはpropsを通した親から子への一方通行
- 子コンポーネントは親コンポーネントに要素を1つしか返せない(`<li></li><li></li>`は不可)

これだけ覚えておくと全体の理解が早くなると思います。
