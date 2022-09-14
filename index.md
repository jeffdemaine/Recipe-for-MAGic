---
layout: home
title: Home
description: Presented at the Access 2022 conference. October 12-14th, 2022: Carleton University, Ottawa.
nav_order: 1
---

*A step-by-step guide on how to identify the most "DISRUPTIVE" academic articles from a given university.*

This is an internal hyperlink: [Preparation](preparation).

# Components

## Microsoft Academic Graph ["MAG"]
An experimental bibliographic database that was freely availble from 2015 TO 2021:

- #### Arnab Sinha, Zhihong Shen, Yang Song, Hao Ma, Darrin Eide, Bo-June (Paul) Hsu, and Kuansan Wang. 2015. An Overview of Microsoft Academic Service (MAS) and Applications. In Proceedings of the 24th International Conference on World Wide Web (WWW ’15 Companion). ACM, New York, NY, USA, 243-246. http://dx.doi.org/10.1145/2740908.2742839


## OpenAlex
A new, Open ("free") bibliographic database of 250+ million records. 

- #### Priem, J., Piwowar, H., & Orr, R. (2022). OpenAlex: A fully-open index of scholarly works, authors, venues, institutions, and concepts. ArXiv. https://arxiv.org/abs/2205.01833


## Small Teams dataset

Due to the increasing speed & size of mainstream science, the "Top 1%" (i.e. most cited) is attracting all the attention. Research that is less immediately impactful is being overlooked.  This is leading to a lack of innovation. The insight is that large teams develop and small teams disrupt science and technology.

DATASET IS AVAILABLE (19.4mb):
- #### Lingfei Wu; Dashun Wang; James Evans, 2021, "Replication Data for: Large teams develop and small teams disrupt science and technology", https://doi.org/10.7910/DVN/JPWNNK, Harvard Dataverse, V1. https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/JPWNNK


## SQLite


# Instructions:
To combine these datasets, use the R language and follow the [Recipe](recipe.md). 

This highlights text as a 'button': `SoR_metadata`.


```r
# Split into words by whitespace for a quick inspection:
snoopy <- c(1, 2, 3, 4)

print(type(snoopy))
```
