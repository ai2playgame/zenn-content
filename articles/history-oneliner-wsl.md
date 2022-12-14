---
title: "直前に実行したコマンドをクリップボードにコピーするワンライナー(WSL対応版)"
emoji: "🕌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [linux]
published: false
---

シェル・ワンライナー160本ノック本を進めているとき、作業メモを取ろうと、直前に実行したコマンドをクリップボードにコピーしたくなった。

マウスを使えば簡単だが、せっかくシェル芸の本をやってるので、コマンドで実現したい。

## 環境

Windows10のWSLとして動くUbuntu 22.04で動作確認。

```bash
$ lsb_release -a
Distributor ID:	Ubuntu
Description:	Ubuntu 22.04.1 LTS
Release:	22.04
Codename:	jammy
```

## 直前に実行したコマンドをクリップボードにコピーするワンライナー

```bash
$ history|tail -n 2|head -n 1|cut -d' ' -f 4-|tr -d '\n'|clip.exe
```

## ちょっと解説

`history`コマンドで、実行したコマンドの履歴が取得できる。その結果を`tail`と`head`に流して、直前のコマンド履歴行を抜き出す（クリップボードにコピーするために実行したコマンドがhistory最下行にあるので、`tail -n 1`では取れない）。

`history`の出力行をスペース区切りにすると、4列目以降が実際に実行したコマンドを指すので、それを`cut`コマンドで切り出す。
末尾の改行文字が邪魔なので、`tr`で改行文字を除去する。

こうして、直前に実行したコマンドを文字列として出力できたので、Windowsの`clip.exe`にパイプで渡してコピーする。
