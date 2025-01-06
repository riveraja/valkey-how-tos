This tutorial will shows how to manage the index

## Create the index

Let's create the index named sampleIdx to start with.

```redis Create the index
FT.CREATE sampleIdx ON JSON PREFIX 1 jkey: SCHEMA $.city AS city TEXT $.company AS company TEXT $.industry AS industry TEXT $.info AS info TEXT $.street AS street TEXT $.population AS population NUMERIC
```

## Alter the index

```redis Alter the index
FT.ALTER sampleIdx SCHEMA ADD $.country AS country TEXT
```

## Drop the index

```redis Drop the index
FT.DROPINDEX sampleIdx
```
