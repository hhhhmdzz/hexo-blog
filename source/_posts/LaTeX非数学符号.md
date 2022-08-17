---
title: LaTeX非数学符号
author: hhhhmdzz
language: zh-Hans
timezone: Asia/Shanghai
top: false
cover: false
toc: true
mathjax: true
comments: true
date: 2022-07-02 16:45:40
img: /medias/featureimages/latex/1.jpg
coverImg:
password:
summary: LaTeX常用非数学符号
description: LaTeX常用非数学符号
keyworks:
categories: LaTeX
tags: LaTeX
---

本文中所有符号与 `LaTeX` 命令均需添加 `amssymb` 宏包。

## 0. 非数学符号

| `LaTeX`命令 |  符号   |      | `LaTeX`命令 | 符号 |      | `LaTeX`命令  |     符号     |
| :---------: | :-----: | :--: | :---------: | :--: | :--: | :----------: | :----------: |
|   `\dag`    | $\dag$  |      |    `\S`     | $\S$ |      | `\copyright` | $\copyright$ |
|   `\ddag`   | $\ddag$ |      |    `\P`     | $\P$ |      |  `\pounds`   |  $\pounds$   |

## 1. AMS定界符

| `LaTeX`命令 |    符号     |      | `LaTeX`命令 |    符号     |      | `LaTeX`命令 |    符号     |      | `LaTeX`命令 |    符号     |
| :---------: | :---------: | :--: | :---------: | :---------: | :--: | :---------: | :---------: | :--: | :---------: | :---------: |
| `\ulcorner` | $\ulcorner$ |      | `\urcorner` | $\urcorner$ |      | `\llcorner` | $\llcorner$ |      | `\lrcorner` | $\lrcorner$ |
|  `\lvert`   |  $\lvert$   |      |  `\rvert`   |  $\rvert$   |      |  `\lVert`   |  $\lVert$   |      |  `\rVert`   |  $\rVert$   |

## 2. AMS希腊和希伯来字母

| `LaTeX`命令 |    符号    |      | `LaTeX`命令  |    符号     |      | `LaTeX`命令 |  符号   |      | `LaTeX`命令 |   符号    |      | `LaTeX`命令 |   符号   |
| :---------: | :--------: | :--: | :----------: | :---------: | :--: | :---------: | :-----: | :--: | :---------: | :-------: | :--: | :---------: | :------: |
| `\digamma`  | $\digamma$ |      | `$\varkappa` | $\varkappa$ |      |   `\beth`   | $\beth$ |      |  `\daleth`  | $\daleth$ |      |  `\gimel`   | $\gimel$ |

## 3. AMS二元关系符

|   `LaTeX`命令   | 符号 |      |    `LaTeX`命令    | 符号 |      |     `LaTeX`命令     | 符号 |
| :--------------: | :--: | :--: | :---------------: | :--: | :--: | :-----------------: | :--: |
|     `\lessdot`     |     $\lessdot$     |      |      `\gtrdot`      |      $\gtrdot$      |      | `\doteqdot`, `\Doteq` | $\Doteq$ |
|    `\leqslant`    |    $\leqslant$    |      |     `\geqslant`     |     $\geqslant$     |      |    `\risingdotseq`    |    $\risingdotseq$    |
|   `\eqslantless`   |   $\eqslantless$   |      |    `\eqslantgtr`    |    $\eqslantgtr$    |      |   `\fallingdotseq`   |   $\fallingdotseq$   |
|      `\leqq`      |      $\leqq$      |      |       `\geqq`       |       $\geqq$       |      |       `\eqcirc`       |       $\eqcirc$       |
| `\lll`, `\llless` |       $\lll$       |      |  `\ggg`, `\gggtr`  |  $\ggg$  |      |       `\circeq`       |       $\circeq$       |
|     `\lesssim`     |     $\lesssim$     |      |      `\gtrsim`      |      $\gtrsim$      |      |     `\triangleq`     |     $\triangleq$     |
|   `\lessapprox`   |   $\lessapprox$   |      |    `\gtrapprox`    |    $\gtrapprox$    |      |       `\bumpeq`       |       $\bumpeq$       |
|     `\lessgtr`     |     $\lessgtr$     |      |     `\gtrless`     |     $\gtrless$     |      |       `\Bumpeq`       |       $\Bumpeq$       |
|    `\lesseqgtr`    |    $\lesseqgtr$    |      |    `\gtreqless`    |    $\gtreqless$    |      |      `\thicksim`      |      $\thicksim$      |
|   `\lesseqqgtr`   |   $\lesseqqgtr$   |      |    `\gtreqqless`    |    $\gtreqqless$    |      |    `\thickapprox`    |    $\thickapprox$    |
|   `\preccurlyeq`   |   $\preccurlyeq$   |      |   `\succcurlyeq`   |   $\succcurlyeq$   |      |      `\approxeq`      |      $\approxeq$      |
|   `\curlyeqprec`   |   $\curlyeqprec$   |      |   `\curlyeqsucc`   |   $\curlyeqsucc$   |      |      `\backsim`      |      $\backsim$      |
|     `\precsim`     |     $\precsim$     |      |     `\succsim`     |     $\succsim$     |      |     `\backsimeq`     |     $\backsimeq$     |
|   `\precapprox`   |   $\precapprox$   |      |    `\succapprox`    |    $\succapprox$    |      |       `\vDash`       |       $\vDash$       |
|    `\subseteqq`    |    $\subseteqq$    |      |    `\supseteqq`    |    $\supseteqq$    |      |       `\Vdash`       |       $\Vdash$       |
|     `\Subset`     |     $\Subset$     |      |      `\Supset`      |      $\Supset$      |      |       `\Vvdash`       |       $\Vvdash$       |
|    `\sqsubset`    |    $\sqsubset$    |      |     `\sqsupset`     |     $\sqsupset$     |      |    `\backepsilon`    |    $\backepsilon$    |
|    `\therefore`    |    $\therefore$    |      |     `\because`     |     $\because$     |      |     `\varpropto`     |     $\varpropto$     |
|    `\shortmid`    |    $\shortmid$    |      |  `\shortparallel`  |  $\shortparallel$  |      |      `\between`      |      $\between$      |
|   `\smallsmile`   |   $\smallsmile$   |      |    `\smallfrown`    |    $\smallfrown$    |      |     `\pitchfork`     |     $\pitchfork$     |
| `\vartriangleleft` | $\vartriangleleft$ |      | `\vartriangleright` | $\vartriangleright$ |      | `\blacktriangleleft` | $\blacktriangleleft$ |
| `\trianglelefteq` | $\trianglelefteq$ |      | `\trianglerighteq` | $\trianglerighteq$ |      | `\blacktriangleright` | $\blacktriangleright$ |

## 4. AMS箭头

|    `LaTeX`命令     | 符号 |      |    `LaTeX`命令     | 符号 |      |     `LaTeX`命令      | 符号 |
| :----------------: | :--: | :--: | :----------------: | :--: | :--: | :------------------: | :--: |
|   `\dashleftarrow`   |   $\dashleftarrow$   |      |  `\dashrightarrow`  |  $\dashrightarrow$  |  |      `\multimap`      |      $\multimap$      |
|  `\leftleftarrows`  |  $\leftleftarrows$  |      | `\rightrightarrows` | $\rightrightarrows$ |      |     `\upuparrows`     |     $\upuparrows$     |
|  `\leftrightarrows`  |  $\leftrightarrows$  |      |  `\rightleftarrows`  |  $\rightleftarrows$  |      |   `\downdownarrows`   |   $\downdownarrows$   |
|    `\Lleftarrow`    |    $\Lleftarrow$    |      |    `\Rrightarrow`    |    $\Rrightarrow$    |      |    `\upharpoonleft`    |    $\upharpoonleft$    |
| `\twoheadleftarrow` | $\twoheadleftarrow$ |      | `\twoheadrightarrow` | $\twoheadrightarrow$ |      |   `\upharpoonright`   |   $\upharpoonright$   |
|   `\leftarrowtail`   |   $\leftarrowtail$   |      |  `\rightarrowtail`  |  $\rightarrowtail$  |      |   `\downharpoonleft`   |   $\downharpoonleft$   |
| `\leftrightharpoons` | $\leftrightharpoons$ |      | `\rightleftharpoons` | $\rightleftharpoons$ |      |  `\downharpoonright`  |  $\downharpoonright$  |
|        `\Lsh`        |        $\Lsh$        |      |        `\Rsh`        |        $\Rsh$        |      |   `\rightsquigarrow`   |   $\rightsquigarrow$   |
|   `\looparrowleft`   |   $\looparrowleft$   |      |  `\looparrowright`  |  $\looparrowright$  |      | `\leftrightsquigarrow` | $\leftrightsquigarrow$ |
|  `\curvearrowleft`  |  $\curvearrowleft$  |      |  `\curvearrowright`  |  $\curvearrowright$  |      |                      |                      |
|  `\circlearrowleft`  |  $\circlearrowleft$  |      | `\circlearrowright` | $\circlearrowright$ |      |                      |                      |

## 5. AMS二元否定关系符和箭头

|  `LaTeX`命令  | 符号 |      |  `LaTeX`命令  | 符号 |      |   `LaTeX`命令    | 符号 |
| :-----------: | :--: | :--: | :-----------: | :--: | :--: | :--------------: | :--: |
|    `\nless`    |   $\nless$  ||`\ngtr`|$\ngtr$||`\varsubsetneqq`|$\varsubsetneqq$|
|     `\lneq`     | $\lneq$ ||`\gneq`|$\gneq$||`\varsupsetneqq`|$\varsupsetneqq$|
|     `\nleq`     |     $\nleq$     |      |     `\ngeq`     |     $\ngeq$     |      |   `\nsubseteqq`   |   $\nsubseteqq$   |
|  `\nleqslant`  |  $\nleqslant$  |      |  `\ngeqslant`  |  $\ngeqslant$  |      |   `\nsupseteqq`   |   $\nsupseteqq$   |
|    ``\lneqq`    |    $\lneqq$    |      |    `\gneqq`    |    $\gneqq$    |      |      `\nmid`      |      $\nmid$      |
|  `\lvertneqq`  |  $\lvertneqq$  |      |  `\gvertneqq`  |  $\gvertneqq$  |      |    `\nparallel`    |    $\nparallel$    |
|    `\nleqq`    |    $\nleqq$    |      |    `\ngeqq`    |    $\ngeqq$    |      |    `\nshortmid`    |    $\nshortmid$    |
|    `\lnsim`    |    $\lnsim$    |      |    `\gnsim`    |    $\gnsim$    |      | `\nshortparallel` | $\nshortparallel$ |
|   `\lnapprox`   |   $\lnapprox$   |      |   `\gnapprox`   |   $\gnapprox$   |      |      `\nsim`      |      $\nsim$      |
|    `\nprec`    |    $\nprec$    |      |    `\nsucc`    |    $\nsucc$    |      |      `\ncong`      |      $\ncong$      |
|   `\npreceq`   |   $\npreceq$   |      |   `\nsucceq`   |   $\nsucceq$   |      |     `\nvdash`     |     $\nvdash$     |
|   `\precneqq`   |   $\precneqq$   |      |   `\succneqq`   |   $\succneqq$   |      |     `\nvDash`     |     $\nvDash$     |
|   `\precnsim`   |   $\precnsim$   |      |   `\succnsim`   |   $\succnsim$   |      |     `\nVdash`     |     $\nVdash$     |
| `\precnapprox` | $\precnapprox$ |      | `\succnapprox` | $\succnapprox$ |      |     `\nVDash`     |     $\nVDash$     |
|  `\subsetneq`  |  $\subsetneq$  |      |  `\supsetneq`  |  $\supsetneq$  |      |  `\ntriangleleft`  |  $\ntriangleleft$  |
| `\varsubsetneq` | $\varsubsetneq$ |      | `\varsupsetneq` | $\varsupsetneq$ |      | `\ntriangleright` | $\ntriangleright$ |
|  `\nsubseteq`  |  $\nsubseteq$  |      |  `\nsupseteq`  |  $\nsupseteq$  |      | `\ntrianglelefteq` | $\ntrianglelefteq$ |
|  `\nleftarrow`  |  $\nleftarrow$  |      | `\nrightarrow` | $\nrightarrow$ |      | `\nleftrightarrow` | $\nleftrightarrow$ |
|  `\nLeftarrow`  |  $\nLeftarrow$  |      | `\nRightarrow` | $\nRightarrow$ |      | `\nLeftrightarrow` | $\nLeftrightarrow$ |


## 6. AMS二元运算符


|    `LaTeX`命令     | 符号 |      |    `LaTeX`命令     | 符号 |      |   `LaTeX`命令   | 符号 |
| :----------------: | :--: | :--: | :----------------: | :--: | :--: | :-------------: | :--: |
|      `\dotplus`      |      $\dotplus$      |      |     `\centerdot`     |    $\centerdot$    |      |    `\intercal`    |    $\intercal$    |
|      `\ltimes`      |      $\ltimes$      |      |      `\rtimes`      |      $\rtimes$      |      | `\divideontimes` | $\divideontimes$ |
| `\Cup`, `\doublecup` | $\Cup$ |      | `\Cap`, `\doublecap` | $\Cap$ |      | `\smallsetminus` | $\smallsetminus$ |
|      `\veebar`      |      $\veebar$      |      |     `\barwedge`     |     $\barwedge$     |      | `\doublebarwedge` | $\doublebarwedge$ |
|      `\boxplus`      |      $\boxplus$      |      |     `\boxminus`     |     $\boxminus$     |      |  `\circleddash`  |  $\circleddash$  |
|     `\boxtimes`     |     $\boxtimes$     |      |      `\boxdot`      |      $\boxdot$      |      |  `\circledcirc`  |  $\circledcirc$  |
|  `\leftthreetimes`  |  $\leftthreetimes$  |      |  `\rightthreetimes`  | $\rightthreetimes$ |      |   `\circledast`   |   $\circledast$   |
|     `\curlyvee`     |     $\curlyvee$     |      |    `\curlywedge`    |    $\curlywedge$    |      |                 |                 |

## 7. AMS其他符号

|  `LaTeX`命令  | 符号 |      |    `LaTeX`命令     | 符号 |      | `LaTeX`命令 | 符号 |
| :-----------: | :--: | :--: | :----------------: | :--: | :--: | :---------: | :--: |
|     `\hbar`     |     $\hbar$     |      |      `\hslash`      |      $\hslash$      |      |    `\Bbbk`    |    $\Bbbk$    |
|    `\square`    |    $\square$    |      |    `\blacksquare`    |    $\blacksquare$    |      |  `\circledS`  |  $\circledS$  |
| `\vartriangle` | $\vartriangle$ |      |   `\blacktriangle`   |   $\blacktriangle$   |      | `\complement` | $\complement$ |
| `\triangledown` | $\triangledown$ |      | `\blacktriangledown` | $\blacktriangledown$ |      |    `\Game`    |    $\Game$    |
|   `\lozenge`   |   $\lozenge$   |      |   `\blacklozenge`   |   $\blacklozenge$   |      |  `\bigstar`  |  $\bigstar$  |
|    `\angle`    |    $\angle$    |      |   `\measuredangle`   |   $\measuredangle$   |      |  `\bigstar`  |  $\bigstar$  |
|    `\diagup`    |    $\diagup$    |      |     `\diagdown`     |     $\diagdown$     |      | `\backprime` | $\backprime$ |
|   `\nexists`   |   $\nexists$   |      |       `\Finv`       |       $\Finv$       |      | `\varnothing` | $\varnothing$ |
|     `\eth`     |     $\eth$     |      |        `\mho`        |        $\mho$        |      |             |             |

参考：

1. [数学符号表](http://persoweb.whu.edu.cn/mathsymb.pdf)

2. [LATEX Mathematical Symbols](https://www.egr.msu.edu/~renjian/LaTeX-Math-Symbols.pdf)

3. [LaTeX:Symbols](https://artofproblemsolving.com/wiki/index.php/LaTeX:Symbols)

4. [LaTeX:Commands](https://artofproblemsolving.com/wiki/index.php/LaTeX:Commands)

本文源码：

```markdown

本文中所有符号与 `LaTeX` 命令均需添加 `amssymb` 宏包。

## 0. 非数学符号

| `LaTeX`命令 |  符号   |      | `LaTeX`命令 | 符号 |      | `LaTeX`命令  |     符号     |
| :---------: | :-----: | :--: | :---------: | :--: | :--: | :----------: | :----------: |
|   `\dag`    | $\dag$  |      |    `\S`     | $\S$ |      | `\copyright` | $\copyright$ |
|   `\ddag`   | $\ddag$ |      |    `\P`     | $\P$ |      |  `\pounds`   |  $\pounds$   |

## 1. AMS定界符

| `LaTeX`命令 |    符号     |      | `LaTeX`命令 |    符号     |      | `LaTeX`命令 |    符号     |      | `LaTeX`命令 |    符号     |
| :---------: | :---------: | :--: | :---------: | :---------: | :--: | :---------: | :---------: | :--: | :---------: | :---------: |
| `\ulcorner` | $\ulcorner$ |      | `\urcorner` | $\urcorner$ |      | `\llcorner` | $\llcorner$ |      | `\lrcorner` | $\lrcorner$ |
|  `\lvert`   |  $\lvert$   |      |  `\rvert`   |  $\rvert$   |      |  `\lVert`   |  $\lVert$   |      |  `\rVert`   |  $\rVert$   |

## 2. AMS希腊和希伯来字母

| `LaTeX`命令 |    符号    |      | `LaTeX`命令  |    符号     |      | `LaTeX`命令 |  符号   |      | `LaTeX`命令 |   符号    |      | `LaTeX`命令 |   符号   |
| :---------: | :--------: | :--: | :----------: | :---------: | :--: | :---------: | :-----: | :--: | :---------: | :-------: | :--: | :---------: | :------: |
| `\digamma`  | $\digamma$ |      | `$\varkappa` | $\varkappa$ |      |   `\beth`   | $\beth$ |      |  `\daleth`  | $\daleth$ |      |  `\gimel`   | $\gimel$ |

## 3. AMS二元关系符

|   `LaTeX`命令   | 符号 |      |    `LaTeX`命令    | 符号 |      |     `LaTeX`命令     | 符号 |
| :--------------: | :--: | :--: | :---------------: | :--: | :--: | :-----------------: | :--: |
|     `\lessdot`     |     $\lessdot$     |      |      `\gtrdot`      |      $\gtrdot$      |      | `\doteqdot`, `\Doteq` | $\Doteq$ |
|    `\leqslant`    |    $\leqslant$    |      |     `\geqslant`     |     $\geqslant$     |      |    `\risingdotseq`    |    $\risingdotseq$    |
|   `\eqslantless`   |   $\eqslantless$   |      |    `\eqslantgtr`    |    $\eqslantgtr$    |      |   `\fallingdotseq`   |   $\fallingdotseq$   |
|      `\leqq`      |      $\leqq$      |      |       `\geqq`       |       $\geqq$       |      |       `\eqcirc`       |       $\eqcirc$       |
| `\lll`, `\llless` |       $\lll$       |      |  `\ggg`, `\gggtr`  |  $\ggg$  |      |       `\circeq`       |       $\circeq$       |
|     `\lesssim`     |     $\lesssim$     |      |      `\gtrsim`      |      $\gtrsim$      |      |     `\triangleq`     |     $\triangleq$     |
|   `\lessapprox`   |   $\lessapprox$   |      |    `\gtrapprox`    |    $\gtrapprox$    |      |       `\bumpeq`       |       $\bumpeq$       |
|     `\lessgtr`     |     $\lessgtr$     |      |     `\gtrless`     |     $\gtrless$     |      |       `\Bumpeq`       |       $\Bumpeq$       |
|    `\lesseqgtr`    |    $\lesseqgtr$    |      |    `\gtreqless`    |    $\gtreqless$    |      |      `\thicksim`      |      $\thicksim$      |
|   `\lesseqqgtr`   |   $\lesseqqgtr$   |      |    `\gtreqqless`    |    $\gtreqqless$    |      |    `\thickapprox`    |    $\thickapprox$    |
|   `\preccurlyeq`   |   $\preccurlyeq$   |      |   `\succcurlyeq`   |   $\succcurlyeq$   |      |      `\approxeq`      |      $\approxeq$      |
|   `\curlyeqprec`   |   $\curlyeqprec$   |      |   `\curlyeqsucc`   |   $\curlyeqsucc$   |      |      `\backsim`      |      $\backsim$      |
|     `\precsim`     |     $\precsim$     |      |     `\succsim`     |     $\succsim$     |      |     `\backsimeq`     |     $\backsimeq$     |
|   `\precapprox`   |   $\precapprox$   |      |    `\succapprox`    |    $\succapprox$    |      |       `\vDash`       |       $\vDash$       |
|    `\subseteqq`    |    $\subseteqq$    |      |    `\supseteqq`    |    $\supseteqq$    |      |       `\Vdash`       |       $\Vdash$       |
|     `\Subset`     |     $\Subset$     |      |      `\Supset`      |      $\Supset$      |      |       `\Vvdash`       |       $\Vvdash$       |
|    `\sqsubset`    |    $\sqsubset$    |      |     `\sqsupset`     |     $\sqsupset$     |      |    `\backepsilon`    |    $\backepsilon$    |
|    `\therefore`    |    $\therefore$    |      |     `\because`     |     $\because$     |      |     `\varpropto`     |     $\varpropto$     |
|    `\shortmid`    |    $\shortmid$    |      |  `\shortparallel`  |  $\shortparallel$  |      |      `\between`      |      $\between$      |
|   `\smallsmile`   |   $\smallsmile$   |      |    `\smallfrown`    |    $\smallfrown$    |      |     `\pitchfork`     |     $\pitchfork$     |
| `\vartriangleleft` | $\vartriangleleft$ |      | `\vartriangleright` | $\vartriangleright$ |      | `\blacktriangleleft` | $\blacktriangleleft$ |
| `\trianglelefteq` | $\trianglelefteq$ |      | `\trianglerighteq` | $\trianglerighteq$ |      | `\blacktriangleright` | $\blacktriangleright$ |

## 4. AMS箭头

|    `LaTeX`命令     | 符号 |      |    `LaTeX`命令     | 符号 |      |     `LaTeX`命令      | 符号 |
| :----------------: | :--: | :--: | :----------------: | :--: | :--: | :------------------: | :--: |
|   `\dashleftarrow`   |   $\dashleftarrow$   |      |  `\dashrightarrow`  |  $\dashrightarrow$  |  |      `\multimap`      |      $\multimap$      |
|  `\leftleftarrows`  |  $\leftleftarrows$  |      | `\rightrightarrows` | $\rightrightarrows$ |      |     `\upuparrows`     |     $\upuparrows$     |
|  `\leftrightarrows`  |  $\leftrightarrows$  |      |  `\rightleftarrows`  |  $\rightleftarrows$  |      |   `\downdownarrows`   |   $\downdownarrows$   |
|    `\Lleftarrow`    |    $\Lleftarrow$    |      |    `\Rrightarrow`    |    $\Rrightarrow$    |      |    `\upharpoonleft`    |    $\upharpoonleft$    |
| `\twoheadleftarrow` | $\twoheadleftarrow$ |      | `\twoheadrightarrow` | $\twoheadrightarrow$ |      |   `\upharpoonright`   |   $\upharpoonright$   |
|   `\leftarrowtail`   |   $\leftarrowtail$   |      |  `\rightarrowtail`  |  $\rightarrowtail$  |      |   `\downharpoonleft`   |   $\downharpoonleft$   |
| `\leftrightharpoons` | $\leftrightharpoons$ |      | `\rightleftharpoons` | $\rightleftharpoons$ |      |  `\downharpoonright`  |  $\downharpoonright$  |
|        `\Lsh`        |        $\Lsh$        |      |        `\Rsh`        |        $\Rsh$        |      |   `\rightsquigarrow`   |   $\rightsquigarrow$   |
|   `\looparrowleft`   |   $\looparrowleft$   |      |  `\looparrowright`  |  $\looparrowright$  |      | `\leftrightsquigarrow` | $\leftrightsquigarrow$ |
|  `\curvearrowleft`  |  $\curvearrowleft$  |      |  `\curvearrowright`  |  $\curvearrowright$  |      |                      |                      |
|  `\circlearrowleft`  |  $\circlearrowleft$  |      | `\circlearrowright` | $\circlearrowright$ |      |                      |                      |

## 5. AMS二元否定关系符和箭头

|  `LaTeX`命令  | 符号 |      |  `LaTeX`命令  | 符号 |      |   `LaTeX`命令    | 符号 |
| :-----------: | :--: | :--: | :-----------: | :--: | :--: | :--------------: | :--: |
|    `\nless`    |   $\nless$  ||`\ngtr`|$\ngtr$||`\varsubsetneqq`|$\varsubsetneqq$|
|     `\lneq`     | $\lneq$ ||`\gneq`|$\gneq$||`\varsupsetneqq`|$\varsupsetneqq$|
|     `\nleq`     |     $\nleq$     |      |     `\ngeq`     |     $\ngeq$     |      |   `\nsubseteqq`   |   $\nsubseteqq$   |
|  `\nleqslant`  |  $\nleqslant$  |      |  `\ngeqslant`  |  $\ngeqslant$  |      |   `\nsupseteqq`   |   $\nsupseteqq$   |
|    ``\lneqq`    |    $\lneqq$    |      |    `\gneqq`    |    $\gneqq$    |      |      `\nmid`      |      $\nmid$      |
|  `\lvertneqq`  |  $\lvertneqq$  |      |  `\gvertneqq`  |  $\gvertneqq$  |      |    `\nparallel`    |    $\nparallel$    |
|    `\nleqq`    |    $\nleqq$    |      |    `\ngeqq`    |    $\ngeqq$    |      |    `\nshortmid`    |    $\nshortmid$    |
|    `\lnsim`    |    $\lnsim$    |      |    `\gnsim`    |    $\gnsim$    |      | `\nshortparallel` | $\nshortparallel$ |
|   `\lnapprox`   |   $\lnapprox$   |      |   `\gnapprox`   |   $\gnapprox$   |      |      `\nsim`      |      $\nsim$      |
|    `\nprec`    |    $\nprec$    |      |    `\nsucc`    |    $\nsucc$    |      |      `\ncong`      |      $\ncong$      |
|   `\npreceq`   |   $\npreceq$   |      |   `\nsucceq`   |   $\nsucceq$   |      |     `\nvdash`     |     $\nvdash$     |
|   `\precneqq`   |   $\precneqq$   |      |   `\succneqq`   |   $\succneqq$   |      |     `\nvDash`     |     $\nvDash$     |
|   `\precnsim`   |   $\precnsim$   |      |   `\succnsim`   |   $\succnsim$   |      |     `\nVdash`     |     $\nVdash$     |
| `\precnapprox` | $\precnapprox$ |      | `\succnapprox` | $\succnapprox$ |      |     `\nVDash`     |     $\nVDash$     |
|  `\subsetneq`  |  $\subsetneq$  |      |  `\supsetneq`  |  $\supsetneq$  |      |  `\ntriangleleft`  |  $\ntriangleleft$  |
| `\varsubsetneq` | $\varsubsetneq$ |      | `\varsupsetneq` | $\varsupsetneq$ |      | `\ntriangleright` | $\ntriangleright$ |
|  `\nsubseteq`  |  $\nsubseteq$  |      |  `\nsupseteq`  |  $\nsupseteq$  |      | `\ntrianglelefteq` | $\ntrianglelefteq$ |
|  `\nleftarrow`  |  $\nleftarrow$  |      | `\nrightarrow` | $\nrightarrow$ |      | `\nleftrightarrow` | $\nleftrightarrow$ |
|  `\nLeftarrow`  |  $\nLeftarrow$  |      | `\nRightarrow` | $\nRightarrow$ |      | `\nLeftrightarrow` | $\nLeftrightarrow$ |


## 6. AMS二元运算符


|    `LaTeX`命令     | 符号 |      |    `LaTeX`命令     | 符号 |      |   `LaTeX`命令   | 符号 |
| :----------------: | :--: | :--: | :----------------: | :--: | :--: | :-------------: | :--: |
|      `\dotplus`      |      $\dotplus$      |      |     `\centerdot`     |    $\centerdot$    |      |    `\intercal`    |    $\intercal$    |
|      `\ltimes`      |      $\ltimes$      |      |      `\rtimes`      |      $\rtimes$      |      | `\divideontimes` | $\divideontimes$ |
| `\Cup`, `\doublecup` | $\Cup$ |      | `\Cap`, `\doublecap` | $\Cap$ |      | `\smallsetminus` | $\smallsetminus$ |
|      `\veebar`      |      $\veebar$      |      |     `\barwedge`     |     $\barwedge$     |      | `\doublebarwedge` | $\doublebarwedge$ |
|      `\boxplus`      |      $\boxplus$      |      |     `\boxminus`     |     $\boxminus$     |      |  `\circleddash`  |  $\circleddash$  |
|     `\boxtimes`     |     $\boxtimes$     |      |      `\boxdot`      |      $\boxdot$      |      |  `\circledcirc`  |  $\circledcirc$  |
|  `\leftthreetimes`  |  $\leftthreetimes$  |      |  `\rightthreetimes`  | $\rightthreetimes$ |      |   `\circledast`   |   $\circledast$   |
|     `\curlyvee`     |     $\curlyvee$     |      |    `\curlywedge`    |    $\curlywedge$    |      |                 |                 |

## 7. AMS其他符号

|  `LaTeX`命令  | 符号 |      |    `LaTeX`命令     | 符号 |      | `LaTeX`命令 | 符号 |
| :-----------: | :--: | :--: | :----------------: | :--: | :--: | :---------: | :--: |
|     `\hbar`     |     $\hbar$     |      |      `\hslash`      |      $\hslash$      |      |    `\Bbbk`    |    $\Bbbk$    |
|    `\square`    |    $\square$    |      |    `\blacksquare`    |    $\blacksquare$    |      |  `\circledS`  |  $\circledS$  |
| `\vartriangle` | $\vartriangle$ |      |   `\blacktriangle`   |   $\blacktriangle$   |      | `\complement` | $\complement$ |
| `\triangledown` | $\triangledown$ |      | `\blacktriangledown` | $\blacktriangledown$ |      |    `\Game`    |    $\Game$    |
|   `\lozenge`   |   $\lozenge$   |      |   `\blacklozenge`   |   $\blacklozenge$   |      |  `\bigstar`  |  $\bigstar$  |
|    `\angle`    |    $\angle$    |      |   `\measuredangle`   |   $\measuredangle$   |      |  `\bigstar`  |  $\bigstar$  |
|    `\diagup`    |    $\diagup$    |      |     `\diagdown`     |     $\diagdown$     |      | `\backprime` | $\backprime$ |
|   `\nexists`   |   $\nexists$   |      |       `\Finv`       |       $\Finv$       |      | `\varnothing` | $\varnothing$ |
|     `\eth`     |     $\eth$     |      |        `\mho`        |        $\mho$        |      |             |             |

参考：

1. [数学符号表](http://persoweb.whu.edu.cn/mathsymb.pdf)

2. [LATEX Mathematical Symbols](https://www.egr.msu.edu/~renjian/LaTeX-Math-Symbols.pdf)

3. [LaTeX:Symbols](https://artofproblemsolving.com/wiki/index.php/LaTeX:Symbols)

4. [LaTeX:Commands](https://artofproblemsolving.com/wiki/index.php/LaTeX:Commands)

```

