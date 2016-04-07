+++
date = "2016-04-03T21:33:34+09:00"
draft = false
title = "reduxの紹介"
categories = ["技術解説"]

+++

この文書では計算機のデータを一括管理しているreduxというライブラリと
それと一緒に使っているredux-thunkというライブラリについて
解説します。

# reduxの短い紹介

reduxはstate(状態)を管理するライブラリです。

今回の計算機で使われているreactではstateをどこに保持するかは
コードを書く側にまかされています。
各コンポーネントは自身にstateを持つことも出来ますが、
アプリケーション全体でどこにデータを持つかは決まっていませんでした。

そこで出てくるのがstateを保持するためのライブラリの1つであるreduxです。
reduxは(react-reduxという派生ライブラリと組みあわせることで)
容易にアプリケーション全体でstateを保持し、変化させ、取りだすことができるわけです。

# stateをどうやって扱うのか

reduxはstateを保持する部分をアプリケーション全体で持っています。
これをstoreと言います。

storeで保持されているstateは基本的に読みとり専用です。
stateを変化させるにはactionと呼ばれるオブジェクト(`{}`)を
dispatchする(dispatchと呼ばれる関数に渡す)ことで変化を依頼します。

actionはreducerと呼ばれる関数にstoreに保持されている現在のstateとともに渡されます。
reducerはactionを見てstateを変化させる必要があるなら次のstateを新しく生成して返します。
そしてstateはまたstoreに保持されます。

このようにしてreduxはstateを管理しています。
[公式の例](http://redux.js.org/index.html)がわかりやすいかと思います。

## reducer

reducerは現在のstateとactionを渡されて、次のstateを返す関数です。
具体例を計算機のソースから持ってきます。

```
import * as RC from "./const/reducer_type.js";

// 入力ロック状態を管理するreducer
export function inputlock_counter(state = 0, action) {
  if (action.type == RC.inputlock.INCREMENT) {
    return Number(state+1);
  } else if (action.type == RC.inputlock.DECREMENT) {
    let retval = Number(state-1);
    if (retval < 0) {
      console.warn("Input Lock Counter is MINUS (Set to 0).");
      retval = Number(0);
    }
    return retval;
  } else {
    return state;
  }
}
```

`RC.inputlock.INCREMENT`などは定数だと思ってください。

actionは`{ type: RC.inputlock.INCREMENT }`のようなオブジェクトです。
このようなオブジェクトをreducer達に渡された時、
`type`を見て自分が担当のreducerとわかった時にstateを
何らかの形で変化させて返します。

ただし注意が必要です。
stateを変化させた場合には **必ず元の変数とは違う変数で返さなければならない** のです。
これを忘れた場合、stateが変化してもstateを見ているコンポーネントには通知されません。


# react-reduxの紹介

react-reduxはreduxをreact上で簡単に使うためのライブラリです。
提供しているAPIは2つですが若干仕様が面倒です。

まず1つ目は`Provider`です。
これはreactのコンポーネントであり、
公式例によれば次のようにして画面を定義するコンポーネントを覆うことで使います。
`store`はreduxのstoreです。
```
ReactDOM.render(
  <Provider store={store}>
    <MyRootComponent />
  </Provider>,
  rootEl
)
```

2つ目は`connect`です。
これは
*stateを取りだしてコンポーネントのpropsとして注入するオブジェクトを返す関数*
と
*コンポーネントのpropsに注入する「中でdispatchしていることを期待される関数」のオブジェクト*
を引数に取ります。
2つ目はオブジェクトを返す関数を引数に取ってもかまいません。
なお、両方の引数ともオプションです。

ややこしいので公式の例を使って説明します。

## mapStateToProps

まず1つ目は`mapStateToProps`という名前で呼ばれる引数です。

これはソースを見るとわかると思いますが、
state全体を引数として渡されて、
その中から **必要なstateだけを取りだして** オブジェクトにして返すことを期待されています。
```
function mapStateToProps(state) {
  return { todos: state.todos }
}
```

先にも書いた通り注意が必要なのは、使うstateだけを取りだすことです。
そうせずに不必要なstateまで取りだしてpropsに注入すると、
stateが更新された時にpropsが更新されて、
その際にコンポーネントが再描画されるので効率が悪くなるのです。

最終的にオブジェクトとして返せば良いことになっているので、
中でstateの値を使って条件判断などをしてオブジェクトを生成してもかまいません。

## mapDispatchToProps

2つ目の引数は`mapDispatchToProps`と呼ばれます。
この引数はオブジェクトか関数を受けとります。

オブジェクトを受けとった場合、
そこで指定されている関数は
全てがaction creator(actionと呼ばれるオブジェクトを返す関数)と考えられて、
dispatchにくるんでコンポーネントのpropsに注入されます。
言いかえれば、propsに注入された関数を使うことで自動的に目的のactionがdispatchされます。

関数を受けとった場合は動作が若干違います。
引数として指定された関数は、1つ目に`dispatch`、2つ目に`ownProps`と呼ばれる引数を受けとります。
そして引数として指定された関数は *propsに注入する関数を含んだオブジェクト* を返すことが期待されています。
なお、`ownProps`はオプションの引数であり受けとらなくてもかまいません。

# redux-thunk

さて、actionはdispatchされるオブジェクトだと前に書きました。
今回使っているredux-thunkとよばれるライブラリ(正確にはミドルウェア)ではこれが拡張されます。
*actionに関数を取ることが可能になる* のです。

わけがわからないと思いますが続けます。
actionに渡された関数は引数としてdispatchを受けとります。
関数は中でdispatchを使いまわすことができます。
結果として、
[StackOverflowに投稿されている例](http://stackoverflow.com/questions/35411423/how-to-dispatch-a-redux-action-with-a-timeout/35415559#35415559)
のようなことができます。
```
// actions.js

function showNotification(id, text) {
  return { type: 'SHOW_NOTIFICATION', id, text }
}
function hideNotification(id) {
  return { type: 'HIDE_NOTIFICATION', id }
}

let nextNotificationId = 0
export function showNotificationWithTimeout(text) {
  return function (dispatch) {
    const id = nextNotificationId++
    dispatch(showNotification(id, text))

    setTimeout(() => {
      dispatch(hideNotification(id))
    }, 5000)
  }
}

// component.js

import { connect } from 'react-redux'

// ...

this.props.showNotificationWithTimeout('You just logged in.')

// ...

export default connect(
  mapStateToProps,
  { showNotificationWithTimeout }
)(MyComponent)
```

connectの2つ目の引数、`mapDispatchToProps`で指定されたオブジェクトの関数は
dispatchでくるまれることを思いだしてください。

# まとめ

- reduxはstateを管理するライブラリ
- react-reduxをstateに繋げることで、stateの変化に応じてコンポーネントの内容を変えられる
