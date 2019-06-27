+++ 
draft = true
date = 2019-06-27T11:15:13+08:00
title = "Paper node: node2vec: Scalable Feature Learning for Networks"
description = ""
slug = "" 
tags = ['paper-note', 'graph']
categories = ['paper-note']
externalLink = ""
series = []
+++

# node2vec: Scalable Feature Learning for Networks
* Published in KDD'16
* Link: https://cs.stanford.edu/~jure/pubs/node2vec-kdd16.pdf 

## 作者
> Aditya Grover
> Jure Leskovec

## 研究目的
* Prior researches can not capture the diversity of connectivity patterns observed in networks.
* Prior researches fail to offer flexibility in sampling of nodes from a network.
* Learn the representation locally and globally at the same time.

## 研究方法
### 資料結構
### 演算法
* 1)將 nodes 之間的關係視作為文本的句子 且 2)將 feature learning 視為是 maximum likelihood optimization 的問題
* Objective function is extended by the Skip-gram approach: `$$\max\limits_{f}\sum_{u \in V}\log Pr(N_S(u)|f(u))$$` where `$N_s(u)$` is the neighbor for node `$u$` generated through a neighborhood sampling staregy `$S$`, `$f$` is the feature representation.
    * 先設定了兩個假設:
        * Conditional independence: 計算單一 node 的 neighborhood likelihood 是獨立於其他點的 `$$Pr(N_S(u)|f(u)) = \prod_{n_i \in N_S(u)} Pr(n_i|f(u))$$`
        * Symmetry in feature space: source node and neighborhood node have a symmetric effect over each other (對稱性的相互影響) in feature space. -> use a softmax unit: `$$Pr(n_i|f(u)) = \frac{\exp{(f(n_i)\cdot f(u))}}{\sum_{v \in V}\exp{(f(v)\cdot f(u))}}$$`
    * 最後的 objective function: `$$\max\limits_{f} \sum_{u \in V}\bigg[-\log Z_u + \sum_{n_i \in N_S(u)}f(n_i) \cdot f(u)\bigg]$$`

* 本文主要觀念：將 BFS, DFS, RandomWalk 進行了一定程度的融合，藉以抽取圖結構的內容相似性 (homophily) 以及結構相似性 (structural) 特徵
    * ![](https://i.imgur.com/CkSXARs.png)
    * Homophily: node 之間是高度連結的，且構成了同一個 network cluster, looking for macro-view (容易找到週遭的點集)
    * Structual: 有相似網絡結構 (structural role) 的 node 會有相似的 embedding, looking for micro-view (容易到遠處找點集)

* 透過 Biased random walk 來擬和 BFS, DFS 的特性，首先設定每個 walk `$c_i$` 長度為 `$l$`,  `$$P(c_i = x|c_{i-1} = v)= \begin{cases} \frac{\pi_{vx}}{Z},& \text{if } (v, x) \in E\\ 0,              & \text{otherwise} \end{cases}$$` where `$\pi_{vx}$` is the unnormalized transition probability between nodes `$v$` and `$x$`, and `$Z$` is the normalizing constant.

    * 再來利用二階隨機遊走 (second-order random walk)，此遊走是 based on two params `$p$` and `$q$`.
        * p: 控制 revisit 的情形
        * q: 用來區分 "inward", "outward" 的 nodes
    * walk traverse 到 node `$v$` 時，transition probabilities on edges (v, x): `$\pi_{vx} = \alpha_{pq}(t, x) \cdot w_{vx}$`，其中 `$$\alpha_{pq}(t, x) = \begin{cases} \frac{1}{p} & \text{if } d_{tx} = 0\\ 1,          & \text{if } d_{tx} = 1\\ \frac{1}{q} & \text{if } d_{tx} = 2\\ \end{cases}$$`
    * ![](https://i.imgur.com/6JGTudP.png) 
        * walk 剛從 `$t$` 走到 `$v$`，此時有三個選擇:
            1. 回 `$t$`
            2. 到離 `$t$` 距離 1的點 (`$x_1$`)
            3. 到離 `$t$` 距離 2 的點 (`$x_2, x_3$`)

### 評估方式
* Multi-label classification
* Link prediction

## 研究貢獻
* node2vec - a semi-supervised algorithm
* biased second-order random walk
    * Maximize the likelihood of preserving network neighborhoods of nodes in a `$d$`-dimensional feature space. (本文中使用 second-order RandomWalk 來產生網路鄰節點)
    * Not tied to a particular sampling startegy and provides parameters to tune the explored search space (跟 DeepWalk, LINE 相比)

## 參考
* https://zhuanlan.zhihu.com/p/39105752