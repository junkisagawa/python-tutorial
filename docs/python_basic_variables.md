# 変数と型

変数と型はPythonプログラミングの基礎です。このページでは「変数とは何か」「型とは何か」について詳しく解説します。

---

## 変数とは？

変数とは、「データ（値）に名前をつけて保存する箱」のようなものです。

具体的な例を見てみましょう。

```python
name = "たろう"
age = 25
```

このコードでは、

* `name` という変数に文字列「たろう」を保存しています。
* `age` という変数に整数の25を保存しています。

変数に値を保存することを「代入」と呼びます。

---

## 型（データの種類）とは？

Pythonにはデータを分類する「型」があります。主な型は以下の通りです。

| 型名（型）     | 説明                      | 例                 |
| --------- | ----------------------- | ----------------- |
| 文字列（str）  | 文字や文を表します               | "こんにちは", "Python" |
| 整数（int）   | 整数値を表します                | 10, -5            |
| 小数（float） | 小数点を含む数値を表します           | 3.14, -0.5        |
| 真偽値（bool） | 真（True）または偽（False）を表します | True, False       |

---

## 型の自動判別（動的型付け）

Pythonは変数への代入時に、自動的に型を判別します。

例：

```python
x = 10       # 整数型（int）として判別
y = "abc"    # 文字列型（str）として判別
z = 3.14     # 小数型（float）として判別
is_valid = True  # 真偽値型（bool）として判別
```

---

## 型の確認方法

変数の型を確認するには、`type()` 関数を使います。

例：

```python
x = 10
y = "abc"
z = 3.14

print(type(x))  # 出力：<class 'int'>
print(type(y))  # 出力：<class 'str'>
print(type(z))  # 出力：<class 'float'>
```

---

## よくある間違いとQ&A

* **変数名のルール**
  
  * 英数字とアンダースコア（\_）のみ使用可能です。
  * 日本語も使用できますが、読みやすさや国際性の観点から英語を推奨します。
  
* **数字から始まる変数名はNG**
  
  * 例：`1name` は使えません。代わりに `name1` のようにしましょう。
  
* **型が異なると演算が失敗する**
  
  * 整数と文字列を直接計算するとエラーになります。
  * 例：

```python
# 間違った例
x = "10"
y = 5
# print(x + y) はエラーになります。

# 正しい例（型変換）
print(int(x) + y)  # 出力：15
```

---

> 変数は「データに名前をつけて管理する」ための非常に重要な仕組みです。

[次へ ▶ 条件分岐](python_basic_if.md)
