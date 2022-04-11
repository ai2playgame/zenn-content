---
title: "VSCode C#の#if falseで囲んだ行の色を変更する方法"
emoji: "🔥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["vscode", "csharp"]
published: false
---

C#は、プリプロセッサディレクティブ ( `#if false` など) で条件付きコンパイルを実現できる。

https://docs.microsoft.com/ja-jp/dotnet/csharp/language-reference/preprocessor-directives#conditional-compilation

VSCodeでC#を書くと、プリプロセッサディレクティブを用いてコンパイルが通らないようにした行も他の行と同じテキスト色で表示してしまう。これでは、実際にはコンパイルされず処理が通らない行であっても、そうでないかのように勘違いされてしまう。

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

もっと細かく色を設定できるようだが、詳細はここでは省く。詳しく知りたい場合はVSCodeの公式ドキュメント等を参照のこと。

https://code.visualstudio.com/api/language-extensions/semantic-highlight-guide


# 参考資料

[VS Code tips — The "Developer: Inspect editor tokens and scopes" command - YouTube](https://www.youtube.com/watch?v=mC_htrJ1QPg)

[C# プリプロセッサ ディレクティブ | Microsoft Docs](https://docs.microsoft.com/ja-jp/dotnet/csharp/language-reference/preprocessor-directives#conditional-compilation)

[Syntax Highlight Guide | Visual Studio Code Extension API](https://code.visualstudio.com/api/language-extensions/syntax-highlight-guide)

[Semantic Highlight Guide | Visual Studio Code Extension API](https://code.visualstudio.com/api/language-extensions/semantic-highlight-guide)

[Change colour of inactive preprocessor blocks in C# · Issue #69513 · microsoft/vscode](https://github.com/Microsoft/vscode/issues/69513)
(VSCodeのC#プラグインの標準機能として対応する気はなさそう。これくらいは直してほしいなあ……)

[c# - VSCode C#の プリプロセッサディレクティブ #if 行の色につきまして - スタック・オーバーフロー](https://ja.stackoverflow.com/questions/52938/vscode-c%E3%81%AE-%E3%83%97%E3%83%AA%E3%83%97%E3%83%AD%E3%82%BB%E3%83%83%E3%82%B5%E3%83%87%E3%82%A3%E3%83%AC%E3%82%AF%E3%83%86%E3%82%A3%E3%83%96-if-%E8%A1%8C%E3%81%AE%E8%89%B2%E3%81%AB%E3%81%A4%E3%81%8D%E3%81%BE%E3%81%97%E3%81%A6)
