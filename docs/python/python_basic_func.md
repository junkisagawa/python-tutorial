# 関数

関数は、繰り返し利用する処理に名前をつけ、必要な時に呼び出せる仕組みです。コードを整理し、再利用を簡単にします。

!!! info "COBOLプログラマーの皆様へ"
    Pythonの関数は、COBOLのSECTIONやPARAGRAPH、またはCALLで呼び出すサブプログラムに似ています。しかし、より柔軟で簡潔に書ける点が違います。

---

## 関数とは？

関数とは、「よく使う処理に名前をつけてまとめ、何度でも使えるようにする仕組み」です。

### 関数のメリット

* コードの再利用ができる
* プログラムが見やすく整理される
* バグの修正や機能追加が容易になる

---

## 関数の作り方（定義と呼び出し）

関数の作成と呼び出しの例を見てみましょう。

```python
def say_hello():
    print("こんにちは！")

say_hello()  # 関数を呼び出す
```

### COBOLとの比較

```cobol
* COBOLの例（PARAGRAPHを使った場合）
PROCEDURE DIVISION.
MAIN-PARAGRAPH.
    PERFORM SAY-HELLO.
    STOP RUN.

SAY-HELLO.
    DISPLAY "こんにちは！".
```

| 特徴 | COBOL | Python |
|------|-------|--------|
| 定義の方法 | PARAGRAPH名を記述 | defキーワードを使用 |
| 呼び出し方 | PERFORM文 | 関数名() |
| 引数の渡し方 | USING句（サブプログラム） | カッコ内に指定 |
| 戻り値 | RETURNING句（一部） | return文 |

このコードを実行すると、次のように表示されます。

```
こんにちは！
```

* `def` は関数の定義を示します。
* `say_hello` は関数の名前です。
* 関数の内容はインデント（半角スペース4つ）で記述します。

---

## 引数と戻り値

関数には「引数（関数に渡すデータ）」と「戻り値（関数が返すデータ）」を設定できます。

```python
def greet(name):
    return f"こんにちは、{name}さん"

message = greet("たろう")
print(message)
```

!!! tip "COBOLのサブプログラムとの違い"
    COBOLのサブプログラムではLINKAGE SECTIONを使ってパラメータを受け渡しますが、Pythonではよりシンプルに記述できます。
    
    ```cobol
    * COBOLサブプログラムの例
    IDENTIFICATION DIVISION.
    PROGRAM-ID. GREET-SUB.
    DATA DIVISION.
    LINKAGE SECTION.
    01 LS-NAME PIC X(20).
    01 LS-MESSAGE PIC X(50).
    
    PROCEDURE DIVISION USING LS-NAME RETURNING LS-MESSAGE.
        STRING "こんにちは、" LS-NAME "さん" 
            DELIMITED BY SIZE INTO LS-MESSAGE.
    ```

このコードを実行すると、次のように表示されます。

```
こんにちは、たろうさん
```

* `name` が引数（関数に渡されるデータ）です。
* `return` 文で指定された内容が戻り値として返ります。

---

## 実践課題

### 課題：引数を使った関数

引数で受け取った2つの数値の合計を返す関数を作成しましょう。

#### 手順

1. 新しいファイルを作成し、`sum_numbers.py`という名前で保存します。
2. 以下のコードをファイルに記述します。

```python
def sum_two_numbers(a, b):
    return a + b

result = sum_two_numbers(10, 20)
print(f"合計は {result} です")
```

3. ターミナルまたはコマンドプロンプトを開き、以下のコマンドでプログラムを実行します。

```bash
python sum_numbers.py
```

出力結果：

```
合計は 30 です
```

---

## よくある間違い・Q&A

### コロン（:）の付け忘れ

* 関数の定義後には必ずコロン（:）を付けましょう。

```python
# 誤った例
def say_hello()
    print("こんにちは！")

# 正しい例
def say_hello():
    print("こんにちは！")
```

### 呼び出し時のカッコ忘れ

* 関数を呼び出す時には必ずカッコが必要です。

```python
# 誤った例
say_hello

# 正しい例
say_hello()
```

### 戻り値がないときの挙動

* 関数内で`return`を指定しない場合、戻り値は`None`になります。

```python
def greet():
    print("こんにちは！")

result = greet()
print(result)  # 出力：None
```

---

> 関数を使うことで、プログラムがスッキリまとまり、再利用がしやすくなります！

[◀ 戻る：ループ](python_basic_loop.md)　｜　[次へ ▶ コレクション](python_basic_collection.md)
