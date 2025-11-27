# Antigravity Agent Rules

このリポジトリは、Antigravity Agent (Gemini) の行動指針となる **`.agent/rules`** を管理するためのリポジトリです。

## .agent/rules とは？

`.agent/rules` は、AI エージェントに対する「行動ルール」や「特定の状況での振る舞い」を定義した Markdown ファイル群を格納するディレクトリです。
ここにルールを記述することで、エージェントを自分（ユーザー）のプロジェクトや開発スタイルに最適化されたパートナーへと育てることができます。

### 主な役割

1.  **行動の定義**: エージェントに守らせたいコーディング規約、振る舞い、思考プロセスなどを記述します。
2.  **発動条件（トリガー）**: 「特定のコマンドが入力された時」「特定のファイルが開かれた時」など、ルールが適用される条件を設定できます。
3.  **知識の参照**: 外部ファイルを参照させることで、複雑なルールを分割管理したり、ナレッジベースとして活用したりできます。

    #### 例：`test01.md`（トリガー）から `test02.md`（コンテンツ）を参照する

    **.agent/rules/test/test01.md**

    ```markdown
    ---
    trigger: model_decision
    description: ユーザーが `use test01` + 質問をしてきたときに発動。
    ---

    .agent/rules/test/test02.md をリファレンスして答えを出すこと。
    ```

    **.agent/rules/test/test02.md**

    ```markdown
    ---
    trigger: model_decision
    description: .agent/rules/test/test01.mdが使用されたときに、一緒に発動。
    ---

    .agent/rules/test/test01.md の Question の答え。

    A：test01 の答えは test02 です。
    ```
