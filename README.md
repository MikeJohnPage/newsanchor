
<!-- README.md is generated from README.Rmd. Please edit that file -->

# newsanchor <img src="newsanchor.png" width="160px" align="right" />

[![Project Status: Active – The project has reached a stable, usable
state and is being actively
developed.](http://www.repostatus.org/badges/latest/active.svg)](http://www.repostatus.org/#active)
[![Build
Status](https://travis-ci.org/CorrelAid/newsanchor.svg?branch=master)](https://travis-ci.org/CorrelAid/newsanchor)
[![codecov](https://codecov.io/gh/CorrelAid/newsanchor/branch/master/graph/badge.svg)](https://codecov.io/gh/CorrelAid/newsanchor)
[![CRAN\_Status\_Badge](http://www.r-pkg.org/badges/version/newsanchor)](https://cran.r-project.org/package=newsanchor)
[![CRAN\_Download\_Badge](https://cranlogs.r-pkg.org/badges/newsanchor)](https://cran.r-project.org/package=newsanchor)

## Purpose/Description

**newsanchor** provides a wrapper for <https://newsapi.org/>. **News
API** is a simple HTTP REST API for searching and retrieving live
articles from all over the web. You can get breaking news headlines, and
search for articles from over 30,000 news sources and blogs.

The API can generally be used for free for non-commercial use cases.
However, please be aware, that this results in some restrictions. For
instance, you cannot download more than 1.000 results. Please see also
<https://newsapi.org/pricing> for commerical options.

The package helps you to answer questions like the following:

  - What are the **top stories** running right now in the world?
  - What **articles** were published about your favorite politician,
    movie star, singer, or celebrity **today**?
  - What are the breaking news in **business**, **entertainment**, or
    **sports**?

## Status

The package is under continuous development and will be extended with
additional features in the future.

## Installation

### Stable Version (coming soon)

``` r
# install package from CRAN
install.packages("newsanchor")

# load package
library(newsanchor)
```

### Current Development Version

``` r
# install devtools package if it's not already
if (!requireNamespace("devtools", quietly = TRUE)) {
  install.packages("devtools")
}

# install package from GitHub
devtools::install_github("correlaid/newsanchor")

# load package
library(newsanchor)
```

## Introduction

### Authentication

You need an API key to access <https://newsapi.org>. You can apply for a
key at <https://newsapi.org/register/>. It is common practice to set API
keys in your R environment file. Hence, every time you start R the key
is loaded. The functions `get_headlines`, `get_headlines_all`,
`get_everything`, `get_everything_all`, and `get_sources` access your
key automatically by executing `Sys.getenv("NEWS_API_KEY")`.
Alternatively, you can provide an explicit definition of your api\_key
with each function call.

In order to add your key to your environment file, you can use the
function `edit_r_environ` from the `usethis` package.

This will open your `.Renviron` file in your text editor. Now, you can
add the following line to it:

    NEWS_API_KEY="yourkeygoeshere"

Save the file and restart R for the changes to take effect.

If your `.Renviron` lives at a non-conventional place, you can also use
the function `set_api_key` from the `newsanchor` package to add the API
key to your environment file. The function below appends the key
automatically to your R environment file.

``` r
# save the api key in the .Renviron file
set_api_key(api_key = "YOUR API KEY", 
            path    = "/my/path/to/.Renviron")
```

### get\_headlines / get\_headlines\_all

These functions connect to the API’s endpoint that returns breaking news
headlines for a country and category, or currently running on a single
or multiple sources. This is perfect if you want to download live
up-to-date news headlines and images. You can provide **specific
news-sources**, **categories**, **countries**, and/or **queries**. For
valid searchterms see the respective paragraph below.

``` r
# get headlines published by the Washington Post
results <- get_headlines(sources = "the-washington-post")
# get headlines published in the category sports
results <- get_headlines(category = "sports")
# get headlines published in Germany
results <- get_headlines(country = "de")
# get headlines published about Trump
results <- get_headlines(query = "Trump")
```

**Limitations:**

  - Please note, only **one** searchterm can be used for `category`,
    `country`, or `query`. Additional elements will be ignored.
  - In addition, `sources` must not be combined with `country` and/or
    `category`.

**Additional settings:**

The default of <https://newsapi.org> only allows to get maximal 100
results per search. Since some search terms might yield much more
results, you can use the option **page** to browse throught your results
(per default: page = 1). You can change the number of results returned
per request using **page\_size** (per default: page\_size = 100).

``` r
results <- get_headlines(category = "sports", page = 2)
results <- get_headlines(category = "sports", page = 3, page_size = 20)
```

**get\_headlines\_all:**

To automatically download all results, use **get\_headlines\_all**. This
function is build around `get_headlines` and provides the same options
(except for `page` and `page_size`).

``` r
results <- get_headlines_all(category = "sports")
```

**searchterms:**

We provide different dataframes :`terms_category`, `terms_sources`, and
`terms_country`. They contain information on valid search terms. Access
them if you are not sure whether some country or some sources will work.

``` r
terms_category
terms_country
terms_sources
```

### get\_everything / get\_everything\_all

This endpoint searches through articles from large and small news
sources and blogs. This includes breaking news as well as other kinds of
normal articles. This function needs a compulsory `query` with each
function call. You can make your search more fine-tuned using the
following additional options: <br>

  - surround entire phrases with quotes ("") for exact matches.
  - prepend words/phrases that must appear with “+” symbol (e.g.,
    +bitcoin).
  - prepend words that must not appear with “-” symbol (e.g., -bitcoin).
  - you can also use AND, OR, NOT keywords (optionally grouped with
    parenthesis, e.g., ‘crypto AND (ethereum OR litecoin) NOT
    bitcoin)’).

<!-- end list -->

``` r
# get everything published about Trump
results <- get_everything(query = "Trump")
# get everything published with the phrase "Trump says"
results <- get_everything(query = "Trump says")
```

As stated above, you always have to provide a `query` with the function
call. However, you can limit your results to multiple **sources**,
different **languages**, or different **domains**. You are also able to
**exclude\_domains** or mark the start (**from**) or end (**to**) time
of your search.

``` r
# get everything published about Trump
results <- get_everything(query = "Trump")
# get everything published about Trump in the Washington Post
results <- get_everything(query = "Trump", sources = "the-washington-post")
# get everything published about Trump in french
results <- get_everything(query = "Trump", languages = "fr")
# get everything published about Trump at bbc.com
results <- get_everything(query = "Trump", domains = "bbc.com")
# get everything published about Trump BUT NOT at bbc.com
results <- get_everything(query = "Trump", exclude_domains = "bbc.com")
# get everything published about Trump BUT NOT at bbc.com
results <- get_everything(query = "Trump", from = "2018-09-08")
```

**Limitations:**

  - If you use the option `sources`, there is a limitation to 20
    sources.
  - The options of `from` and `to` should be in ISO 8601 format.
  - The `language` needs to be a two digits ISO 639-1 code. You can only
    use one language for a search, default is all.
  - Some limitations are restricted to the free plan of www.newsapi.org.
    For example, you cannot access articles that have been published
    more than a month ago. This can only be accomplished using the paid
    plan.

**Additional settings:**

The default of <https://newsapi.org> only allows to get a maximum of 100
results per search. Since some search terms might yield much more
results, you can use the option **page** to browse throught your results
(per default: page = 1). You can change the number of results returned
per request using **page\_size** (per default: page\_size = 100).

``` r
results <- get_everything(query = "Trump", page = 2)
results <- get_everything(query = "Trump", page = 3, page_size = 20)
```

In addition, you can sort (**sort\_by**) the order of the articles.
Possible options are `relevancy` (articles more closely related to the
query come first), `popularity` (articles from popular sources and
publishers come first) and `publishedAt` (newest articles come first).
Per default articles are sorted by `publishedAt`.

``` r
# sort results by relevancy
results <- get_everything(query = "Trump", sort_by = "relevancy")
# sort results by popularity
results <- get_everything(query = "Trump", sort_by = "popularity")
# sort results by date
results <- get_everything(query = "Trump", sort_by = "publishedAt")
```

**get\_everything\_all:** To automatically download all results, use
**get\_everything\_all**. This function is build around `get_everything`
and provides the same options (except for *page* and *page\_size*). Be
aware that here, the limit of 1,000 articles kicks in if you do not have
a paid plan. The function will then provide you with a dataset of these
1,000 results and stop there.

``` r
results <- get_everything_all(query = "Trump")
```

**searchterms:** We provide several dataframes with valid search terms.
You can find all dataframes below:

``` r
terms_language
terms_sources
terms_country
terms_category
```

### get\_sources

This function returns the subset of news publishers that top headlines
are available from. This information is also provided in a dataframe
`terms_sources`. However, this function also allows to return sources
for specific categories or languages.

``` r
publisher <- get_sources()
```
