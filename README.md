# NoSQL Challenge

## UK Food Ratings

The UK Food Standards Agency evaluates various establishments across the United Kingdom, and gives them a food hygiene rating. 
The editors of a food magazine, Eat Safe, Love, want to evaluate some of the ratings data in order to help their journalists and food critics decide where to focus future articles.

### Part 1 - Database and Jupyter Notebook setup

The data provided in 'Resources/establishments.json' is imported to **'uk_food'** database in MongoDB under **'establishments'** collection.

> mongoimport --type json -d uk_food -c establishments --drop --jsonArray establishments.json

### Part 2 - Update the database

**establishments** collection is updated with a newly opened restaurant **Penang Flavors**

**BusinessTypeID** field of the document that was insered into the collection is updated to reflect the respective business type.

The documents in **establishments** collection with **LocalAuthorityName** containing word **Dover** is removed.

The data type of **latitude** and **longitude** feilds are updated to **double**

The text values contained in **RatingValue** field is removed and data type of the field is set to **integer**.

### Part 3 - Exploratary Analysis

#### Questions

##### 1. Which establishments have a hygiene score equal to 20?

Following query was run to find the answer:

> query = {"scores.Hygiene" : {"$eq" : 20}}

The count_documents() function was used to find out that there are 41 establishments which have a hygiene score equal to 20.

The results were converted to a panda dataframe and total number of rows in the dataframe and top 10 rows were printed.

[View Result](./US_Food_Ratings/Output/hygiene_score20.csv)

##### 2.  Which establishments in London have a RatingValue greater than or equal to 4?

Following query was run to find the answer:

> query = { "LocalAuthorityName" : { "$regex" : "London"}, 
>          "RatingValue" : {"$gte" : 4}
>        }

The count_documents() function was used to find out that there are 33 establishments in London have a RatingValue greater than or equal to 4.

The results were converted to a panda dataframe and total number of rows in the dataframe and top 10 rows were printed.

[View Result](./US_Food_Ratings/Output/london_rating_gt4.csv)


##### 3. What are the top 5 establishments with a RatingValue of 5, sorted by lowest hygiene score, nearest to the new restaurant added, "Penang Flavours"?

degree_search = 0.01
Latitude and Longitude of "Penang Flavours" restaurant is provided here.
latitude = 51.49014200
longitude = 0.08384000

Following query was run to find the answer:
> query = { "RatingValue" : {"$eq" : 5}, 
>          "geocode.latitude" : {"$lte" : latitude + degree_search, "$gte" : latitude - degree_search},
>          "geocode.longitude" : {"$lte" : longitude + degree_search, "$gte" : longitude - degree_search}
>        }

Sort by hygiene score:
sort = [("scores.Hygiene", 1)] 

The results were converted to a panda dataframe and total number of rows in the dataframe and all the rows were printed.

[View Result](./US_Food_Ratings/Output/top_establishments_near_penang_flavors.csv)

##### 4. How many establishments in each Local Authority area have a hygiene score of 0?

Following pipelie was created to analyze this:

1. Matches establishments with a hygiene score of 0

> match_query = { "$match" : {"scores.Hygiene" : {"$eq" : 0}}}

2. Groups the matches by Local Authority

> group_query = { "$group" : { "_id" : "$LocalAuthorityName", "count" : { "$sum" : 1 }}}

3. Sorts the matches from highest to lowest

> sort_query = { "$sort" : {"count" : -1} }

The results were converted to a panda dataframe and total number of rows in the dataframe and top 10 rows were printed.

[View Result](./US_Food_Ratings/Output/establishments_by_local_authority_hyg0.csv)




# Disclaimer
This repo was published for educational purpose only. Copyright 2024 edX Boot Camps LLC. All rights reserved.




