+++
date = "2016-04-03T21:33:34+09:00"
draft = true
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
アプリケーション全体でどこにstateを持つかは決まっていませんでした。

そこで出てくるのがstateを保持するためのライブラリの1つであるreduxです。
reduxは(react-reduxという派生ライブラリと組みあわせることで)
容易にアプリケーション全体でstateを保持し、変化させ、取りだすことができるわけです。

# stateをどうやって扱うのか

reduxはstateを保持する部分をアプリケーション全体で1つ持っています。
これをstoreと言います。

storeで保持されているstateは基本的に読みとり専用です。
stateを変化させるにはactionと呼ばれるオブジェクト(`{}`)を
dispatchする(dispatchと呼ばれる関数に渡す)ことで変化を依頼します。

actionはreducerと呼ばれる関数にstoreに保持されている現在のstateとともに渡されます。
reducerはactionを見てstateを変化させる必要があるなら次のstateを新しく生成して返します。
そしてstateはまたstoreに保持されます。

このようにしてreduxはstateを管理しています。
[公式の例](http://redux.js.org/index.html)がわかりやすいかと思います。

# react-reduxの紹介
