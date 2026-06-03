# 卒業研究：日本語歌詞の韻律構造自動生成

日本語歌詞を対象に、形態素解析・マルコフ連鎖・整数線形計画法を組み合わせて品詞構造と韻律構造を自動生成する卒業研究プロジェクトです。

---

## 概要

本研究では、日本語の歌詞・小説などのテキストから品詞を抽出し、マルコフ連鎖によって品詞の連鎖構造を生成、さらに整数線形計画法（ILP）を用いて韻を踏む単語配置を最適化することで、歌詞の構造を自動生成します。

### 処理フロー

```
日本語テキスト
      ↓
① 品詞抽出（MeCab による形態素解析）
      ↓
② 品詞構造生成（マルコフ連鎖）
      ↓
③ 韻律構造自動生成（ILP による最適化）
      ↓
韻を踏んだ歌詞グリッドの出力
```

---

## ノートブック構成

### 1. `Extracting_parts_of_speech.ipynb` — 品詞抽出

日本語テキスト（歌詞・小説など）から品詞ごとに単語を分類・抽出します。

- **手法**: MeCab による形態素解析
- **入力**: 日本語テキストファイル（`.txt`）
- **出力**: 品詞別単語リスト（名詞・動詞・形容詞・助詞・助動詞・副詞・接続詞・感動詞・記号）
- **実行例**: 名詞 98語、動詞 44語、形容詞 5語、助詞 22語、助動詞 8語 を抽出

### 2. `Part_of_speech_structure_generation.ipynb` — 品詞構造生成

品詞の遷移確率に基づき、マルコフ連鎖を用いて歌詞の品詞構造（4×4グリッド）を生成します。

- **手法**: マルコフ連鎖 + ルーレット選択
- **入力**: 開始確率・遷移確率行列・終了確率（事前の言語分析に基づく）
- **出力**: 4×4 の品詞グリッド（各セルに名詞・動詞・助詞などが配置される）

| 位置 | Beat 1 | Beat 2 | Beat 3 | Beat 4 |
|------|--------|--------|--------|--------|
| 行 1 | 名詞   | 助詞   | 動詞   | 助動詞 |
| 行 2 | ...    | ...    | ...    | ...    |

### 3. `Automatic_generation_of_prosodic_structure.ipynb` — 韻律構造自動生成

品詞構造グリッドに、韻を踏むように単語を最適配置します。

- **手法**: 整数線形計画法（ILP）による韻スコアの最大化
- **韻の判定**: テキスト → 漢字読み（MeCab）→ カタカナ → ローマ字 → 母音列で類似度を計算
- **スコアリング**: 語頭母音一致（+2点）、語尾母音一致（+10〜15点+ボーナス）、母音密度（×5）
- **韻のパターン**: AABB（隣接韻）、ABAB（交差韻）、ABBA（包囲韻）から選択可能
- **入力**: 単語リスト（`words.txt`）
- **出力**: 8×4 または 4×4 の韻グリッド（韻グループを色分けで可視化）

---

## 使用技術・ライブラリ

| ライブラリ | 用途 |
|-----------|------|
| MeCab / ipadic | 日本語形態素解析・漢字読み取得 |
| Pykakasi | カタカナ・漢字 → ローマ字変換 |
| PuLP (CBC solver) | 整数線形計画法による最適化 |
| NetworkX / Matplotlib | 韻関係のグラフ可視化 |
| Pandas | データ整形・テーブル表示 |
| NumPy / SciPy | 数値計算 |

---

## セットアップ

### 前提条件

- Python 3.x
- MeCab（システムへのインストールが必要）
- Google Colab または Jupyter Notebook 環境

### インストール

```bash
pip install mecab-python3 ipadic pykakasi pulp networkx matplotlib pandas numpy scipy
```

MeCab のインストール（Ubuntu/Debian の場合）:

```bash
sudo apt-get install mecab libmecab-dev mecab-ipadic-utf8
```

### 実行手順

1. 分析対象の日本語テキストファイルを用意する
2. `Extracting_parts_of_speech.ipynb` を実行して品詞別単語リストを生成
3. `Part_of_speech_structure_generation.ipynb` を実行して品詞グリッドを生成
4. `Automatic_generation_of_prosodic_structure.ipynb` に単語リスト（`words.txt`）を入力して韻律グリッドを生成

---

## ファイル構成

```
Graduation_research/
├── Extracting_parts_of_speech.ipynb       # 品詞抽出
├── Part_of_speech_structure_generation.ipynb  # 品詞構造生成
└── Automatic_generation_of_prosodic_structure.ipynb  # 韻律構造自動生成
```
