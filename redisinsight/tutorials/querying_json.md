This tutorial shows how to query JSON documents.

## Querying JSON documents

Find all documents where info has words "quo veritatis".

```redis Documents with info quo veritatis
FT.SEARCH sampleIdx '@info:(quo veritatis)'
```

As above, but return only 3 elements - company, city, population

```redis Return only 3 elements
FT.SEARCH sampleIdx '@info:(quo veritatis)' RETURN 3 company city population
```

As above, but limit the result set to only 3

```redis Limit results
FT.SEARCH sampleIdx '@info:(quo veritatis)' RETURN 3 company city population LIMIT 0 3
```

As above, but this time sort by population ascending

```redis Sort by population
FT.SEARCH sampleIdx '@info:(quo veritatis)' RETURN 3 company city population LIMIT 0 3 SORTBY population ASC
```

As above but this time we want to filter where city contains words Bogisich or Davis

```redis Filter by city containing Bogisich or Davis
FT.SEARCH sampleIdx '@info:(quo veritatis) @city:(bogisich|davis)' RETURN 3 company city population SORTBY population ASC
```

As above but this time we want to filter by population less than 30 million

```redis Where population is less than 30 million
FT.SEARCH sampleIdx '@info:(quo veritatis) @city:(bogisich|davis) @population:[-inf 30000000]' RETURN 3 company city population SORTBY population ASC
```

As above but this time we want to filter by population between 20million to 30million

```redis Filter by population between 20M to 30M
FT.SEARCH sampleIdx '@info:(quo veritatis) @city:(bogisich|davis) @population:[20000000 30000000]' RETURN 3 company city population SORTBY population ASC
```

Showing score and explain score

```redis Using WITHSCORE and EXPLAINSCORES
FT.SEARCH sampleIdx '@info:(quo veritatis) @city:(bogisich|davis) @population:[20000000 30000000]' RETURN 3 company city population SORTBY population ASC WITHSCORES EXPLAINSCORE
```

## Aggregation queries

Get the total number of documents as count_country where info contains the words "non optio", grouped by country

```redis Count of documents group by country filtered by info
FT.AGGREGATE sampleIdx "@info:(non optio)" GROUPBY 1 @country REDUCE COUNT 0 AS count_country
```

As above but we sort by the last pipeline in the aggregation in descending order with limit

```redis Sort by count_country in descending order
FT.AGGREGATE sampleIdx "@info:(non optio)" GROUPBY 1 @country REDUCE COUNT 0 AS count_country SORTBY 2 @count_country DESC LIMIT 0 3
```

## Using explain

It is best to select raw mode (-r) when running this

```redis Getting the explain plan
FT.EXPLAINCLI sampleIdx '@info:(quo veritatis) @city:(bogisich|davis) @population:[20000000 30000000]' RETURN 3 company city population
```
