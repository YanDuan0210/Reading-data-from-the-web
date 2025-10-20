Reading data from the web
================
Yan Duan
2025-10-20

## R Markdown

#### Scrape a table

I want the first table from \[this page\] Reading in the html.

``` r
url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

drug_use_html = read_html(url)
```

extract the table(s)提取表格; focus on the first one rvest
的选择器函数，用 CSS 选择器 “table” 选出文档里所有
<table>

标签，返回一个 xml_nodeset（节点集合）

``` r
table_marj = 
  drug_use_html |>
  rvest::html_element("table") |> 
  html_table() |> 
  slice(-1) |> 
  as_tibble()
```

## Read books

I want the data from here

``` r
url = "http://books.toscrape.com"

books_html = read_html(url)
```

``` r
books_titles = 
  books_html |>
  html_elements("h3") |>
  html_text2()

books_stars = 
  books_html |>
  html_elements(".star-rating") |>
  html_attr("class")

books_price = 
  books_html |>
  html_elements(".price_color") |>
  html_text()

books = tibble(
  title = books_titles,
  stars = books_stars,
  price = books_price
)
```

#### API

``` r
nyc_water = 
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.csv") |> 
  content("parsed")  #解析 response 正文内容
```

    ## Rows: 65 Columns: 4
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl (4): year, new_york_city_population, nyc_consumption_million_gallons_per...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

将此数据集导入为 JSON 文件

``` r
nyc_water = 
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.json") |> 
  content("text") |>
  jsonlite::fromJSON() |>
  as_tibble()
```

#### pokemon API

R 调用网络 API 并解析 JSON 数据 **`poke`** 提取

``` r
poke = 
  GET("http://pokeapi.co/api/v2/pokemon/1") |>
  content()

poke[["name"]]
```

    ## [1] "bulbasaur"

``` r
poke[["height"]]
```

    ## [1] 7

``` r
poke[["abilities"]]
```

    ## [[1]]
    ## [[1]]$ability
    ## [[1]]$ability$name
    ## [1] "overgrow"
    ## 
    ## [[1]]$ability$url
    ## [1] "https://pokeapi.co/api/v2/ability/65/"
    ## 
    ## 
    ## [[1]]$is_hidden
    ## [1] FALSE
    ## 
    ## [[1]]$slot
    ## [1] 1
    ## 
    ## 
    ## [[2]]
    ## [[2]]$ability
    ## [[2]]$ability$name
    ## [1] "chlorophyll"
    ## 
    ## [[2]]$ability$url
    ## [1] "https://pokeapi.co/api/v2/ability/34/"
    ## 
    ## 
    ## [[2]]$is_hidden
    ## [1] TRUE
    ## 
    ## [[2]]$slot
    ## [1] 3
