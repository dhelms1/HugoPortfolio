---
title: Web Scraping GPU Information with Rvest
author: Derek Helms
date: '2021-06-16'
slug: gpuscraping
banner: images/gpu_banner.jpg
categories: []
tags:
  - Projects
description: \[**CURRENTLY IN PROGRES**\] In this project, I use R and its library **Rvest** to scrape Newegg for information about GPUs including price, rating, and model specs. The main emphasis of this project is Web Scraping, Data Cleaning, and Exploratory Data Analysis. The data may be used for modeling but the model type and purpose has yet to be determined. 
---

![](img/banner.jpg)

# So Close... Yet So Far
If you're reading this, then most likely you too have some type of interest in machine learning or data science. And you know that GPUs are amazing for speeding up the processes you do in this fields, especially for Deep Learning. However if you're currently in the market for one of these GPUs, especially the new NVIDIA RTX Series which would be PERFECT for Deep Learning projects, you know how impossible it is to find one of them (special thanks to all the crypto miners and resellers out there). So I figured if I can't have one, might as well scrape Newegg for information on a bunch of different graphics cards and do some Data Analysis right? Not my first though, but it did sound like an interesting project. And although I initially wanted to implement it in Python, I figured this would be a great learning opportunity for R especially given my current internship. And so here we are, scraping Newegg for information on GPUs while I patiently wait for them to become available.

## Is this Ethical?
In my many hours of searching the internet about the rules of web scraping and if certain website allow it or not, I find myself asking "Am I really suppose to be doing this?". Some people say no, some say it's fine, others just avoid the question and continue their work. I still don't have the answer to this question, but in my attempt to collect the data as ethically as possible I've taken the following steps:

- Only creating requests ~2 times per day, spaced out by a few hours.
- Keeping the data private on my computer and not uploading for public use.
- No private information is being collected, only product details.

Each page on Newegg has around 36 results, and even though it has hundreds of pages I only scraped 10 of them over the span of 5 days. I didn't want to create too many requests and overload their site, so I tried to minimize the amount of time I spent sending requests. Hopefully this is ethical, and if not then this entire post was just hypothetical.

# The Scraping Script
So basically there are three main categories for which data was scraped: Model Specs (Brand, Series, Memory, etc.), Ratings (Average & Total Number), and Pricing Information (Price, Sale, & Shipping). I'll break down each of these, and the exact code used to collect the data.

Just a quick note, a lifesaver for this project was the [SelectorGadget](https://rvest.tidyverse.org/articles/selectorgadget.html) from Rvest and part of the tidyverse. Basically instead of having to inspect elements using html code, this allowed me to find css selectors in the page that I needed and extract them below (these are the string passed to the *html_nodes()* functions). 

## Model Specs
``` r
get_model_info <- function(gpu_link) {
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
So the first piece of information I wanted to pull were specs for each GPU (described in the cols variable above). This wasn't too hard to complete, the string in *html_nodes()* navigates to the specs tab and pulls the entire table from the website. I then collapsed this into a data frame, and if the specs tab was missing one of the desired pieces of information (from the cols variable), then I would fill the column with NA. The function returned a data frame with the select columns, which was then added to a larger data frame which I will describe later in the Data Collection section.

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
Now this... This was true pain to get working. If only Newegg had a normal rating system like every other website out there. But of course they have to put their ratings as "eggs", which is creative I will say, but also made pulling this information ridiculously long. I don't expect the above code to make much sense (it doesn't really to me and I'm the one who wrote it), but basically I had to read through the raw data without converting it to readable text using *html_text()*. I couldn't just index it either because each rating appeared at a different point for each gpu. However, the work around this was to split the raw data at the *rating rating* tag and take the second object, which I then subset ranging from index 10 to 30 to produce this:

> ="5 out of 5 eggs"></

This above is an example of rating for a "5 star" product, which I will now need to clean but that will occur later during the Data Processing section (I probably could have decreased the index range, but I wanted to keep a buffer just in case). Getting the Number of Ratings was much easier, just converting to readable text and taking the first item. I'm just glad this part is over, and now we can move onto the Pricing script code.

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
We go from the hardest to now the easiest piece of code in this entire project. I wish everything was this straight forward, just a simple css selector and converting to it text and we have all the variables we need. I pulled the current price, the savings tag (saying how much % off an item was), and the shipping cost (either "Free" or Numeric value). 

Originally I had tried to also get the original price before it was put on sale and create a new column, where I'd fill it with the current price if not on sale and the sale price if on sale but this gave me a lot of issues. The original prices was a different size vector each time and trying to input them was taking more time than I'd like to admit, so I skipped this and just stuck with the current price.

So now that all the functions have been explained, we can see how the data set was put together in one large data frame.

# Data Collection
So as I had previously stated earlier, the data was gather over a number of days since I only wanted to send a few requests per day. Around 2 pages were scraped per day, changing the *page_num* variable below to specify the given page. So first I'll go over how each script function from above was collected and put into its own data frame, and then how I combined them all together in order to make a final data set.

## Creating the Individual Data Frames
```r
page_num <- 1
link <- paste0("https://www.newegg.com/Desktop-Graphics-Cards/SubCategory/ID-48/Page-",
              page_num, "?Tid=7709")
gpu_links <- read_html(link) %>% html_nodes("a.item-title") %>% html_attr("href")

pricing_df <- rbind(get_gpu_pricing(link), pricing_df)
gpu_df <- rbind(t(sapply(gpu_links, FUN = get_model_info, USE.NAMES = FALSE)), gpu_df)
rating_df <- rbind(t(sapply(gpu_links, FUN = get_gpu_ratings, USE.NAMES = FALSE)),
                   rating_df)
page_df <- data.frame(Page=rep(page_num, length(gpu_links)))
link_df <- data.frame(URL=gpu_links)
```
*To be filled...*

## Creating the Final Data Frame
``` r
current_final_df <- read.csv('data\\gpu_raw_data.csv')
new_data_df <- cbind(page_df, link_df, gpu_df, pricing_df, rating_df)
new_data_df <- new_data_df %>% mutate(across(everything(), as.character)) %>% 
                  replace_with_na_all(condition = ~.x == 'NA')
if (exists('current_final_df')) {
  colnames(new_data_df) <- colnames(current_final_df)
  final_df <- rbind(current_final_df, new_data_df)
}
write.csv(final_df, 'data\\gpu_raw_data.csv', row.names = FALSE)
```
*To be filled...*


