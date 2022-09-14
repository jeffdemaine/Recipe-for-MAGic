---
layout: home
description: Presented at the Access 2022 conference. October 12-14th, 2022: Carleton University, Ottawa.
nav_order: 1
---

*A step-by-step guide on how to identify the most "DISRUPTIVE" academic articles from a given university.*


!(images/Panoramix_stamp_Belgique.jpg)

# Ingredients:

## [Microsoft Academic Graph](https://www.microsoft.com/en-us/research/project/microsoft-academic-graph/) ("MAG")
An experimental bibliographic database of ~150 million records that was freely available from 2015 TO 2021. No longer supported. 

- #### Arnab Sinha, Zhihong Shen, Yang Song, Hao Ma, Darrin Eide, Bo-June (Paul) Hsu, and Kuansan Wang. 2015. An Overview of Microsoft Academic Service (MAS) and Applications. In Proceedings of the 24th International Conference on World Wide Web (WWW ’15 Companion). ACM, New York, NY, USA, 243-246. http://dx.doi.org/10.1145/2740908.2742839


## [OpenAlex](https://openalex.org)
A new, free bibliographic database of 250+ million records. Built upon the MAG, it collects metadata from CrossRef and PubMed and is growing by 50,000 records per day.

- #### Priem, J., Piwowar, H., & Orr, R. (2022). OpenAlex: A fully-open index of scholarly works, authors, venues, institutions, and concepts. ArXiv. https://arxiv.org/abs/2205.01833


## [Small Teams Disrupt](https://lingfeiwu.github.io/smallTeams/)

Due to the increasing speed & size of mainstream science, the "Top 1%" (i.e. most cited) research attracts the most attention. Research that is less immediately impactful is being overlooked, leading to a lack of innovation. The insight is that while large teams develop science, small teams produce more disruptive ideas.

[DATASET IS AVAILABLE](https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/JPWNNK) (19.4mb)
- #### Lingfei Wu; Dashun Wang; James Evans, 2021, "Replication Data for: Large teams develop and small teams disrupt science and technology", https://doi.org/10.7910/DVN/JPWNNK, Harvard Dataverse, V1. https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/JPWNNK


## [SQLite](https://www.sqlite.org/index.html)
A small, fast, self-contained, high-reliability, full-featured, SQL database engine that is built in to the R programming language.


# Instructions:
To combine these datasets, use the R language and follow the [Recipe](recipe.md). 
