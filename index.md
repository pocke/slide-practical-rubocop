# 実用的な RuboCop の話

[表参道.rb #19 静的解析と技術的負債と……](https://omotesandorb.connpass.com/event/48967/)

---

## 自己紹介

---

### だれこれ


<img style="width: 20%; border-radius: 50%" alt="ほにゃー" src="pocke.svg">

- Pocke ( Masataka Kuwabara )
- Engineer at Actcat Inc.
- RuboCop Contributor
- TW: [@p\_ck\_](https://twitter.com/p_ck_)  GH: [@pocke](https://github.com)

---

### 労働


<img src="sideci.png" alt="SideCI">

[sideci.com](https://sideci.com)

---

### RuboCop でやってること

- [Performance/RegexpMatch](https://github.com/bbatsov/rubocop/pull/3824)
- [Lint/SafeNavigationChain](https://github.com/bbatsov/rubocop/pull/3804)
- [Lint/MultipleCompare](https://github.com/bbatsov/rubocop/pull/3795)
- ...

Lint 関係の Cop メイン

---


<img src="rubocop-contributor.png" alt="Contribution Graph">

トータルで9番目にコミットが多い

[Contributors to bbatsov/rubocop](https://github.com/bbatsov/rubocop/graphs/contributors)

---

<img src="rubocop-2months.png" alt="Contribution Graph 2 months">

ここ2ヶ月だと一番コミットが多いのは私

[Contributors to bbatsov/rubocop](https://github.com/bbatsov/rubocop/graphs/contributors?from=2016-11-30&to=2017-01-30&type=c)


---


## Agenda

---

## Agenda

RuboCop 開発者から見た、<br>
実用的な RuboCop の使い方を広めたい




---

## Agenda

1. RuboCop とは
  1. 概略
  1. RuboCop はそのままでは実用的ではない？
1. 実用的な RuboCop Lint 編
1. 実用的な RuboCop Style 編


---


## 1. RuboCop とは

Q. RuboCop を知っている人？ <i class="twa twa-lg twa-raised-hand"></i>



---

### 1-1 RuboCop の概略



RuboCop は Rubyの静的解析器で、<br>
複数の役割を持っています。

- [Ruby Style Guide](https://github.com/bbatsov/ruby-style-guide) に沿ってスタイルチェックを行う
- バグやパフォーマンス上問題のあるコードを検出する
- 複雑すぎるコードを検出する


---


### 例1 Style Checker

```ruby
def foo
  bar+baz            # スペースが足りない
  array.map{|x|bar}  # スペースが足りない
    end              # end の alignment がおかしい
```

- コーディングスタイルの問題を検出する
- 多くの場合、`rubocop -a`で自動修正可能


---

### 例2 Lint / Performance


```ruby
def foo
  if 10 < x < 20       # Ruby ではこのようには書けない
    # ...
  end
  array.sort do |a, b|
    a.foo <=> b.foo    # array.sort_by(&:foo) の方が速い！
  end
end
```

バグやパフォーマンスの問題を検出する

---

### 例3 Metrics

```ruby
def foo                                           # めっちゃ複雑
  if val < 1
  else
    case cond
    when x                                        # めっちゃ長い行
      if (hoge == 1 && fuga == 2) || array.all? { |piyo| piyo <= 3 }  
        do_something1
      end
      do_something2
    when y
      do_something3
    else
      do_something4
    end
  end
end
```

複雑すぎるコードを検出する


---

### 1-1 まとめ

- RuboCop を使うと様々な観点からRubyコードの問題点を発見可能
- `-a`オプションを使用することで自動で問題を修正

RuboCop を使えば<br>
Ruby コードに平穏が訪れる……？？

---



### 1-2 RuboCop の問題点


平穏は訪れない

---

### ひたすら大量の警告

弊社のメインプロダクトで無設定の RuboCop を走らせると、8545個の警告が

```
$ rubocop
....

764 files inspected, 8545 offenses detected
```

<i class="twa twa-2x twa-innocent"></i><i class="twa twa-2x twa-innocent"></i><i class="twa twa-2x twa-innocent"></i>


---

### 何故警告が出るのか

```
2065 Metrics/LineLength
1539 Style/StringLiterals
 762 Style/FrozenStringLiteralComment
 704 Style/AsciiComments
 478 Style/Documentation
 206 Style/IndentHash
 168 Style/NumericLiterals
 157 Metrics/MethodLength
 154 Style/BracesAroundHashParameters
 151 Style/ExtraSpacing
 150 Metrics/AbcSize
 145 Style/ClassAndModuleChildren
 139 Metrics/BlockLength
 110 Style/SpaceInsideBlockBraces
 109 Style/HashSyntax
```

警告数が上位15件の Cop

---


```
2065 Metrics/LineLength               # 1行80文字制限
1539 Style/StringLiterals             # Single Quotation を強制
 762 Style/FrozenStringLiteralComment # マジックコメントを強制
 704 Style/AsciiComments              # コメントにASCII文字を強制
 478 Style/Documentation              # クラスにドキュメントコメントを強制
 206 Style/IndentHash                 # Hashリテラルのスタイルを強制
 168 Style/NumericLiterals            # 数値の3ケタで区切りを強制 例) 300_000
 157 Metrics/MethodLength             # 1メソッドに含まれる行数制限
 154 Style/BracesAroundHashParameters # メソッド呼び出しの際Hashの括弧を強制
 151 Style/ExtraSpacing               # 余分なスペースを検出
 150 Metrics/AbcSize                  # メソッドの複雑さを検出
 145 Style/ClassAndModuleChildren     # ネストしたクラスのスタイルを強制
 139 Metrics/BlockLength              # 1ブロックに含まれる行数制限
 110 Style/SpaceInsideBlockBraces     # ブロックの括弧の周りにスペースを強制
 109 Style/HashSyntax                 # Hash Rockeet を使うか強制
```

---

## 2. MeowCop / RuboCop を Lint として使う


---

## 3. Gry / RuboCop を Style Checker として使う

---

## まとめ
