---
title: "The Basic Needs Student Success Survey (BNS3) Website [IN PROGRESS]"
date: 2021-12-08
slug: bns3website
excerpt: The final product of my summer Undergraduate Research Program and partially into my work as a Data Science Intern, the BNS3 website has been published to present findings from the pilot 2 survey. The focus of the website is on exploratory data analysis results from survey data, as well as cross campus comparison.
hero: "/images/bns3website_img/banner.jpg"
authors:
  - Hugo Authors
---

[Official Website Homepage](https://chicocalfresh.github.io/bns-pilot2-website/index.html)

# What is BNS3?

The Basic Needs Student Success Survey (BNS3) is a survey focused on assessing food insecurity, health, and assistance program effectiveness for undergraduate students who are currently enrolled in the Educational Opportunity Program (EOP). This specific pilot that I worked on is the second study conducted by the Center for Healthy Communities at Chico State, and was administered to students at three CSU campuses: Chico, Fresno, and Northridge. The main purpose for this second cross-sectional study was to revise the BNS3 tool and validate student perception on impact of assistance programs before the statewide dissemination (which we are currently working on now). 

The homepage of the website does a much better job at explaining the background, run time, and purpose of the survey than I do, so please feel free to [click here](https://chicocalfresh.github.io/bns-pilot2-website/index.html) to view the page. 

# Data Processing

I never really understood how much data processing you truly do with real world data until I began working on this project. If you've done any kind of data science or machine learning project than you've mostly likely had experience working with data from Kaggle or similar websites, and there is some data processing involved in order to get it into a usable format. Fill missing values with the column mean, change some data types around, a little feature engineering magic here and there. But nothing too serious right? We're talking maybe a couple hours and 100 lines of code before you're ready to go. And that's what I figured when I heard that I'd be processing the survey data for BNS3 (spoiler: it wasn't that easy).

I wasn't sure what I was expecting, maybe like a short questionnaire of Yes/No and numerical responses that I could clean up in a couple hours? But then I opened the data file... And cried a little bit inside. Hundreds of questions (all of which needed to be reformatted) and what would turn out to be my biggest enemy, free response questions. I still have nightmares about trying to parse them into usable answers, but it's okay now... I think.

So I started cleaning, and 30 hours later, I thought I had finally finished. But then I met with my supervisor, found more things to reformat, and another 30 hours of cleaning began. Repeat this processes over the first month of my research program and I finally finished the cleaning script (kind of at least). An entire month just for cleaning data... And it wasn't even finished. 6 months later I still go back and add updates here and there when I find an error or we change our minds on a previous decision. But it was a learning experience like no other, real world data isn't as pretty as "machine learning ready" data sets you find online, and spending weeks figuring out how to process data was one of the main learning experiences from my research program. But also I'd be happy if I don't see any uncleaned data for a while.

# Building the Website
*To be filled in...*

## Exploratory Data Analysis
*To be filled in...*

# Where to now?
*To be filled in...*



