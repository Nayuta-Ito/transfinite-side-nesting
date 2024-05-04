# transfinite-side-nesting
無限回貫通問題は存在しない

## 前文
無限回貫通問題とは、任意回貫通する表記において
```
  (0,(ω,0)+(ω,0)) ...(1)
> (0,(ω,0)+(1,0)) ...(2)
> (0,(ω,0)+(0,(ω,0))) ...(3)
> (0,(ω,0)+(0,(2,0))) ...(4)
> (0,(ω,0)+(0,(0,(ω,0)))) ...(5)
> ...
```
という無限列が生じる問題です。

(2)の(1,0)は1回貫通、つまりΩに対応する項です。そのため、これを崩壊させると可算順序数が出てくるはずです。(4)の(0,(2,0))は内側の(2,0)が2段階横ネストを試み、あたかも非可算順序数であるかのように振る舞っています。これは、(2,0)がその外側の(0,_)を2回貫通のψ_0だと勝手に思い込んでいることが原因です。

このリポジトリでは、貫通数とψの添え字を別に扱うことで、無限回貫通問題の存在しない表記を作成します。

## 準備
### 集合など
- 0は自然数に含めるものとする。
- ポインタとは自然数のことである。
- 自然数全体の集合をNで表す。
- ポインタ全体の集合をPointerで表す。
- 文字列全体の集合をStringで表す。
- 順序対p=(a,b)に対し、p.left=a, p.right=bとする。

### 項
- 文字0と(と)と+と,からなる要素数5の集合をΣとする。
- Σの元のみからなる0文字以上の文字列全体の集合Σ\*と表す。
- 集合T⊂Σ\*⊂StringとPT⊂Σ\*⊂Stringを以下のように同時に再帰的に定める。
- 1. 0∈Tである。
- 2. 任意のa∈PTとb∈T\\{0}に対し、a+b∈Tである。
- 3. 任意のa∈Tとb∈{0,(0,0,0)}とc∈Tに対し、(a,b,c)∈T∩PTである。

### 抽出
1. t=(a,b,c)∈PTに対し、T.sup=aとする。
2. t=(a,b,c)∈PTに対し、T.sub=bとする。
3. t=(a,b,c)∈PTに対し、T.arg=cとする。
 
### 略記
1. Tの元0を$0と略記する。
2. Tの元(0,0,0)を$1と略記する。
3. 2以上の自然数nに対し、$1+$(n-1)を$nと略記する。
4. PTの元(a,b,c)をψ^a_b(c)と略記するが、この定義では使用しない。

### ユーティリティ関数
#### 文字列全般
- a,b∈Stringに対し、a+bでaとbの連結を表す。
- a∈Stringとn∈Nに対し、a*nでaのn回の繰り返しを表す。
- a∈Stringに対し、a.lengthでaの長さを表す。
```
例
"abc".length = 3
```
- a∈Stringとn∈Nに対し、n<a.lengthであれば、a[n]でaの(n+1)文字目を表す。
```
例
"abc"[-1]は未定義
"abc"[0] = 'a'
"abc"[1] = 'b'
"abc"[2] = 'c'
"abc"[3]は未定義
```
- a∈Stringとn,m∈Nに対し、n<a.lengthかつm<a.lengthであれば、a[n..m]を以下の文字列とする。
  - n>mであれば、a[n..m]は空文字列である。
  - n<=mであれば、a[n..m]はaの(n+1)文字目から(m+1)文字目, both inclusive, の部分文字列である。
```
例
"abc"[0..-1]は未定義
"abc"[0..0] = "a"
"abc"[0..1] = "ab"
"abc"[0..2] = "abc"
"abc"[2..2] = "c"
"abc"[1..0] = ""
"abc"[1..3]は未定義
```
- a∈Stringとn,m∈Nに対し、n<a.lengthかつm<=a.lengthであれば、a[n...m]を以下の文字列とする。
  - m=0であれば、a[n...m]は空文字列である。
  - m>0であれば、a[n...m]はa[n..(m-1)]である。
```
例
"abc"[0...-1]は未定義
"abc"[0...0] = ""
"abc"[0...1] = "a"
"abc"[0...2] = "ab"
"abc"[0...3] = "abc"
"abc"[1...0] = ""
"abc"[1...1] = ""
"abc"[2...3] = "c"
"abc"[1...4]は未定義
```

#### 関数Van(t, cpl, cpr, brl, brr, core, n): String×Pointer×Pointer×Pointer×Pointer×String×N→String の定義
1. (brl <= cpl かつ cpr <= brr かつ cpl <= cpr かつ brl <= brr かつ cpl < t.length かつ cpr < t.length かつ brl < t.length かつ brr < t.length)でなければ、未定義とする。
2. 1.の条件を満たさないとする。
   1. A∈StringをA=t[0...brl]とする。
   2. B∈StringをB=t[brl...cpl]とする。
   3. C∈StringをC=t[(cpr+1)..brr]とする。
   4. D∈StringをD=t[(brr+1)...(t.length)]とする。
   5. Van(t, cpl, cpr, brl, brr, core, n) = A + B\*n + core + C\*n + Dとする。
```
例
Van("abcABCdeDEFdef", 6, 7, 3, 10, "fgh", 0) = "abcfghdef"
Van("abcABCdeDEFdef", 6, 7, 3, 10, "fgh", 1) = "abcABCfghDEFdef"
Van("abcABCdeDEFdef", 6, 7, 3, 10, "fgh", 2) = "abcABCABCfghDEFDEFdef"
Van("abcABCdeDEFdef", 6, 7, 3, 10, "fgh", 3) = "abcABCABCABCfghDEFDEFDEFdef"
```

#### Nodes(t, n): PT×N→P(Pointer^2)の定義
Nodes(t,n)は、集合{(a,b)∈Pointer^2 | a<t.length ∧ b<t.length ∧ s[a..b]∈T}である。

#### DigUp(t, n): PT×N→Pointer^2の定義
DigUp(t,n)は、集合Sを{(a,b)∈Pointer^2 | a<t.length ∧ b<t.length ∧ s[a..b]∈T ∧ (∀i<n (b > DigUp(t,i).right))}としたとき、Sの中で第1要素が最大のものの中で第2要素が最小のものである。

#### IsCNF(t): T×Pointer^2→Booleanの定義
1. t=0ならば、IsCNF(t)=trueとする。
2. もしt∈PTでなければ、tはt\_1∈PTとt\_2∈T\\{0}を用いてt=t\_1+t\_2と書ける。このとき、IsCNF(t)=IsCNF(t\_1)∧IsCNF(t\_2)である。
3. もしt∈PTであれば、tはt\_1,t\_2,t\_3∈Tを用いてt=(t\_1,t\_2,t\_3)と書ける。このとき、IsCNF(t)=IsCNF(t\_1)∧t\_2=0∧IsCNF(t\_3)である。

## 正規化
t∈Tの正規化は、tの一次正規化の二次正規化である。

t∈Tの一次正規化は、tに対して以下の操作を行えなくなるまで行ったものである。
1. (a, b)∈Nodes(t, n)であって、IsCNF(t[a..b])かつt[a..b]∈PTであるものを探す。なければ一次正規化を終了する。
2. 見つけた(a, b)に対し、s=t[a..b]とおく。このとき、sはp,q,r∈Tを用いて(p,q,r)と書ける。
3. tをt[0...a]+"("+"0,"+q+","+r+")"+t[(b+1)...(t.length)]で置き換える。

t∈Tの二次正規化は、tに対して以下の操作を行えなくなるまで行ったものである。
1. あるa,b∈Pointerとs∈Tが存在し、t[a..b]="("+s+",0,"+"("+s+$1+",0,0)"+",0)"であるとする。なければ二次正規化を終了する。
2. tをt[0...a]+"("+s+","+$1+","+$0+")"+t[(b+1)...(t.length)]で置き換える。

## 比較
s,t∈Tに対し、Cmp(s,t): T×T→{-1,0,+1}を以下で定義する。

1. s=0かつt=0ならば、Cmp(s,t)=0である。
2. s=0かつt≠0ならば、Cmp(s,t)=-1である。
3. s≠0かつt=0ならば、Cmp(s,t)=+1である。
4. s≠0かつt≠0とする。
  1. s∈PTかつ￢(t∈PT)ならば、tはa∈PTとb∈T\\{0}を用いてa+bと書ける。このとき、Cmp(s,t)=(　Cmp(s,a)≠0 ? Cmp(s,a) : -1　)である。
  2. ￢(s∈PT)かつt∈PTならば、sはa∈PTとb∈T\\{0}を用いてa+bと書ける。このとき、Cmp(s,t)=(　Cmp(a,t)≠0 ? Cmp(a,t) : +1　)である。
  3. ￢(s∈PT)かつ￢(t∈PT)ならば、sはa∈PTとb∈T\\{0}を用いてa+bと書け、tはc∈PTとd∈T\\{0}を用いてc+dと書ける。このときCmp(s,t)=(　Cmp(a,c)≠0 ? Cmp(a,c) : Cmp(b,d)　)である。
  4. s∈PTかつt∈PTならば、sの正規化をs'、tの正規化をt'とする。
    1. WIP

## 定義
WIP

## 巨大数
WIP
