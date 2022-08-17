---
title: LaTeX数学符号
author: hhhhmdzz
language: zh-Hans
timezone: Asia/Shanghai
top: true
cover: true
toc: true
mathjax: true
comments: true
date: 2022-07-02 16:42:29
img: /medias/featureimages/latex/0.jpg
coverImg:
password:
summary: LaTeX常用数学符号
description: LaTeX常用数学符号
keyworks:
categories: LaTeX
tags: LaTeX
---

<font color='red'></font>



两个 LaTeX 工具（其实是一个）：[https://latex.codecogs.com/](https://latex.codecogs.com/) 和 [https://latex.codecogs.com/eqneditor/editor.php](https://latex.codecogs.com/eqneditor/editor.php)



注：红色命令需要导入宏包 `latexsym`

## 1. 数学模式重音符

| `LaTeX`命令 |  **符号**   |      |   `LaTeX`命令   |    **符号**     |
| :---------: | :---------: | :--: | :-------------: | :-------------: |
|  `\hat{a}`  |  $\hat{a}$  |      |  `\widehat{A}`  |  $\widehat{A}$  |
| `\tilde{a}` | $\tilde{a}$ |      | `\widetilde{A}` | $\widetilde{A}$ |
|  `\dot{a}`  |  $\dot{a}$  |      |   `\ddot{a}`    |   $\ddot{a}$    |
|  `\vec{a}`  |  $\vec{a}$  |      |   `\breve{a}`   |   $\breve{a}$   |
|  `\bar{a}`  |  $\bar{a}$  |      |   `\acute{a}`   |   $\acute{a}$   |
| `\check{a}` | $\check{a}$ |      |   `\grave{a}`   |   $\grave{a}$   |

## 2. 希腊字母

| 大写`LaTeX`命令 |      小写`LaTeX`命令      |   大写    |           小写            |      | 大写`LaTeX`命令 |    小写`LaTeX`命令    |    大写    |         小写          |
| :-------------: | :-----------------------: | :-------: | :-----------------------: | :--: | :-------------: | :-------------------: | :--------: | :-------------------: |
|       `A`       |         `\alpha`          |    $A$    |         $\alpha$          |      |       `N`       |         `\nu`         |    $N$     |         $\nu$         |
|       `B`       |          `\beta`          |    $B$    |          $\beta$          |      |      `\Xi`      |         `\xi`         |   $\Xi$    |         $\xi$         |
|    `\Gamma`     |         `\gamma`          | $\Gamma$  |         $\gamma$          |      |       `O`       |          `o`          |    $O$     |          $o$          |
|    `\Delta`     |         `\delta`          | $\Delta$  |         $\delta$          |      |      `\Pi`      |    `\pi`, `\varpi`    |   $\Pi$    |    $\pi$, $\varpi$    |
|       `E`       | `\epsilon`, `\varepsilon` |    $E$    | $\epsilon$, $\varepsilon$ |      |       `P`       |   `\rho`, `\varrho`   |    $P$     |   $\rho$, $\varrho$   |
|       `Z`       |          `\zeta`          |    $Z$    |          $\zeta$          |      |    `\Sigma`     | `\sigma`, `\varsigma` |  $\Sigma$  | $\sigma$, $\varsigma$ |
|       `H`       |          `\eta`           |    $H$    |          $\eta$           |      |       `T`       |        `\tau`         |    $T$     |        $\tau$         |
|    `\Theta`     |   `\theta`, `\vartheta`   | $\Theta$  |   $\theta$, $\vartheta$   |      |   `\Upsilon`    |      `\upsilon`       | $\Upsilon$ |      $\upsilon$       |
|       `I`       |          `\iota`          |    $I$    |          $\iota$          |      |     `\Phi`      |   `\phi`, `\varphi`   |   $\Phi$   |   $\phi$, $\varphi$   |
|       `K`       |         `\kappa`          |    $K$    |         $\kappa$          |      |       `X`       |        `\chi`         |    $X$     |        $\chi$         |
|    `\Lambda`    |         `\lambda`         | $\Lambda$ |         $\lambda$         |      |     `\Psi`      |        `\psi`         |   $\Psi$   |        $\psi$         |
|       `M`       |           `\mu`           |    $M$    |           $\mu$           |      |    `\Omega`     |       `\omega`        |  $\Omega$  |       $\omega$        |

## 3. 二元关系符

|             `LaTeX`命令              |                 符号                 |      |             `LaTeX`命令              |                 符号                 |      |           `LaTeX`命令            |               符号               |
| :----------------------------------: | :----------------------------------: | :--: | :----------------------------------: | :----------------------------------: | :--: | :------------------------------: | :------------------------------: |
|                 `<`                  |                 $<$                  |      |                 `>`                  |                 $>$                  |      |               `=`                |               $=$                |
|            `\leq`, `\le`             |                $\le$                 |      |            `\geq`, `\ge`             |                $\geq$                |      |             `\equiv`             |             $\equiv$             |
|                `\ll`                 |                $\ll$                 |      |                `\gg`                 |                $\gg$                 |      |             `\doteq`             |             $\doteq$             |
|               `\prec`                |               $\prec$                |      |               `\succ`                |               $\succ$                |      |              `\sim`              |              $\sim$              |
|              `\preceq`               |              $\preceq$               |      |              `\succeq`               |              $\succeq$               |      |             `\simeq`             |             $\simeq$             |
|              `\subset`               |              $\subset$               |      |              `\supset`               |              $\supset$               |      |            `\approx`             |            $\approx$             |
|             `\subseteq`              |             $\subseteq$              |      |             `\supseteq`              |             $\supseteq$              |      |             `\cong`              |             $\cong$              |
| <font color='red'>`\sqsubset`</font> | <font color='red'>$\sqsubset$</font> |      | <font color='red'>`\sqsupset`</font> | <font color='red'>$\sqsupset$</font> |      | <font color='red'>`\Join`</font> | <font color='red'>$\Join$</font> |
|            `\sqsubseteq`             |            $\sqsubseteq$             |      |            `\sqsupseteq`             |            $\sqsupseteq$             |      |            `\bowtie`             |            $\bowtie$             |
|                `\in`                 |                $\in$                 |      |            `\ni`, `\owns`            |                $\ni$                 |      |            `\propto`             |            $\propto$             |
|               `\vdash`               |               $\vdash$               |      |               `\dashv`               |               $\dashv$               |      |            `\models`             |            $\models$             |
|                `\mid`                |                $\mid$                |      |             `\parallel`              |             $\parallel$              |      |             `\perp`              |             $\perp$              |
|               `\smile`               |               $\smile$               |      |               `\frown`               |               $\frown$               |      |             `\asymp`             |             $\asymp$             |
|                 `:`                  |                 $:$                  |      |               `\notin`               |               $\notin$               |      |          `\neq`, `\ne`           |              $\neq$              |

## 4. 二元运算符

|            `LaTeX`命令            |               符号                |      |            `LaTeX`命令            |               符号                |      |   `LaTeX`命令    |       符号       |
| :-------------------------------: | :-------------------------------: | :--: | :-------------------------------: | :-------------------------------: | :--: | :--------------: | :--------------: |
|               `\pm`               |               $\pm$               |      |               `\mp`               |               $\mp$               |      | `\triangleleft`  | $\triangleleft$  |
|              `\cdot`              |              $\cdot$              |      |              `\div`               |              $\div$               |      | `\triangleright` | $\triangleright$ |
|             `\times`              |             $\times$              |      |            `\setminus`            |            $\setminus$            |      |     `\star`      |     $\star$      |
|              `\cup`               |              $\cup$               |      |              `\cap`               |              $\cap$               |      |      `\ast`      |      $\ast$      |
|             `\sqcup`              |             $\sqcup$              |      |             `\sqcap`              |             $\sqcap$              |      |     `\circ`      |     $\circ$      |
|          `\vee`, `\lor`           |              $\vee$               |      |         `\wedge`, `land`          |              $\land$              |      |    `\bullet`     |    $\bullet$     |
|             `\oplus`              |             $\oplus$              |      |             `\ominus`             |             $\ominus$             |      |    `\diamond`    |    $\diamond$    |
|              `\odot`              |              $\odot$              |      |             `\oslash`             |             $\oslash$             |      |     `\uplus`     |     $\uplus$     |
|            `\otimes`     |     $\otimes$     ||`\bigcirc`|$\bigcirc$||`\amalg`|$\amalg$|
|         `\bigtriangleup`          |         $\bigtriangleup$          |      |        `\bigtriangledown`         |        $\bigtriangledown$         |      |    `\dagger`     |    $\dagger$     |
|  <font color='red'>`\lhd`</font>  |  <font color='red'>$\lhd$</font>  |      |  <font color='red'>`\rhd`</font>  |  <font color='red'>$\rhd$</font>  |      |    `\ddagger`    |    $\ddagger$    |
| <font color='red'>`\unlhd`</font> | <font color='red'>$\unlhd$</font> |      | <font color='red'>`\unrhd`</font> | <font color='red'>$\unrhd$</font> |      |      `\wr`       |      $\wr$       |

## 5. 大尺度运算符

| `LaTeX`命令 |   符号    |      | `LaTeX`命令 |    符号     |      | `LaTeX`命令 |    符号     |      | `LaTeX`命令  |     符号     |
| :---------: | :-------: | :--: | :---------: | :---------: | :--: | :---------: | :---------: | :--: | :----------: | :----------: |
|   `\sum`    |  $\sum$   |      |  `\bigcup`  |  $\bigcup$  |      |  `\bigvee`  |  $\bigvee$  |      | `\bigoplus`  | $\bigoplus$  |
|   `\prod`   |  $\prod$  |      |  `\bigcap`  |  $\bigcap$  |      | `\bigwedge` | $\bigwedge$ |      | `\bigotimes` | $\bigotimes$ |
|  `\coprod`  | $\coprod$ |      | `\bigsqcup` | $\bigsqcup$ |      |             |             |      |  `\bigodot`  |  $\bigodot$  |
|   `\int`    |  $\int$   |      |   `\oint`   |   $\oint$   |      |             |             |      | `\biguplus`  | $\biguplus$  |

## 6. 箭头

|      `LaTeX`命令      |         符号         |      |      `LaTeX`命令      |         符号          |      |             `LaTeX`命令             |                符号                 |
| :-------------------: | :------------------: | :--: | :-------------------: | :-------------------: | :--: | :---------------------------------: | :---------------------------------: |
|          `+`          |         $+$          |      |          `-`          |          $-$          |      |                                     |                                     |
| `\leftarrow`, `\gets` |       $\gets$        |      |   `\longleftarrow`    |   $\longleftarrow$    |      |             `\uparrow`              |             $\uparrow$              |
| `\rightarrow`, `\to`  |         $\to$             ||`\longrightarrow`|$\longrightarrow$||`\downarrow`|$\downarrow$|
|   `\leftrightarrow`   |  $\leftrightarrow$   |      | `\longleftrightarrow` | $\longleftrightarrow$ |      |           `\updownarrow`            |           $\updownarrow$            |
|     `\Leftarrow`      |     $\Leftarrow$     |      |   `\Longleftarrow`    |   $\Longleftarrow$    |      |             `\Uparrow`              |             $\Uparrow$              |
|     `\Rightarrow`     |    $\Rightarrow$     |      |   `\Longrightarrow`   |   $\Longrightarrow$   |      |            `\Downarrow`             |            $\Downarrow$             |
|   `\Leftrightarrow`   |  $\Leftrightarrow$   |      | `\Longleftrightarrow` | $\Longleftrightarrow$ |      |           `\Updownarrow`            |           $\Updownarrow$            |
|       `\mapsto`       |      $\mapsto$       |      |     `\longmapsto`     |     $\longmapsto$     |      |             `\nearrow`              |             $\nearrow$              |
|   `\hookleftarrow`    |   $\hookleftarrow$   |      |   `\hookrightarrow`   |   $\hookrightarrow$   |      |             `\searrow`              |             $\searrow$              |
|   `\leftharpoonup`    |   $\leftharpoonup$   |      |   `\rightharpoonup`   |   $\rightharpoonup$   |      |             `\swarrow`              |             $\swarrow$              |
|  `\leftharpoondown`   |  $\leftharpoondown$  |      |  `\rightharpoondown`  |  $\rightharpoondown$  |      |             `\nwarrow`              |             $\nwarrow$              |
| `\rightleftharpoons`  | $\rightleftharpoons$ |      |        `\iff`         |        $\iff$         |      | <font color='red'>`\leadsto`</font> | <font color='red'>$\leadsto$</font> |



## 7. 定界符

|  `LaTeX`命令   |   符号    |      |  `LaTeX`命令   |     符号     |      |  `LaTeX`命令   |      符号      |      |  `LaTeX`命令   |      符号      |
| :------------: | :-------: | :--: | :------------: | :----------: | :--: | :------------: | :------------: | :--: | :------------: | :------------: |
|      `(`       |    $($    |      |      `)`       |     $)$      |      |   `\uparrow`   |   $\uparrow$   |      |   `\Uparrow`   |   $\Uparrow$   |
| `[`, `\lbrack` |    $[$    |      | `]`, `rbrack`  |     $]$      |      |  `\downarrow`  |  $\downarrow$  |      |  `\Downarrow`  |  $\Downarrow$  |
| `{`, `\lbrace` | $\lbrace$ |      | `}`, `\rbrace` |  $\rbrace$   |      | `\updownarrow` | $\updownarrow$ |      | `\Updownarrow` | $\Updownarrow$ |
|   `\langle`    | $\langle$ |      |   `\rangle`    |  $\rangle$   |      |    `\vert`     |    $\vert$     |      |    `\Vert`     |    $\Vert$     |
|   `\lfloor`    | $\lfloor$ |      |   `\rfloor`    |  $\rfloor$   |      |    `\lceil`    |    $\lceil$    |      |    `\rceil`    |    $\rceil$    |
|      `/`       |    $/$    |      |  `\backslash`  | $\backslash$ |      |                |                |      |                |                |

## 8. 大尺度定界符

| `LaTeX`命令  |     符号     |      | `LaTeX`命令  |     符号     |      |  `LaTeX`命令  |     符号      |      |  `LaTeX`命令  |     符号      |
| :----------: | :----------: | :--: | :----------: | :----------: | :--: | :-----------: | :-----------: | :--: | :-----------: | :-----------: |
|  `\lgroup`   |  $\lgroup$   |      |  `\rgroup`   |  $\rgroup$   |      | `\lmoustache` | $\lmoustache$ |      | `\rmoustache` | $\rmoustache$ |
| `\arrowvert` | $\arrowvert$ |      | `\Arrowvert` | $\Arrowvert$ |      | `\bracevert`  | $\bracevert$  |      |               |               |

## 9. 其他符号

|  `LaTeX`命令   |      符号      |      | `LaTeX`命令  |     符号     |      |           `LaTeX`命令           |              符号               |      |             `LaTeX`命令             |                符号                 |
| :------------: | :------------: | :--: | :----------: | :----------: | :--: | :-----------------------------: | :-----------------------------: | :--: | :---------------------------------: | :---------------------------------: |
|    `\dots`     |    $\dots$     |      |   `\cdots`   |   $\cdots$   |      |            `\vdots`             |            $\vdots$             |      |              `\ddots`               |              $\ddots$               |
|    `\hbar`     |    $\hbar$     |      |   `\imath`   |   $\imath$   |      |            `\jmath`             |            $\jmath$             |      |               `\ell`                |               $\ell$                |
|     `\Re`      |     $\Re$      |      |    `\Im`     |    $\Im$     |      |            `\aleph`             |            $\aleph$             |      |                `\wp`                |                $\wp$                |
|   `\forall`    |   $\forall$    |      |  `\exists`   |  $\exists$   |      | <font color='red'>`\mho`</font> | <font color='red'>$\mho$</font> |      |             `\partial`              |             $\partial$              |
|      `‘`       |      $‘$       |      |   `\prime`   |   $\prime$   |      |           `\emptyset`           |           $\emptyset$           |      |              `\infty`               |              $\infty$               |
|    `\nabla`    |    $\nabla$    |      | `\triangle`  | $\triangle$  |      | <font color='red'>`\Box`</font> | <font color='red'>$\Box$</font> |      | <font color='red'>`\Diamond`</font> | <font color='red'>$\Diamond$</font> |
|     `\bot`     |     $\bot$     |      |    `\top`    |    $\top$    |      |            `\angle`             |            $\angle$             |      |               `\surd`               |               $\surd$               |
| `\diamondsuit` | $\diamondsuit$ |      | `\heartsuit` | $\heartsuit$ |      |           `\clubsuit`           |           $\clubsuit$           |      |            `\spadesuit`             |            $\spadesuit$             |
| `\neg`, `lnot` |     $\neg$     |      |   `\flat`    |   $\flat$    |      |           `\natural`            |           $\natural$            |      |              `\sharp`               |              $\sharp$               |

## 10. 数字字母

|         例子          |         命令          |        所需宏包         |
| :-------------------: | :-------------------: | :---------------------: |
|   $\mathrm{ABCdef}$   |   `\mathrm{ABCdef}`   |                         |
|   $\mathit{ABCdef}$   |   `\mathit{ABCdef}`   |                         |
| $\mathnormal{ABCdef}$ | `\mathnormal{ABCdef}` |                         |
|  $\mathcal{ABCdef}$   |  `\mathcal{ABCdef}`   |                         |
|  $\mathscr{ABCdef}$   |  `\mathscr{ABCdef}`   |                         |
|  $\mathfrak{ABCdef}$  |  `\mathfrak{ABCdef}`  |        `eufrak`         |
|    $\mathbb{RZQ}$     |    `\mathbb{RZQ}`     | `amsfonts` or `amssymb` |
|    $\mathbf{RZQ}$     |    `\mathbf{RZQ}`     |                         |

参考：

1. [数学符号表](http://persoweb.whu.edu.cn/mathsymb.pdf)

2. [LATEX Mathematical Symbols](https://www.egr.msu.edu/~renjian/LaTeX-Math-Symbols.pdf)

3. [LaTeX:Symbols](https://artofproblemsolving.com/wiki/index.php/LaTeX:Symbols)

4. [LaTeX:Commands](https://artofproblemsolving.com/wiki/index.php/LaTeX:Commands)

本文源码：

```markdown


两个 LaTeX 工具（其实是一个）：[https://latex.codecogs.com/](https://latex.codecogs.com/) 和 [https://latex.codecogs.com/eqneditor/editor.php](https://latex.codecogs.com/eqneditor/editor.php)



注：红色命令需要导入宏包 `latexsym`

## 1. 数学模式重音符

| `LaTeX`命令 |  **符号**   |      |   `LaTeX`命令   |    **符号**     |
| :---------: | :---------: | :--: | :-------------: | :-------------: |
|  `\hat{a}`  |  $\hat{a}$  |      |  `\widehat{A}`  |  $\widehat{A}$  |
| `\tilde{a}` | $\tilde{a}$ |      | `\widetilde{A}` | $\widetilde{A}$ |
|  `\dot{a}`  |  $\dot{a}$  |      |   `\ddot{a}`    |   $\ddot{a}$    |
|  `\vec{a}`  |  $\vec{a}$  |      |   `\breve{a}`   |   $\breve{a}$   |
|  `\bar{a}`  |  $\bar{a}$  |      |   `\acute{a}`   |   $\acute{a}$   |
| `\check{a}` | $\check{a}$ |      |   `\grave{a}`   |   $\grave{a}$   |

## 2. 希腊字母

| 大写`LaTeX`命令 |      小写`LaTeX`命令      |   大写    |           小写            |      | 大写`LaTeX`命令 |    小写`LaTeX`命令    |    大写    |         小写          |
| :-------------: | :-----------------------: | :-------: | :-----------------------: | :--: | :-------------: | :-------------------: | :--------: | :-------------------: |
|       `A`       |         `\alpha`          |    $A$    |         $\alpha$          |      |       `N`       |         `\nu`         |    $N$     |         $\nu$         |
|       `B`       |          `\beta`          |    $B$    |          $\beta$          |      |      `\Xi`      |         `\xi`         |   $\Xi$    |         $\xi$         |
|    `\Gamma`     |         `\gamma`          | $\Gamma$  |         $\gamma$          |      |       `O`       |          `o`          |    $O$     |          $o$          |
|    `\Delta`     |         `\delta`          | $\Delta$  |         $\delta$          |      |      `\Pi`      |    `\pi`, `\varpi`    |   $\Pi$    |    $\pi$, $\varpi$    |
|       `E`       | `\epsilon`, `\varepsilon` |    $E$    | $\epsilon$, $\varepsilon$ |      |       `P`       |   `\rho`, `\varrho`   |    $P$     |   $\rho$, $\varrho$   |
|       `Z`       |          `\zeta`          |    $Z$    |          $\zeta$          |      |    `\Sigma`     | `\sigma`, `\varsigma` |  $\Sigma$  | $\sigma$, $\varsigma$ |
|       `H`       |          `\eta`           |    $H$    |          $\eta$           |      |       `T`       |        `\tau`         |    $T$     |        $\tau$         |
|    `\Theta`     |   `\theta`, `\vartheta`   | $\Theta$  |   $\theta$, $\vartheta$   |      |   `\Upsilon`    |      `\upsilon`       | $\Upsilon$ |      $\upsilon$       |
|       `I`       |          `\iota`          |    $I$    |          $\iota$          |      |     `\Phi`      |   `\phi`, `\varphi`   |   $\Phi$   |   $\phi$, $\varphi$   |
|       `K`       |         `\kappa`          |    $K$    |         $\kappa$          |      |       `X`       |        `\chi`         |    $X$     |        $\chi$         |
|    `\Lambda`    |         `\lambda`         | $\Lambda$ |         $\lambda$         |      |     `\Psi`      |        `\psi`         |   $\Psi$   |        $\psi$         |
|       `M`       |           `\mu`           |    $M$    |           $\mu$           |      |    `\Omega`     |       `\omega`        |  $\Omega$  |       $\omega$        |

## 3. 二元关系符

|             `LaTeX`命令              |                 符号                 |      |             `LaTeX`命令              |                 符号                 |      |           `LaTeX`命令            |               符号               |
| :----------------------------------: | :----------------------------------: | :--: | :----------------------------------: | :----------------------------------: | :--: | :------------------------------: | :------------------------------: |
|                 `<`                  |                 $<$                  |      |                 `>`                  |                 $>$                  |      |               `=`                |               $=$                |
|            `\leq`, `\le`             |                $\le$                 |      |            `\geq`, `\ge`             |                $\geq$                |      |             `\equiv`             |             $\equiv$             |
|                `\ll`                 |                $\ll$                 |      |                `\gg`                 |                $\gg$                 |      |             `\doteq`             |             $\doteq$             |
|               `\prec`                |               $\prec$                |      |               `\succ`                |               $\succ$                |      |              `\sim`              |              $\sim$              |
|              `\preceq`               |              $\preceq$               |      |              `\succeq`               |              $\succeq$               |      |             `\simeq`             |             $\simeq$             |
|              `\subset`               |              $\subset$               |      |              `\supset`               |              $\supset$               |      |            `\approx`             |            $\approx$             |
|             `\subseteq`              |             $\subseteq$              |      |             `\supseteq`              |             $\supseteq$              |      |             `\cong`              |             $\cong$              |
| <font color='red'>`\sqsubset`</font> | <font color='red'>$\sqsubset$</font> |      | <font color='red'>`\sqsupset`</font> | <font color='red'>$\sqsupset$</font> |      | <font color='red'>`\Join`</font> | <font color='red'>$\Join$</font> |
|            `\sqsubseteq`             |            $\sqsubseteq$             |      |            `\sqsupseteq`             |            $\sqsupseteq$             |      |            `\bowtie`             |            $\bowtie$             |
|                `\in`                 |                $\in$                 |      |            `\ni`, `\owns`            |                $\ni$                 |      |            `\propto`             |            $\propto$             |
|               `\vdash`               |               $\vdash$               |      |               `\dashv`               |               $\dashv$               |      |            `\models`             |            $\models$             |
|                `\mid`                |                $\mid$                |      |             `\parallel`              |             $\parallel$              |      |             `\perp`              |             $\perp$              |
|               `\smile`               |               $\smile$               |      |               `\frown`               |               $\frown$               |      |             `\asymp`             |             $\asymp$             |
|                 `:`                  |                 $:$                  |      |               `\notin`               |               $\notin$               |      |          `\neq`, `\ne`           |              $\neq$              |

## 4. 二元运算符

|            `LaTeX`命令            |               符号                |      |            `LaTeX`命令            |               符号                |      |   `LaTeX`命令    |       符号       |
| :-------------------------------: | :-------------------------------: | :--: | :-------------------------------: | :-------------------------------: | :--: | :--------------: | :--------------: |
|               `\pm`               |               $\pm$               |      |               `\mp`               |               $\mp$               |      | `\triangleleft`  | $\triangleleft$  |
|              `\cdot`              |              $\cdot$              |      |              `\div`               |              $\div$               |      | `\triangleright` | $\triangleright$ |
|             `\times`              |             $\times$              |      |            `\setminus`            |            $\setminus$            |      |     `\star`      |     $\star$      |
|              `\cup`               |              $\cup$               |      |              `\cap`               |              $\cap$               |      |      `\ast`      |      $\ast$      |
|             `\sqcup`              |             $\sqcup$              |      |             `\sqcap`              |             $\sqcap$              |      |     `\circ`      |     $\circ$      |
|          `\vee`, `\lor`           |              $\vee$               |      |         `\wedge`, `land`          |              $\land$              |      |    `\bullet`     |    $\bullet$     |
|             `\oplus`              |             $\oplus$              |      |             `\ominus`             |             $\ominus$             |      |    `\diamond`    |    $\diamond$    |
|              `\odot`              |              $\odot$              |      |             `\oslash`             |             $\oslash$             |      |     `\uplus`     |     $\uplus$     |
|            `\otimes`     |     $\otimes$     ||`\bigcirc`|$\bigcirc$||`\amalg`|$\amalg$|
|         `\bigtriangleup`          |         $\bigtriangleup$          |      |        `\bigtriangledown`         |        $\bigtriangledown$         |      |    `\dagger`     |    $\dagger$     |
|  <font color='red'>`\lhd`</font>  |  <font color='red'>$\lhd$</font>  |      |  <font color='red'>`\rhd`</font>  |  <font color='red'>$\rhd$</font>  |      |    `\ddagger`    |    $\ddagger$    |
| <font color='red'>`\unlhd`</font> | <font color='red'>$\unlhd$</font> |      | <font color='red'>`\unrhd`</font> | <font color='red'>$\unrhd$</font> |      |      `\wr`       |      $\wr$       |

## 5. 大尺度运算符

| `LaTeX`命令 |   符号    |      | `LaTeX`命令 |    符号     |      | `LaTeX`命令 |    符号     |      | `LaTeX`命令  |     符号     |
| :---------: | :-------: | :--: | :---------: | :---------: | :--: | :---------: | :---------: | :--: | :----------: | :----------: |
|   `\sum`    |  $\sum$   |      |  `\bigcup`  |  $\bigcup$  |      |  `\bigvee`  |  $\bigvee$  |      | `\bigoplus`  | $\bigoplus$  |
|   `\prod`   |  $\prod$  |      |  `\bigcap`  |  $\bigcap$  |      | `\bigwedge` | $\bigwedge$ |      | `\bigotimes` | $\bigotimes$ |
|  `\coprod`  | $\coprod$ |      | `\bigsqcup` | $\bigsqcup$ |      |             |             |      |  `\bigodot`  |  $\bigodot$  |
|   `\int`    |  $\int$   |      |   `\oint`   |   $\oint$   |      |             |             |      | `\biguplus`  | $\biguplus$  |

## 6. 箭头

|      `LaTeX`命令      |         符号         |      |      `LaTeX`命令      |         符号          |      |             `LaTeX`命令             |                符号                 |
| :-------------------: | :------------------: | :--: | :-------------------: | :-------------------: | :--: | :---------------------------------: | :---------------------------------: |
|          `+`          |         $+$          |      |          `-`          |          $-$          |      |                                     |                                     |
| `\leftarrow`, `\gets` |       $\gets$        |      |   `\longleftarrow`    |   $\longleftarrow$    |      |             `\uparrow`              |             $\uparrow$              |
| `\rightarrow`, `\to`  |         $\to$             ||`\longrightarrow`|$\longrightarrow$||`\downarrow`|$\downarrow$|
|   `\leftrightarrow`   |  $\leftrightarrow$   |      | `\longleftrightarrow` | $\longleftrightarrow$ |      |           `\updownarrow`            |           $\updownarrow$            |
|     `\Leftarrow`      |     $\Leftarrow$     |      |   `\Longleftarrow`    |   $\Longleftarrow$    |      |             `\Uparrow`              |             $\Uparrow$              |
|     `\Rightarrow`     |    $\Rightarrow$     |      |   `\Longrightarrow`   |   $\Longrightarrow$   |      |            `\Downarrow`             |            $\Downarrow$             |
|   `\Leftrightarrow`   |  $\Leftrightarrow$   |      | `\Longleftrightarrow` | $\Longleftrightarrow$ |      |           `\Updownarrow`            |           $\Updownarrow$            |
|       `\mapsto`       |      $\mapsto$       |      |     `\longmapsto`     |     $\longmapsto$     |      |             `\nearrow`              |             $\nearrow$              |
|   `\hookleftarrow`    |   $\hookleftarrow$   |      |   `\hookrightarrow`   |   $\hookrightarrow$   |      |             `\searrow`              |             $\searrow$              |
|   `\leftharpoonup`    |   $\leftharpoonup$   |      |   `\rightharpoonup`   |   $\rightharpoonup$   |      |             `\swarrow`              |             $\swarrow$              |
|  `\leftharpoondown`   |  $\leftharpoondown$  |      |  `\rightharpoondown`  |  $\rightharpoondown$  |      |             `\nwarrow`              |             $\nwarrow$              |
| `\rightleftharpoons`  | $\rightleftharpoons$ |      |        `\iff`         |        $\iff$         |      | <font color='red'>`\leadsto`</font> | <font color='red'>$\leadsto$</font> |



## 7. 定界符

|  `LaTeX`命令   |   符号    |      |  `LaTeX`命令   |     符号     |      |  `LaTeX`命令   |      符号      |      |  `LaTeX`命令   |      符号      |
| :------------: | :-------: | :--: | :------------: | :----------: | :--: | :------------: | :------------: | :--: | :------------: | :------------: |
|      `(`       |    $($    |      |      `)`       |     $)$      |      |   `\uparrow`   |   $\uparrow$   |      |   `\Uparrow`   |   $\Uparrow$   |
| `[`, `\lbrack` |    $[$    |      | `]`, `rbrack`  |     $]$      |      |  `\downarrow`  |  $\downarrow$  |      |  `\Downarrow`  |  $\Downarrow$  |
| `{`, `\lbrace` | $\lbrace$ |      | `}`, `\rbrace` |  $\rbrace$   |      | `\updownarrow` | $\updownarrow$ |      | `\Updownarrow` | $\Updownarrow$ |
|   `\langle`    | $\langle$ |      |   `\rangle`    |  $\rangle$   |      |    `\vert`     |    $\vert$     |      |    `\Vert`     |    $\Vert$     |
|   `\lfloor`    | $\lfloor$ |      |   `\rfloor`    |  $\rfloor$   |      |    `\lceil`    |    $\lceil$    |      |    `\rceil`    |    $\rceil$    |
|      `/`       |    $/$    |      |  `\backslash`  | $\backslash$ |      |                |                |      |                |                |

## 8. 大尺度定界符

| `LaTeX`命令  |     符号     |      | `LaTeX`命令  |     符号     |      |  `LaTeX`命令  |     符号      |      |  `LaTeX`命令  |     符号      |
| :----------: | :----------: | :--: | :----------: | :----------: | :--: | :-----------: | :-----------: | :--: | :-----------: | :-----------: |
|  `\lgroup`   |  $\lgroup$   |      |  `\rgroup`   |  $\rgroup$   |      | `\lmoustache` | $\lmoustache$ |      | `\rmoustache` | $\rmoustache$ |
| `\arrowvert` | $\arrowvert$ |      | `\Arrowvert` | $\Arrowvert$ |      | `\bracevert`  | $\bracevert$  |      |               |               |

## 9. 其他符号

|  `LaTeX`命令   |      符号      |      | `LaTeX`命令  |     符号     |      |           `LaTeX`命令           |              符号               |      |             `LaTeX`命令             |                符号                 |
| :------------: | :------------: | :--: | :----------: | :----------: | :--: | :-----------------------------: | :-----------------------------: | :--: | :---------------------------------: | :---------------------------------: |
|    `\dots`     |    $\dots$     |      |   `\cdots`   |   $\cdots$   |      |            `\vdots`             |            $\vdots$             |      |              `\ddots`               |              $\ddots$               |
|    `\hbar`     |    $\hbar$     |      |   `\imath`   |   $\imath$   |      |            `\jmath`             |            $\jmath$             |      |               `\ell`                |               $\ell$                |
|     `\Re`      |     $\Re$      |      |    `\Im`     |    $\Im$     |      |            `\aleph`             |            $\aleph$             |      |                `\wp`                |                $\wp$                |
|   `\forall`    |   $\forall$    |      |  `\exists`   |  $\exists$   |      | <font color='red'>`\mho`</font> | <font color='red'>$\mho$</font> |      |             `\partial`              |             $\partial$              |
|      `‘`       |      $‘$       |      |   `\prime`   |   $\prime$   |      |           `\emptyset`           |           $\emptyset$           |      |              `\infty`               |              $\infty$               |
|    `\nabla`    |    $\nabla$    |      | `\triangle`  | $\triangle$  |      | <font color='red'>`\Box`</font> | <font color='red'>$\Box$</font> |      | <font color='red'>`\Diamond`</font> | <font color='red'>$\Diamond$</font> |
|     `\bot`     |     $\bot$     |      |    `\top`    |    $\top$    |      |            `\angle`             |            $\angle$             |      |               `\surd`               |               $\surd$               |
| `\diamondsuit` | $\diamondsuit$ |      | `\heartsuit` | $\heartsuit$ |      |           `\clubsuit`           |           $\clubsuit$           |      |            `\spadesuit`             |            $\spadesuit$             |
| `\neg`, `lnot` |     $\neg$     |      |   `\flat`    |   $\flat$    |      |           `\natural`            |           $\natural$            |      |              `\sharp`               |              $\sharp$               |

## 10. 数字字母

|         例子          |         命令          |        所需宏包         |
| :-------------------: | :-------------------: | :---------------------: |
|   $\mathrm{ABCdef}$   |   `\mathrm{ABCdef}`   |                         |
|   $\mathit{ABCdef}$   |   `\mathit{ABCdef}`   |                         |
| $\mathnormal{ABCdef}$ | `\mathnormal{ABCdef}` |                         |
|  $\mathcal{ABCdef}$   |  `\mathcal{ABCdef}`   |                         |
|  $\mathscr{ABCdef}$   |  `\mathscr{ABCdef}`   |                         |
|  $\mathfrak{ABCdef}$  |  `\mathfrak{ABCdef}`  |        `eufrak`         |
|    $\mathbb{RZQ}$     |    `\mathbb{RZQ}`     | `amsfonts` or `amssymb` |
|    $\mathbf{RZQ}$     |    `\mathbf{RZQ}`     |                         |

参考：

1. [数学符号表](http://persoweb.whu.edu.cn/mathsymb.pdf)

2. [LATEX Mathematical Symbols](https://www.egr.msu.edu/~renjian/LaTeX-Math-Symbols.pdf)

3. [LaTeX:Symbols](https://artofproblemsolving.com/wiki/index.php/LaTeX:Symbols)

4. [LaTeX:Commands](https://artofproblemsolving.com/wiki/index.php/LaTeX:Commands)

```

