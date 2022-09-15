Executing this step-by-step "Recipe" will associate the metadata of articles from a given university with the 'disruption score' given in the Small Teams dataset.
- Lingfei Wu; Dashun Wang; James Evans, 2021, "Replication Data for: Large teams develop and small teams disrupt science and technology", https://doi.org/10.7910/DVN/JPWNNK, Harvard Dataverse, V1 
  https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/JPWNNK

![Potion magique](images/Panoramix_1.jpeg)

```R
# R script for the "MAGic made easy" process presented at the 
# - BRIC 2022 conference (McMaster university, Hamilton: June 2022)
# - Access 2022 conference (Carleton university, Ottawa: October 2022)
# The aim is to assign a "disruption score" to articles from a given university.
# Jeffrey Demaine (ORICD #0000-0003-4586-1317)

# Fetch the packages:
install.packages("openalexR") # To query OpenAlex
install.packages("RSQLite")   # Load R's internal SQLite database.
install.packages("DBI")       # May not be necessary, as it seems to be inside RSQLite anyways...

# If R cannot find the "openalexR" package in CRAN, get it from GitHub:
install.packages("devtools")
devtools::install_github("massimoaria/openalexR")

# IMPORTANT: In the menu bar of RStudio do: 
# Session -> Set Working Directory -> Source File Location


# Load the libraries:
library(Matrix)
library(dplyr)
library(DBI)
library(openalexR)


# [1] BUILD a query to get all the articles from McMaster in OpenAlex for 2018-2020:
# Because OpenAlex has a limit of 10,000 records per request, break this into yearly
# slices and then add them together afterwards (SEE line 64):
query_inst <- oaQueryBuild(
  entity = "works",
  filter = "institutions.id:I106938459", # For example, the University of New Brunswick
  date_from = "2020-01-01",
  date_to = "2020-12-31"
)

# [1.1] SEND the query:
resultCount <- oaApiRequest(query_url = query_inst, total.count = TRUE, verbose=TRUE)

# [1.2] How many records will be returned?
resultCount$count

# [1.3] FETCH the records in JSON format:
resultRecords <- oaApiRequest(query_url = query_inst, total.count = FALSE, verbose=TRUE)

# [1.4] Converting a large number of JSON records takes a while...
df <- oa2df(resultRecords, entity = "works") #NOTE: This throws an error for 2021 data.

# [1.4.1] A bit of cleanup is required.
# The dataframe contains 4 fields containing lists of dataframes. These cannot be written to SQLite as such.
# As these fields are not needed and cause errors, simply delete them:
df$author <- NULL
df$TCperYear <- NULL
df$ids <- NULL
df$concept <- NULL

# [1.4.2] Similarly, these throw the error "Can only bind lists of raw vectors (or NULL)". So remove all lists:
df$related_works <- NULL
df$CR <- NULL
df$IS <- NULL


# [2] CREATE an SQLite database in memory. Put the dataframe into it as a table:
temp.db <- dbConnect(RSQLite::SQLite(), ":memory:")
dbWriteTable(temp.db, "oa_2020_UNB", df, append = TRUE)
# REPEAT STEPS 1 + 2 as necessary for each year.

# A bit of database management:
# [2.1] May be needed to tidy up tables:
dbExecute(temp.db, "DROP VIEW myview")

# [2.2] PARSE out the MAGid from the "id" field into a table "view":
dbExecute(temp.db, "CREATE VIEW myview AS SELECT *, SUBSTR(id, 23, 10) AS MAGid FROM oa_2020_UNB")

# [2.3] Check to see that the MAGid has been parse in the correct format:
peekaboo <- dbGetQuery(temp.db, "SELECT * FROM myview LIMIT 10;")
View(peekaboo)


# [3] Retrieve the SmallTeams dataset for the correct years into  a temporary database:
# The 'Small Teams' dataset should be in a table called "Disruptions", in a SQLite 
# database called "Disruptiveness.db", located in the same directory as this file.
SmallTeams.db <- dbConnect(RSQLite::SQLite(), "Disruptiveness.db")
ST_2020 <- dbGetQuery(SmallTeams.db, 'SELECT * FROM Disruptions WHERE Year = 2020')

# [3.1] Write the result to the temporary database:
dbExecute(temp.db, "DROP TABLE ST_2020")
dbWriteTable(temp.db, "ST_2020", ST_2020)


# [4] Finally, JOIN the OpenAlex records with the SmallTeams metadata:
join_result <- dbGetQuery(temp.db, 'SELECT ST_2020.Year, ST_2020.Field, 
  ST_2020.TeamSize, ST_2020.MultipleInstitutions, 
  ST_2020.Disruption, myview.* FROM ST_2020 INNER JOIN myview 
    ON ST_2020.MAGid = myview.MAGid')

View(join_result)

# [4.1] Save the result to a local database:
local.db <- dbConnect(RSQLite::SQLite(), "UNB_in_OpenAlex.sqlite")
dbWriteTable(local.db, "Disruption_UNB_2020", join_result)


# [5] Clean up the database connections:
dbDisconnect(temp.db)
dbDisconnect(SmallTeams.db)
dbDisconnect(local.db)

```
