---
title: "C#の#if falseで囲んだ行の色を変更する方法"
emoji: "🔥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["vscode", "csharp"]
published: false
---

C#は、プリプロセッサディレクティブ ( `#if false` など) で条件付きコンパイルを実現できる。

https://docs.microsoft.com/ja-jp/dotnet/csharp/language-reference/preprocessor-directives#conditional-compilation

VSCodeでC#を書くと、プリプロセッサディレクティブを用いてコンパイルが通らないようにした行も、コメント行のように元々のテキスト色で表示する。このため、実際にはコンパイルされず処理が通らない行であっても、そうでないかのように勘違いされてしまう。

![](/images/2022/04/08/01.png)

コンパイルが通らない行は、「コメントアウトした行」のように、文字色が薄い灰色になっていることが好ましい。今回はVSCodeの設定を書き換えることで、この問題を解決した。

![](/images/2022/04/08/02.png)

# 必要な設定

`setting.json`に以下を記述するだけでOK。

```json
"editor.semanticTokenColorCustomizations": {
    "rules": {
        "excludedCode": "#88846f"
    }
},
```

# 補足: Inspect Editor Tokens and Scopes機能について

VSCodeには、エディタ内の文字にカーソルを当てると、色定義キーや、今使っているカラーコードなどの情報を取得できる「開発者: エディター トークンとスコープの検査 / Developer: Inspect Editor Tokens and Scopes」という機能がある。

今回文字色を変えた「`#if false`」で囲まれた部分は、「excludedCode」というキーが割り当てられていた。これをもとに `setting.json` を書き換えている。

![](/images/2022/04/08/03.png)

もっと細かく色を設定できるようだが、詳細はここでは省く。気になる方は公式サイトを確認のこと。

https://code.visualstudio.com/api/language-extensions/semantic-highlight-guide
