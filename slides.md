---
title: ggplot2 v4.0 に備えよう
format:
  gfm:
    variant: +yaml_metadata_block
theme: default
background: /background.png
fonts:
  sans: Noto Sans JP
  mono: Fira Code
  weights: 600,900
class: text-center
drawings:
  persist: false
mdc: true
---


# ggplot2<br/>v4.0<br/>に備えよう

2025/06/21 Tokyo.R#118

@yutannihilation

---
layout: image-right
image: "/icon.jpg"
---

# ドーモ！

## 名前:

湯谷啓明

- MIERUNE で GIS エンジニア見習い
- ggplot2 のメンテナ（いちおう）

------------------------------------------------------------------------

# 宣伝：うちのCTOの本が今日発売

<Tweet id="1928764558921105630" scale=1.5 />

---
layout: two-cols
---

# ggplot2

- グラフを描画するためのフレームワーク
- 一貫性のある文法で、細かい調整がしやすい

::right::

![](https://ggplot2.tidyverse.org/logo.png)

------------------------------------------------------------------------

# ggplot2 v4.0

<v-clicks>

- たぶん来月あたりリリース
- 100年に1度の出来と言われた2024年を超す21世紀最高の出来栄え（ボジョレーヌーボー風）
- `theme()` まわりが大幅強化

</v-clicks>

------------------------------------------------------------------------

# 今日話すこと

- `theme(geom = )`
- `ink` と `paper`
- `theme(palette = )`

------------------------------------------------------------------------

# これまで

- だいたいのスタイルは `theme()` で設定できる
- と言いつつ「ただし、」が多かった

------------------------------------------------------------------------

# 「ただし」の例

- `colour` や `fill` の scale は `options()`

  ``` r
  options(
    ggplot2.continuous.colour = "viridis",
    ggplot2.continuous.fill = "viridis"
  )
  ```

- `Geom` の点や線の大きさ、色などは `update_geom_defaults()`

  ``` r
  update_geom_defaults("point",
    aes(color = "red")
  )
  ```

------------------------------------------------------------------------

# 「ただし」の例

- `theme(text = )` の指定は `geom_text()` などには引き継がれない
- `theme(text = )` のサイズは `geom_text()` のサイズ指定と単位が違う

------------------------------------------------------------------------

# `theme(geom = )`

- `element_geom()` でデフォルトのスタイルを指定できるようになった

``` r
p <- ggplot(mpg, aes(displ, hwy)) + geom_point()

p2 <- p +
  theme(
    geom = element_geom(
      pointshape = "square",
      pointsize = 8
    )
  )
```

------------------------------------------------------------------------

![](./plot/unnamed-chunk-2-1.png)

------------------------------------------------------------------------

# `element_geom(fontsize = )`

- サイズの単位がポイントなので、サイズを揃えるのが簡単になった。

``` r
ggplot(d, aes(Foo, Bar, label = label)) +
  geom_text() +
  labs(title = "Foo") +
  theme(
    plot.title = element_text(size = 30),
    axis.title = element_text(size = 30),
    axis.text = element_text(size = 30),
    geom = element_geom(fontsize = 30)
  )
```

------------------------------------------------------------------------

![](./plot/unnamed-chunk-5-1.png)

------------------------------------------------------------------------

# `ink` と `paper`

- `ink`: メインの色
- `paper`: 背景の色
- `accent`: 強調の色

------------------------------------------------------------------------

# （元のプロット）

``` r
ggplot(mpg, aes(class)) + geom_bar()
```

<img src="./plot/unnamed-chunk-6-1.png" style="height:80.0%" />

------------------------------------------------------------------------

# `ink` が青

``` r
p + theme_gray(ink = "blue")
```

<img src="./plot/unnamed-chunk-8-1.png" style="height:80.0%" />

------------------------------------------------------------------------

# `paper` が赤

``` r
p + theme_gray(paper = "red")
```

<img src="./plot/unnamed-chunk-9-1.png" style="height:80.0%" />

------------------------------------------------------------------------

# `ink` が青で `paper` が赤

``` r
p + theme_gray(ink = "blue", paper = "red")
```

<img src="./plot/unnamed-chunk-10-1.png" style="height:80.0%" />

------------------------------------------------------------------------

# `ink` と `paper`

- `ink` はまあわかる
- `paper` が入ってくると難しい…
- `accent` はもう謎

------------------------------------------------------------------------

# `from_theme()`

- なぜこの指定が動くのかというと `from_theme()` が default aes
  に設定されているから

``` r
GeomPoint$default_aes
#> Aesthetic mapping: 
#> * `shape`  -> `from_theme(pointshape)`
#> * `colour` -> `from_theme(colour %||% ink)`
#> * `fill`   -> `from_theme(fill %||% NA)`
#> * `size`   -> `from_theme(pointsize)`
#> * `alpha`  -> NA
#> * `stroke` -> `from_theme(borderwidth)`
```

------------------------------------------------------------------------

# `from_theme()`

- つまり、Geom
  側の実装に寄るので、拡張パッケージ側の対応はまだこれから。

------------------------------------------------------------------------

# `theme(palette = )`

- `scales_colour_*()` とか `scales_fill_*()` を使わなくても、`theme()`
  からカラーパレットを指定できるようになった！
- scales パッケージのパレットが指定できる

------------------------------------------------------------------------

# パレット名を指定

``` r
p +
  theme(
    palette.colour.discrete = "mint"
  )
```

------------------------------------------------------------------------

![](./plot/unnamed-chunk-14-1.png)

------------------------------------------------------------------------

# `scales::pal_*()` を指定

``` r
pal <- pal_viridis(option = "F")

p +
  theme(
    palette.colour.discrete = pal
  )
```

------------------------------------------------------------------------

![](./plot/unnamed-chunk-16-1.png)

------------------------------------------------------------------------

# 自作のパレットを指定

- 必要なのは discrete なパレットだが、continuous
  にしておくと補間してくれる

``` r
my_pal <- as_continuous_pal(
  c("red", "black", "yellow")
)

p +
  theme(
    palette.colour.discrete = my_pal
  )
```

------------------------------------------------------------------------

![](./plot/unnamed-chunk-18-1.png)

------------------------------------------------------------------------

# 補足

- palette の管理は scales v1.4.0 の新機能
- palette 一覧は、`scales:::palette_names()`
  で確認できる（他にいい方法ある…？）

------------------------------------------------------------------------

``` r
scales:::palette_names()
#>   [1] "greens 2"        "r4"              "greens 3"        "blues"          
#>   [5] "terrain"         "tableau 10"      "terrain 2"       "purple-orange"  
#>   [9] "spectral"        "ag_sunset"       "sunset"          "ylgn"           
#>  [13] "orrd"            "purple-blue"     "rdylbu"          "paired"         
#>  [17] "teal"            "gnbu"            "inferno"         "puor"           
#>  [21] "grey"            "armyrose"        "purpor"          "purple-brown"   
#>  [25] "cividis"         "tofino"          "tropic"          "oranges"        
#>  [29] "bugn"            "green-brown"     "warm"            "plasma"         
#>  [33] "harmonic"        "ag_grnyl"        "polychrome 36"   "reds 2"         
#>  [37] "rdbu"            "reds 3"          "green-orange"    "purples 2"      
#>  [41] "blue-red 2"      "blues 2"         "lisbon"          "accent"         
#>  [45] "purples 3"       "blue-red 3"      "blues 3"         "purple-yellow"  
#>  [49] "blue-red"        "turbo"           "piyg"            "viridis"        
#>  [53] "tealrose"        "bluyl"           "bupu"            "classic tableau"
#>  [57] "blue-yellow"     "broc"            "oslo"            "heat"           
#>  [61] "set1"            "set2"            "purd"            "hue"            
#>  [65] "set3"            "temps"           "reds"            "dark2"          
#>  [69] "brwnyl"          "mint"            "burgyl"          "blue-yellow 2"  
#>  [73] "prgn"            "blue-yellow 3"   "turku"           "roma"           
#>  [77] "burg"            "purp"            "sunsetdark"      "batlow"         
#>  [81] "heat 2"          "hawaii"          "red-purple"      "tealgrn"        
#>  [85] "peach"           "pastel 1"        "magenta"         "pastel 2"       
#>  [89] "lajolla"         "pastel1"         "pastel2"         "greys"          
#>  [93] "okabe-ito"       "pubu"            "cork"            "pinkyl"         
#>  [97] "blugrn"          "ylorrd"          "rdylgn"          "magma"          
#> [101] "set 1"           "set 2"           "ylgnbu"          "set 3"          
#> [105] "pubugn"          "red-green"       "purple-green"    "greens"         
#> [109] "mako"            "alphabet"        "geyser"          "dark mint"      
#> [113] "vik"             "cyan-magenta"    "emrld"           "red-yellow"     
#> [117] "ylorbr"          "brbg"            "cold"            "purples"        
#> [121] "fall"            "red-blue"        "ggplot2"         "berlin"         
#> [125] "rocket"          "rdgy"            "dark 2"          "dark 3"         
#> [129] "dynamic"         "green-yellow"    "redor"           "rdpu"           
#> [133] "grays"           "light grays"     "earth"           "oryel"          
#> [137] "r3"              "zissou 1"
```

------------------------------------------------------------------------

# まとめ

- `theme()` まわりが大幅強化されるので楽しみ
- いっぱい変更があるので、リリースからしばらくはトラブルあるかも
