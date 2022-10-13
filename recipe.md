Executing this step-by-step "Recipe" will associate the metadata of articles from a given university with the 'disruption score' given in the Small Teams dataset.
- Lingfei Wu; Dashun Wang; James Evans, 2021, "Replication Data for: Large teams develop and small teams disrupt science and technology", https://doi.org/10.7910/DVN/JPWNNK, Harvard Dataverse, V1 
  https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/JPWNNK

![Potion magique](images/Panoramix_1.jpeg)

```R
# R script for the "MAGic made easy" process presented at the BRIC 2022 conference.
# The aim is to assign a "disruption score" to articles from a given university.
# Presented at: 
# - BRIC conference (June 2022) in Hamilton, ON [Original]
# - ACCESS conference (October 2022) in Ottawa, ON [Updated to work with openalexR v1.0]
# Jeff Demaine (ORICD #0000-0003-4586-1317), June 2022. Version 2, October 2022

# [SETUP A] Fetch the packages:
install.packages("openalexR") # To query OpenAlex
install.packages("RSQLite")   # Load R's internal SQLite database.
install.packages("DBI")       # May not be necessary, as it seems to be inside RSQLite anyways...
install.packages("dplyr")

# [SETUP A - alternate] If R cannot find the "openalexR" package in CRAN, get it from GitHub:
install.packages("devtools")
devtools::install_github("massimoaria/openalexR")

# [SETUP B] IMPORTANT: In the menu bar of RStudio do: 
# Session -> Set Working Directory -> Source File Location

# [SETUP C] Load the libraries:
library(Matrix)
library(dplyr)
library(DBI)
library(openalexR)


# [1] BUILD a query to get all the articles from a university in OpenAlex for 2018-2020:
# Because OpenAlex has a limit of 10,000 records per request, break this into yearly
# slices and then add them together afterwards:
results_DF <- oa_fetch(
  entity = "works",
  authorships.institutions.id = "I129902397", # Dalhousie
  from_publication_date = "2020-01-01",
  to_publication_date = "2020-12-31"
)

# [1.1] SET the name of the university being queried:
universityName <- "Dalhousie"


# [2] CLEAN UP the dataframe. These throw the error "Can only bind lists of raw vectors (or NULL)". 
# So remove all fields that contain lists:
results_DF$author <- NULL
results_DF$counts_by_year <- NULL
results_DF$ids <- NULL
results_DF$concepts <- NULL
results_DF$referenced_works <- NULL
results_DF$issn <- NULL
results_DF$relevance_score <- NULL


# [3] CREATE an SQLite database in memory. Put the dataframe into it as a table:
temp_db <- dbConnect(RSQLite::SQLite(), ":memory:")
dbWriteTable(temp_db, "OpenAlex_results", results_DF, append = TRUE)
# REPEAT STEPS 1 + 2 as necessary for each year.

# A bit of database management:
# [3.1] May be needed to tidy up tables:
dbExecute(temp_db, "DROP VIEW OpenAlex")

# [3.2] PARSE out the MAGid from the "id" field into a table "view":
dbExecute(temp_db, "CREATE VIEW OpenAlex AS SELECT SUBSTR(id, 23, 10) AS MAGid, * FROM OpenAlex_results")

# [3.3] Check to see that the MAGid has been parsed in the correct format:
peekaboo <- dbGetQuery(temp_db, "SELECT * FROM OpenAlex LIMIT 10;")
View(peekaboo)


# [4] Retrieve the SmallTeams dataset for the correct years into  a temporary database:
# The 'Small Teams' dataset should be in a table called "Disruptions", in a SQLite 
# database called "Disruptiveness.db", located in the same directory as this file.
SmallTeams.db <- dbConnect(RSQLite::SQLite(), "..\\DATA\\Disruptiveness.db")
ST_2020 <- dbGetQuery(SmallTeams.db, 'SELECT * FROM Disruptions WHERE Year = 2020')

# [4.1] Write the result to the temporary database:
dbExecute(temp_db, "DROP TABLE SmallTeams")
dbWriteTable(temp_db, "SmallTeams", ST_2020)


# [5] Finally, JOIN the OpenAlex records with the SmallTeams metadata:
join_result <- dbGetQuery(temp_db, 'SELECT SmallTeams.Year, SmallTeams.Field, 
  SmallTeams.TeamSize, SmallTeams.MultipleInstitutions, 
  SmallTeams.Disruption, OpenAlex.* FROM SmallTeams INNER JOIN OpenAlex 
    ON SmallTeams.MAGid = OpenAlex.MAGid')

# [5.1] Save the result to a local database. Modify the file name as appropriate:
local.db <- dbConnect(RSQLite::SQLite(), "..\\OUTPUT\\Disruption_"+universityName+"_via_OpenAlex_2.sqlite")
dbWriteTable(local.db, "Disruption_"+universityName+"_2020", join_result)


# [6] Clean up the database connections:
dbDisconnect(temp.db)
dbDisconnect(SmallTeams.db)
dbDisconnect(local.db)

```
