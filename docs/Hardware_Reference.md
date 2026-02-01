# Hack Computer Hardware Reference

このドキュメントは、Nand2Tetrisプロジェクトにおいて実装されたハードウェア素子の仕様書です。
すべてのチップは、Nandゲートおよびそれ以前に定義されたチップのみを使用して構築されています。

## 1. Elementary Logic Gates (基本論理ゲート)

### NAND (Primitive)

* **Function:** 機能的完全性をもつ二つの二項論理演算子（NANDとNOR）のうち、電子回路で実現しやすい方。否定論理積。

* **Interface:** `IN a, b; OUT out;`

* **Logic:** `if (a==1 and b==1) then out=0 else out=1`

### Not (Inverter)

* **Function:** 入力ビットを反転させる。

* **Interface:** `IN in; OUT out;`

* **Implementation:** `Nand(in, in)`

* **Truth Table:**
  | in | out |
  |:--:|:---:|
  | 0  |  1  |
  | 1  |  0  |

### And

* **Function:** 両方の入力が1の時のみ1を出力する（論理積）。

* **Interface:** `IN a, b; OUT out;`

* **Implementation:** `Not(Nand(a, b))`

* **Truth Table:**
  | a | b | out |
  |:--:|:--:|:--:|
  | 0 | 0 | 0 |
  | 0 | 1 | 0 |
  | 1 | 0 | 0 |
  | 1 | 1 | 1 |

### Or

* **Function:** 少なくともどちらかの入力が1なら1を出力する（論理和）。

* **Interface:** `IN a, b; OUT out;`

* **Implementation:** `Nand(Not(a), Not(b))` (De Morgan's laws)

* **Truth Table:**
  | a | b | out |
  |:--:|:--:|:--:|
  | 0 | 0 | 0 |
  | 0 | 1 | 1 |
  | 1 | 0 | 1 |
  | 1 | 1 | 1 |

### Xor (Exclusive Or)

* **Function:** 入力が異なる場合のみ1を出力する（排他的論理和）。

* **Interface:** `IN a, b; OUT out;`

* **Implementation Note:** `Or(a,b) And Nand(a,b)`

  * 標準的な実装（Nand数9）よりも効率的な構成（Nand数6）を採用。

* **Truth Table:**
  | a | b | out |
  |:--:|:--:|:--:|
  | 0 | 0 | 0 |
  | 0 | 1 | 1 |
  | 1 | 0 | 1 |
  | 1 | 1 | 0 |

## 2. Selectors & Distributors (制御・分岐)

### Mux (Multiplexer)

* **Function:** 選択信号 `sel` に応じて、入力 `a` か `b` のどちらかを通す（if-else）。

* **Interface:** `IN a, b, sel; OUT out;`

* **Logic:**

  * If `sel==0` then `out=a`

  * If `sel==1` then `out=b`

### DMux (Demultiplexer)

* **Function:** 選択信号 `sel` に応じて、入力 `in` を `a` か `b` のどちらかに流す。

* **Interface:** `IN in, sel; OUT a, b;`

* **Logic:**

  * If `sel==0` then `a=in, b=0`

  * If `sel==1` then `a=0, b=in`

### Multi-Bit / Multi-Way Variants

これらは基本ゲートを並列配置、または木構造（Tree）状に配置して実装される。

* **Not16, And16, Or16:** 16ビットバスに対するビットごとの演算。物理的にゲートを16個並列に配置して実装。

* **Mux16:** 16ビットバス同士の選択。`sel` は1ビット。

* **Or8Way:** 8ビット入力の論理和。

  * **Function:** 入力ビットのうち、1つでも1なら1を出力。

  * **Implementation:** トーナメント方式（木構造）。

    1. 8入力を2つずつOrして4本にする。

    2. 4本を2つずつOrして2本にする。

    3. 最後の2本をOrして出力。

* **Mux4Way16 / Mux8Way16:** 多入力の選択器。

  * **Function:** 4つ（または8つ）の16ビット入力から、選択信号 `sel` に応じて1つを選んで出力する。

  * **Key Concept:**

    * **Mux4Way16:** `sel[2]`を使用。下位ビット(`sel[0]`)で予選（a vs b, c vs d）を行い、上位ビット(`sel[1]`)で決勝を行う。

    * **Mux8Way16:** `sel[3]`を使用。`Mux4Way16` を2つ使い、最後に `Mux16` で統合する。バスのスライス（`sel[0..1]`）を使用。

* **DMux4Way / DMux8Way:** 多出力への分配器。

  * **Function:** 1つの入力を、選択信号 `sel` に応じて4つ（または8つ）の出力先へ分配する。

  * **Key Concept:**

    * 上位ビットで「上流か下流か（例: a,b系 か c,d系か）」を大きく分岐させ、下位ビットでさらに細かく分岐させる階層構造を持つ。

## 3. Arithmetic Chips (算術演算)

### HalfAdder (半加算器)

* **Function:** 2つの1ビット入力を加算する。

* **Interface:** `IN a, b; OUT sum, carry;`

* **Implementation:**

  * `sum = Xor(a, b)`

  * `carry = And(a, b)`

### FullAdder (全加算器)

* **Function:** 3つの1ビット入力（繰り上がり含む）を加算する。

* **Interface:** `IN a, b, c; OUT sum, carry;`

* **Implementation:** 2つの `HalfAdder` と1つの `Or` を組み合わせて構成。

### Add16 (16-bit Adder)

* **Function:** 2つの16ビット数値を加算する。

* **Interface:** `IN a[16], b[16]; OUT out[16];`

* **Implementation:** Ripple Carry Adder（波及桁上げ加算器）。

  * 0ビット目は `HalfAdder`、1\~15ビット目は `FullAdder` を数珠繋ぎにする。

  * 最上位ビットのオーバーフローは無視する仕様。

*Created by: hira-ping*