---
title: LaTeX数学符号
author: hhhhmdzz
language: zh-Hans
timezone: Asia/Shanghai
top: true
cover: false
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



## 5. 大尺度运算符



## 6. 箭头



## 7. 定界符



## 8. 大尺度定界符



## 9. 其他符号



## 10. 数字字母



