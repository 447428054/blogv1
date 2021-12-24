---
layout:     post
title:      Attention可视化
subtitle:   Attention可视化
date:       2021-12-24
author:     JMX
header-img: img/post_bd_04.jpg
catalog: true
tags:
    - Attention
---


# 热力图可视化

> 使用matplotlib与seaborn完成attention矩阵的热力图绘制

```
# -*- coding:utf-8 -*-
# author:   LZ
# create time:  2021/11/15 14:54
# file: genpic.py
# IDE:  PyCharm

import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
import matplotlib.ticker as ticker
import seaborn as sns
import matplotlib


gperes = {
     '武': {'武': 0.1, '汉': 0.9, '市': 1, '长': 0.05, '江': 0.09, '大': 0.03, '桥': 0.2},
     '汉': {'武': 0, '汉': 0.2, '市': 0.1, '长': 0.03, '江': 0.04, '大': 0.02, '桥': 0.07},
     '市': {'武': 0, '汉': 0, '市': 0.1, '长': 0.01, '江': 0.02, '大': 0.03, '桥': 0.05},
     '长': {'武': 0, '汉': 0, '市': 0, '长': 0.25, '江': 0.1, '大': 0.08, '桥': 0.09},
     '江': {'武': 0, '汉': 0, '市': 0, '长': 0, '江': 0.15, '大': 0.04, '桥': 0.015},
     '大': {'武': 0, '汉': 0, '市': 0, '长': 0, '江': 0, '大': 0.14, '桥': 0.15},
     '桥': {'武': 0, '汉': 0, '市': 0, '长': 0, '江': 0, '大': 0, '桥': 0.12},
}

newgperes = {}
for k, v in gperes.items():
     for w, a in v.items():
          newgperes.setdefault(w, {})
          newgperes[w][k] = a


plt.rcParams['font.sans-serif']=['Arial Unicode MS']
plt.rcParams['axes.unicode_minus'] = False
gpedata = pd.DataFrame(newgperes)
ax = sns.heatmap(gpedata, cmap="YlOrRd")
ax.xaxis.tick_top()
ax.set_yticklabels(ax.get_yticklabels(), rotation=0)
plt.savefig('loctest.png')
plt.show()

```

![gpe.png](https://note.youdao.com/yws/res/4219/WEBRESOURCE634cbdbbd647eadaead0b49b3da4b742)


# NLP文本注意力可视化

> 根据注意力矩阵，得到html颜色深浅代表相关性强弱


```
# Credits to Lin Zhouhan(@hantek) for the complete visualization code
import random, os, numpy, scipy
from codecs import open


def createHTML(texts, weights, savepath):
    """
    Creates a html file with text heat.
	weights: attention weights for visualizing
	texts: text on which attention weights are to be visualized
    """
    fOut = open(savepath, "w", encoding="utf-8")
    part1 = """
    <html lang="en">
    <head>
    <meta http-equiv="content-type" content="text/html; charset=utf-8">
    <style>
    body {
    font-family: Sans-Serif;
    }
    </style>
    </head>
    <body>
    <h3>
    Heatmaps
    </h3>
    </body>
    <script>
    """
    part2 = """
    var color = "255,0,0";
    var ngram_length = 3;
    var half_ngram = 1;
    for (var k=0; k < any_text.length; k++) {
    var tokens = any_text[k].split(" ");
    var intensity = new Array(tokens.length);
    var max_intensity = Number.MIN_SAFE_INTEGER;
    var min_intensity = Number.MAX_SAFE_INTEGER;
    for (var i = 0; i < intensity.length; i++) {
    intensity[i] = 0.0;
    for (var j = -half_ngram; j < ngram_length-half_ngram; j++) {
    if (i+j < intensity.length && i+j > -1) {
    intensity[i] += trigram_weights[k][i + j];
    }
    }
    if (i == 0 || i == intensity.length-1) {
    intensity[i] /= 2.0;
    } else {
    intensity[i] /= 3.0;
    }
    if (intensity[i] > max_intensity) {
    max_intensity = intensity[i];
    }
    if (intensity[i] < min_intensity) {
    min_intensity = intensity[i];
    }
    }
    var denominator = max_intensity - min_intensity;
    for (var i = 0; i < intensity.length; i++) {
    intensity[i] = (intensity[i] - min_intensity) / denominator;
    }
    if (k%2 == 0) {
    var heat_text = "<p><br><b>Example:</b><br>";
    } else {
    var heat_text = "<b>Example:</b><br>";
    }
    var space = "";
    for (var i = 0; i < tokens.length; i++) {
    heat_text += "<span style='background-color:rgba(" + color + "," + intensity[i] + ")'>" + space + tokens[i] + "</span>";
    if (space == "") {
    space = " ";
    }
    }
    //heat_text += "<p>";
    document.body.innerHTML += heat_text;
    }
    </script>
    </html>"""
    putQuote = lambda x: "\"%s\"" % x
    textsString = "var any_text = [%s];\n" % (",".join(map(putQuote, texts)))
    weightsString = "var trigram_weights = [%s];\n" % (",".join(map(str, weights)))
    fOut.write(part1)
    fOut.write(textsString)
    fOut.write(weightsString)
    fOut.write(part2)
    fOut.close()

    return

gperes = {
     '武': {'武': 0.1, '汉': 0.9, '市': 1, '长': 0.05, '江': 0.09, '大': 0.03, '桥': 0.2},
     '汉': {'武': 0, '汉': 0.2, '市': 0.1, '长': 0.03, '江': 0.04, '大': 0.02, '桥': 0.07},
     '市': {'武': 0, '汉': 0, '市': 0.1, '长': 0.01, '江': 0.02, '大': 0.03, '桥': 0.05},
     '长': {'武': 0, '汉': 0, '市': 0, '长': 0.25, '江': 0.1, '大': 0.08, '桥': 0.09},
     '江': {'武': 0, '汉': 0, '市': 0, '长': 0, '江': 0.15, '大': 0.04, '桥': 0.015},
     '大': {'武': 0, '汉': 0, '市': 0, '长': 0, '江': 0, '大': 0.14, '桥': 0.15},
     '桥': {'武': 0, '汉': 0, '市': 0, '长': 0, '江': 0, '大': 0, '桥': 0.12},
}

matrix = []
# 获取矩阵表示
for k, v in gperes.items():
    matrix.append(list(v.values()))
str_ = '武 汉 市 长 江 大 桥'
createHTML([str_] * 7,
           matrix,
           './visualization/test.html')
```

![wx20211211-193035@2x.png](https://note.youdao.com/yws/res/4222/WEBRESOURCEfe795460490b96a91dcf376681fb9283)

# 参考链接
https://blog.csdn.net/qq_38607066/article/details/101345282#t17

https://blog.csdn.net/u010490755/article/details/89574847