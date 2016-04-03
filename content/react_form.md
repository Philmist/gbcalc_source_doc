+++
date = "2016-04-03T06:13:11+09:00"
draft = false
title = "reactのフォームについて"
categories = ["技術解説"]

+++

この文書ではreactのフォームについて説明します。

# reactのフォーム(入力欄)

reactのフォームは他のコンポーネントと違って、
*ユーザーの入力で表示内容を変化させなければなりません* 。
さもなければ入力しても表示が変化しないコンポーネントができます。

reactのフォームには2種類あります。
*controlled components* と *uncontrolled components* です。
controlledの方は(props.)valueを要素に持ち表示/入力内容を制御できるコンポーネントで、
uncontrolledは(props.)valueを持たず全てがユーザーまかせのコンポーネントです。

今回はcontrolled componentsしか使っていないのでそちらについて解説します。

# controlled componentsの使い方

まず、ざっと流れを説明します。

1. フォームの`onChange`に関数を指定する
(`<input type="text" onChange={なんちゃら} />`のように)
1. onChangeで指定された関数は引数に *イベント* を受けとる
1. 引数で受けとったイベント(`e`としておきます)から入力された値を読みとる(`e.target.value`)
1. 入力された値を処理してフォームの`value`で指定された何か(stateとか)に値を設定する

公式の例を見てみましょう。
React.createClassを使っている例なので
`onChange`で指定された関数は`this`が暗黙的にbindされています。

```
getInitialState: function() {
  return {value: 'Hello!'};
},
handleChange: function(event) {
  this.setState({value: event.target.value});
},
render: function() {
  return (
    <input
      type="text"
      value={this.state.value}
      onChange={this.handleChange}
    />
  );
}
```

注意しなければならないのは
**`this.props.value`に値を指定しないと表示されている値が反映されない**
ということです。
これを忘れるといつまで経っても値が更新されません。

# 子コンポーネントから親コンポーネントに値を返すという考え方

このフォームのテクニックは
*子にコールバック関数を渡して親に値を渡す*
とも考えることができます。
つまりコールバック関数を通して、
親の持っている値を変更して子に渡されるpropsを変更するのです。

コールバック関数をpropsとして渡して親が値をもらうテクニックは
親から子へのデータの一方通行が基本のreactでは良く使われるので
覚えておきたいところです。

もっとも、今回のソースではあまりそのやり方を使っていないのですが…。

# 補足: uncontrolled components

ちなみにフォームの`value`に何かを割り当てない場合、
それはuncontrolled componentsになります。
この場合、値を読みとるのは`onChange`からになります。

uncontrolled componentsは表示値を制御しにくくなるため今回は使っていません。
