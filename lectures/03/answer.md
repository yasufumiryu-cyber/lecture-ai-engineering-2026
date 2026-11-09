# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い：
第1回は嘘まみれ。エージェント回答はほぼほぼ定義と一致していた。

- `--trace` で見えたツール呼び出し（ツール名と引数）：
--- trace ---
[tool call]   make_quiz({'lecture': 2})
[tool return] make_quiz -> {"question": "「事前学習」の意味は？", "answer": "大量の汎用テキストで、次のトークンを予測するようにモデルを学習させる最初の段階。言語の知識や一般的な常識の多くはここで身につく。"}…

- 1つの質問での LLM 呼び出し回数（`requests`）：
２回


- 追加したツールと、docstring に書いた説明：

agent.tool_plain(tools.make_quiz)

def make_quiz(lecture: int) -> str:
    """指定した回の用語から 1つを選び、
    確認問題と答えを返す。

    「第2回の確認問題を出して」のように、
    復習用の問題を求められたときに使う。
    """
    terms = [term for term in load_glossary() if term["lecture"] == lecture]
    if not terms:
        return f"第{lecture}回の用語が用語集に見つかりませんでした。"

    term = random.choice(terms)
    return json.dumps(
        {
            "question": f"「{term['term']}」の意味は？",
            "answer": term["definition"],
        },
        ensure_ascii=False,
    )

## 宿題

- 取り組んだレベル：基礎 
- やったこと：docstringに「詳細な解説」もつけるよう実装した。
- 結果：ツールの呼ばれ方は変わらなかったが、返答で「詳しく確認したいときはいつでも声かけてね！」という表記が追加されたのと、tool_returnでexplanationというカテゴリが追加された。
- 気づき・疑問：一回きりのメッセージなので、ただチャットしているだけでは答え合わせもできないのが不便だと思った。
