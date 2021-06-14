---
title: Web Scraping GPU Information with Rvest [In Progress]
author: Derek Helms
date: '2021-06-14'
slug: gpuscraping
banner: images/gpu_banner.jpg
categories: []
tags:
  - Projects
description: In this project, I use R and it's library **Rvest** to scrape Newegg for information about GPU's including price, rating, and model specs. The main emphasis of this project is Web Scraping, Data Cleaning, and Exploratory Data Analysis. The data may be used for modeling but the model type and purpose has yet to be determined. 
---

![](img/banner.jpg)

# Introduction
*To be filled in...*

# The Scraping Script
## Model Specs
``` r
get_model_info <- function(gpu_link) { # Pull information from "specs" tab
  cols <- c('Brand', 'Model', 'Chipset Manufacturer', 'GPU Series', 'GPU', 
            'Core Clock', 'Boost Clock', 'Memory Size', 'Memory Interface', 
            'Memory Type', 'Date First Available')
  model_info <- read_html(gpu_link) %>% 
    html_nodes("table , #product-mini-feature .tab-nav:nth-child(2)") %>% 
    html_table(fill=TRUE)
  info_df <- data.frame(tmp=t(do.call(rbind, model_info))) %>% 
    row_to_names(row_number = 1)
  for (cname in cols) {
    if (cname %notin% colnames(info_df)) {
      info_df[cname] <- rep(NA, nrow(info_df))
    }
  }
  info_df[cols]
}
```
*To be filled in...*

## Model Ratings
``` r
get_gpu_ratings <- function(gpu_link) {
  data.frame('AvgRating' = 
                substring(unlist(strsplit(as.character(read_html(gpu_link) %>% 
                html_nodes(".product-rating")), "rating rating"))[2], 10, 30),
             'NumberOfRatings' = read_html(gpu_link) %>% 
                html_nodes(".product-rating .item-rating-num") %>% 
                  html_text() %>% .[1])
}
```
*To be filled in...*

## Model Pricing
``` r
get_gpu_pricing <- function(page_link) {
  page <- read_html(page_link)
  tmp_df <- data.frame('Price' = page %>% html_nodes('.price-current') %>% html_text())
  tmp_df$CurrentSavings <- page %>% html_nodes('.price-save') %>% html_text()
  tmp_df$Shipping <- page %>% html_nodes('.price-ship') %>% html_text()
  tmp_df
}
```
*To be filled in...*

## Is this Ethical?
In my many hours of searching the internet about the rules of web scraping and if certain website allow it or not, I find myself asking "Am I really suppose to be doing this?". Some website say no, some say it's fine, others just don't say anything. I still don't have the answer to this, but in my attempt to collect the data as ethically as possible I've taken the following steps:

- Only creating requests 2-3 times per day, spaced out by a few hours.
- Keeping the data private on my computer and not uploading for public use.
- No private information is being collected, only product details.

Each page on Newegg has around 36 results, and even though it has hundreds of pages I only scraped 10 of them over the span of 4 days. I didn't want to create too many requests and overload their site, so I tried to minimize the amount of time I spent sending requests. Hopefully this is ethical (if not then this entire post is just hypothetical).

## Data Collection
*To be filled in...*



