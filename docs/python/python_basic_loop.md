# ループ（繰り返し）

ループは、「同じ処理を何度も繰り返す」仕組みです。繰り返しの処理が必要な場面で役立ちます。

!!! info "COBOLプログラマーの皆様へ"
    COBOLのPERFORM文に相当するのがPythonのループです。ただし、書き方や考え方にいくつか違いがあります。このページでは、COBOLとの比較を交えながら説明します。

---

## for文の基本

Pythonの`for`文を使った基本的なループの例を見てみましょう。

```python
for i in range(5):
    print(i)
```

このコードは以下のように表示されます。

```
0
1
2
3
4
```

* `range(5)`は0から4までの数値を順番に作成します。
* 作成された数値は変数`i`に順番に代入されます。

### COBOLとの比較

```cobol
* COBOLでの同等の処理
PERFORM VARYING WS-I FROM 0 BY 1 UNTIL WS-I >= 5
    DISPLAY WS-I
END-PERFORM.
```

| 特徴 | COBOL | Python |
|------|-------|--------|
| 繰り返し方法 | PERFORM文 | for文、while文 |
| カウンタ変数 | 事前に宣言が必要 | forループで自動生成可能 |
| 範囲指定 | FROM...BY...UNTIL | range()関数 |
| 終了条件 | UNTIL条件を明示 | range()で自動的に決定 |

---

## リストを使ったループ

リストを使ったループの例です。

```python
fruits = ["りんご", "バナナ", "みかん"]
for fruit in fruits:
    print(fruit)
```

!!! note "COBOLとの違い"
    COBOLでは配列（TABLE）の要素を処理する場合、インデックスを使って参照しますが、Pythonでは直接要素を取り出せます。
    
    ```cobol
    * COBOLの例
    01 FRUIT-TABLE.
       05 FRUITS OCCURS 3 TIMES PIC X(10).
    01 WS-INDEX PIC 9.
    
    PERFORM VARYING WS-INDEX FROM 1 BY 1 UNTIL WS-INDEX > 3
        DISPLAY FRUITS(WS-INDEX)
    END-PERFORM.
    ```

このコードは以下を表示します。

```
りんご
バナナ
みかん
```

リストの要素が順番に変数fruitに代入され、その要素に対して処理が行われます。

---

## while文の基本

条件がTrueである間、処理を繰り返す`while`文の例です。

```python
count = 0
while count < 3:
    print(count)
    count += 1
```

このコードは以下を表示します。

```
0
1
2
```

条件がFalseになるまでループが続きます。

---

## 実践課題

### 課題1：偶数と奇数を表示する

1〜20の数字を順番に表示し、偶数の場合にはその数字の後に「偶数です」と表示するプログラムを作りましょう。

#### 手順

1. 新しいファイルを作成し、`loop_even.py`という名前で保存します。
2. 以下のコードをファイルに記述します。

```python
for number in range(1, 21):
    if number % 2 == 0:
        print(f"{number} は偶数です")
    else:
        print(number)
```

3. ターミナルまたはコマンドプロンプトを開き、以下のコマンドでプログラムを実行します。

```bash
python loop_even.py
```

### 課題2：リストの要素を繰り返し処理

好きな食べ物を5つリストに入れて、その食べ物を順番に表示するプログラムを作りましょう。

#### 手順

1. 新しいファイルを作成し、`favorite_foods.py`という名前で保存します。
2. 以下のコードをファイルに記述します（食べ物は自由に変更してください）。

```python
foods = ["ピザ", "寿司", "ラーメン", "カレー", "パン"]
for food in foods:
    print(f"好きな食べ物は {food} です")
```

3. ターミナルまたはコマンドプロンプトを開き、以下のコマンドでプログラムを実行します。

```bash
python favorite_foods.py
```

---

## よくある間違い・Q&A

### 無限ループに注意

* `while`文で条件がずっとTrueだと、プログラムが止まらなくなります。

```python
# 無限ループの例（注意！）
while True:
    print("止まりません！")
```

### コロン（:）の付け忘れ

* `for`と`while`文の後ろには必ずコロンが必要です。

```python
# 誤った例
for i in range(5)
    print(i)

# 正しい例
for i in range(5):
    print(i)
```

### インデントの忘れ

ループ内の処理はインデント（半角スペース4つ）で区切ります。

```python
# 誤った例
for i in range(3):
print(i)

# 正しい例
for i in range(3):
    print(i)
```

---

> ループを使いこなすことで、「大量のデータ処理」や「繰り返しの自動化」が簡単にできます！

[◀ 戻る：条件分岐](python_basic_if.md)　｜　[次へ ▶ 関数](python_basic_func.md)
