# Never Break The Chain

- Take a starting point: This Girl's In Love With You
- Identify an attribute that you like about it
- Make a playlist based on that

## Detailed step by step: Version 0.1
1. Take a starting point
   1. Input for the WikiData ID of the album
   2. Get the Wikidata entry:
	  1. Get the producer for that album
	  2. Get all the values of those attributes
2. Identify an attribute that you like about it
   1. Display all the attributes and their values
   2. Select the attribute and get its value
3. Make a playlist based on that
   1. Template search on Wikidata for Albums using that starting point and that attribute.

### Getting all attributes and their values
- https://opendata.stackexchange.com/questions/10334/get-items-properties-know-qid
- https://stackoverflow.com/questions/41527706/get-properties-by-qid/41532485#41532485

- This is quite close
```
SELECT ?p ?attName  WHERE {
      BIND(wd:Q1939850 AS ?q)
      #?q wdt:P31 wd:Q5.
      ?q ?p ?statement.
      ?realAtt wikibase:claim ?p.
      ?realAtt rdfs:label ?attName.
      FILTER(((LANG(?attName)) = "en") || ((LANG(?attName)) = ""))
}
GROUP BY ?p ?attName
```


### Get all albums produced by Jerry Wexler

```
# Get all albums that match the producer Q537722
SELECT ?album ?albumLabel WHERE {
SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE]". }
      ?album p:P162 ?statement0.
      ?statement0 (ps:P162/(wdt:P279*)) wd:Q537722.
      ?album p:P31 ?statement1.
      ?statement1 (ps:P31/(wdt:P279*)) wd:Q482994.
  }
```

```
SELECT DISTINCT ?item ?itemLabel WHERE {
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE]". }
  {
    SELECT DISTINCT ?item WHERE {
      ?item p:P162 ?statement0.
      ?statement0 (ps:P162/(wdt:P279*)) wd:Q537722.
      ?item p:P31 ?statement1.
      ?statement1 (ps:P31/(wdt:P279*)) wd:Q482994.
    }
    LIMIT 100
  }
}
```
