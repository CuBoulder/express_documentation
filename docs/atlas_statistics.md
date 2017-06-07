# Atlas Statistics

## Atlas Front-end

There is much fanfare and talk of a front-end skin that will make querying the site statistics objects easier, but for right
now, a simple GET request in your browser will do. 

## Statistics Object Structure

Site statistics are compiled and sent to Atlas during cron runs once per day. The statistic objects are then stored in a
MongoDB instance where we will query them. 

Since MongoDB is a document store certain restrictions come into play when you are making you queries. Unlike a relational
database, we can't join tables to make complex queries. 

The stat entries are made via Python where you define the type for each part of the statistics object. Some parts of the stats object are strings and some are arrays. Where it gets tricky is trying to define a schema for the stats object when you don't know what the structure of child elements would be. 

### Querying Strictly Typed Fields

For instance, if we have a part of the stats object called "bundles" and we list each bundle underneath it, e.g. "bundles.cu_news_bundle", we have to know what would be beneath "bundles.cu_news_bundle" in order to be able to query that data. If we don't define that structure before hand, the MongoDB query syntax won't work as expected. 

```
<nodes_by_type>
  <article>26</article>
  <article_list_page>12</article_list_page>
  <content_list_page>2</content_list_page>
  <faqs>2</faqs>
  <file>14</file>
  <page>78</page>
  <person>9</person>
  <photo_gallery>2</photo_gallery>
  <webform>6</webform>
</nodes_by_type>
```

As you can see in the example above, we have to define the schema version in the stats object schema so we can query it. Since we know what node types we have, we can define those in the schema and then tally up how many nodes per type. 

To query, let's say, the sites that have more that 70 basic pages, we can send a request like...

```
https://osr-atlas03.int.colorado.edu/statistics?where={"nodes_by_type.page":{"$gt":70}}
```

**nodes_by_type.page** gets you down to where the code count stat is for the Basic Page content type. You can combine any parents in the hierarchy with periods to drill down to where you need to perform your search. 

**"$gt":70"** looks for any "nodes_by_type.page" that have a higher count than 70, i.e. counts that are "greater than" 70 nodes. The MongoDB query syntax for $gt can be found here, and there is a lot of information on other operators: https://docs.mongodb.com/manual/reference/operator/query/gt/

### Querying Lossely Typed Fields

But what happens if you don't know what a site might have for a particular key? For those cases, we concatentate the data into a comma-separated string. 

```
<nodes_other>collection_item, people_list_page, newsletter</nodes_other>
```

Since we don't know what node types a site might have enabled on it, we can't explicitly declare them and have to add a concatenated string that could contain any sort node type machine name. 

```
https://osr-atlas03.int.colorado.edu/statistics?where={"nodes_other":{"$regex":"collection"}}
```

**nodes_other** isused in the same way as the previous query, but we are using the $regex operator now. The documentation for the $regex operator (https://docs.mongodb.com/manual/reference/operator/query/regex/) says a lot of things, but I was only able to look for a string of text that might be in the field I am trying to query. So, since "collection" appears in the "nodes_other" field example, that site record will be returned even though it isn't the only other node type listed.
