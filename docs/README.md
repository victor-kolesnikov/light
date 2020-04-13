Light Data Lake Thoughts

Have a datasets catalog under your full control? Here is what you can do with it.

Over this long weekend, I have doing some design for a data platform which hosts datasets and has a custom built catalog. 
The catalog is accessible for the users that can do some data analytics via various tools. 
I was trying to find a way for how to expose a partitioned dataset in the catalog for the users who want to query 
data over some period of time. The problem was with data versions in the dataset - for each processing day the data could 
arrive many times and only one version of it would be interesting for those trending queries. 
Sure, it is not a big problem - we can say that only the latest version should be used or have another dataset with the versions that are deemed
to be published, but I wanted to find more elegant way.

Let's consider the example below:

![Catalog Magic in a Data Lake](data-lake-catalog-magic.svg)

The dataset A (1) is hosted in some s3 bucket and for 14th of April four versions of data 
have arrived. The datasets catalog has registered the datasets partitions - (2). A user writes a query (3) and fires it 
into the platform. The platform extracts the query criteria and performs partitions pruning via the catalog (4) - 
the partitions scan has only one partition to be processed. Nothing is unusual so far.

Now imagine that your platform has two data areas: the staging area where all versions of data are accumulated and the publishing area 
where only one version of the data is allowed. The publishing mechanism can be implemented within the catalog - we can create a 
virtual dataset (5) by pointing its partition to the partition associated with the version that is being published. No data 
needs to be copied - we link the same file to the new dataset. The fact that the published dataset has only one partition 
per a day makes all the possible trending queries on it very simple. 

If you can generate entries into your catalog and fabricate partitions in a form you need, if you can set up
business processes around your data so that only one version is selected to be final, you can drastically simplify life of 
you data analysts who would not be forced anymore to deal with technical versions and can focus on others more interesting 
 aspects of data analysis. 
 
P.S. In our set up we are using Apache Spark with custom DataSource over some S3 compatible store. 

![The original drawing...](data-catalog-drawings-1.jpg)
