---	
layout: post	
title: Azure Query Time JOINs
---	

# Solving Query Joins in Azure

## Problem Statement:
In most real world use cases, there exist several relational dependencies between our datasources.
These relationships need to be joined and queried at search time; since these datasources are currently normalized or split logically into tables with no redundant information.
Other key search service providers like Lucene and SOLR handle this by super-fast query joins using optimized Application side joins. As we will see later, we cannot use this method for our use cases due to certain restrictions. 
We need to find suitable alternatives for these join queries.

## Methods:
### Application Side Joins: 
In this method the results matching from the first index are then queried against the second index and the resulting results are joined on the application side.

<img src="/images/img_az.jpg" width="400"/>

It has been used successfully in Lucene and SOLR with the following enhancements over normal filtering:
Caching: The documents which are frequently used are cached, which gave a 20X improvement.
Converting Strings to Numbers: String matching during the filter step is very time and resource intensive. Hence strings are converted to numbers, which are used for matching instead. This gave a 50X improvement.
We cannot ensure that Azure is doing these, but it is the SOTA approach, so it must be the underlying process.

<ins>PROS:</ins>
- Data can remain normalized.
- Write performance improves, since only once source needs to be updated.

<ins>CONS:</ins>
- Need to run extra queries and perform expensive joins at search time.
- Azure’s restriction on the GET filter query is 8 KB, hence some searches may exceed that limit; if we have millions of rows in our datasources making our queries too long.
- Search relevance suffers, since the scores from the different indexes need to be combined arbitrarily.

<ins>When to use it?</ins>\
Data Sources where data has a limited number of matching documents and preferably, is seldom updated (so that caching can help).

### Data Denormalization:
This is a key technique which eliminates the need for joins.
The tables are flattened along the required fields, which gives us the best performance, since we are using Azure Search how it is meant to be used.
Using the Complex Datatypes (where fields with different datatypes can stored together as collections) available in Azure, we can handle both 1-1 and 1-N relationships in our data.
The combined matching results can be displayed separately on the client-side using predetermined formats.

<ins>PROS:</ins>
- Speed, since no need for expensive client-side joins and extra queries.
- Better read performance.
- Better search relevance, since all the related information is stored together, and the inbuilt search relevance scores can be used.
- Can reduce the number of indexes, if full denormalization is performed.

<ins>CONS:</ins>
- Requires special index design
- Increased complexity in case of extremely nested JOIN relationships.
- Latency during write for data update of frequently updated datasources.
- Causes explosion of data for very highly 1-N relationships which may introduce latency.

<ins>When to use it?</ins>\
Data Sources where data isn’t updated very frequently, and simple join relationships exist between them.

### Azure SQL VIEWs:
The most natural solution to the problem, since we can replicate the current Query structure directly.
A View is essentially a SQL query between 1 or more datasources. 
It can be connected to an Indexer and loaded into the index.
The index can be updated by simply running the indexer once.

<ins>PROS:</ins>
- Most closely resembles the current SOTA approach.
- Easy update irrespective of the update cycles of the datasources.
- Can model highly complex, nested JOINs.
- Optimal search relevance.
- Can be easily altered as per requirement, without changing the indexer/index.

<ins>CONS:</ins>
- Only works with Azure Databases.
- Requires an additional Indexer and Index.

<ins>When to use it?</ins>\
Complex Nested JOIN queries which cannot be modelled by other approaches.

## Conclusion:
Unfortunately, there is no single correct solution to this problem. Our final solution will likely be an amalgamation of these approaches applied to relevant datasources.

## References:
- [LUCENE Approach](https://seecr.nl/2013/10/16/reducing-index-maintenance-costs-with-query-time-join-for-solrlucene/ "LUCENE Approach")
- [Azure SQL](https://docs.microsoft.com/en-us/sql/t-sql/statements/create-view-transact-sql?view=sql-server-ver15 "Azure SQL")
- [Elastic Search Approach](https://www.elastic.co/guide/en/elasticsearch/guide/2.x/relations.html "Elastic Search Approach")
