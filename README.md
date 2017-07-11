# MongoDB Atlas Competition

Welcome to the MongoDB Atlas Competition! This is your chance to explore the true power of MongoDB Atlas, for FREE, and win some awesome prizes... Good luck!

### Step 1 - Setup

1. Signup for [MongoDB Atlas](https://www.mongodb.com/atlas)

2. Create a new M0 Free Tier cluster (and don’t forget to give it a catchy name). Check out this [[screenshot of the Atlas Free Tier setup]](atlas_setup_screen.png)

  Hint: Make sure you provide an admin username & password at the bottom of the cluster setup page

3. Download and uncompress the `usa_zipcodes.zip` dataset from this repository

4. Import the zipcode dataset into your Atlas cluster

Hint: mongoimport can be run from your local computer and pointed directly at MongoDB Atlas as the destination. If you need more help, see the **"Beware, spoilers below!"** section.


5. Once you’ve got your dataset up and running in MongoDB Atlas, use the ‘mongo’ CLI (or create a program for extra points) to find the answers to these problems...

### Step 2 - Challenges

Hint: You might want to use the [Aggregation Framework](https://docs.mongodb.com/manual/aggregation/) for these...

1. How many States in our dataset have populations **above** 10 million?

2. Of those States with populations **greater than 10 million**, which State has the **4th largest** population?

3. Find the average City population for each State. Which State has the **lowest average**?

Hint: You’ll only need to use two $group operations to find each State's average city population. 
Once you have this, just sort your result set accordingly and you should have the answer!

Extra Hint: State *"CT"* (Connecticut) should have an average City population of *14674.625*, and this is **not the lowest**.

4. Once you’ve got the answers to the above you should have some activity on your MongoDB Atlas cluster. Head over to the [Atlas GUI](https://cloud.mongodb.com) and take a screenshot of the Metrics (monitoring) panel. It should look something like [[this Atlas Metrics screenshot]](atlas_metrics_screen.png)


### Beware, spoilers below!

1. Need help importing your dataset into MongoDB Atlas?

- You'll find it much easier to do this with the mongoimport and mongo shell tools. Both of which are avaiable when installing MongoDB. [Check this out](https://docs.mongodb.com/getting-started/shell/installation/) if you haven't already got the tools installed.

- If you can’t connect to the Atlas cluster, make sure you’ve configured the Security IP Whitelist in the [Atlas GUI](https://cloud.mongodb.com)

- If your mongoimport reports an error with ’SASL authentication’, but you can definitely connect (try using mongo client to test), then you might want to check you have a user with readWrite permissions:
    - [mongoimport access requirements](https://docs.mongodb.com/manual/reference/program/mongoimport/#required-access)
    - [Using mongoimport with MongoDB Atlas](https://docs.atlas.mongodb.com/import/mongoimport)

2. Before you start building your aggregation operation, you might want an idea of what the data looks like. Using the [mongo shell client](https://docs.mongodb.com/getting-started/shell/client/), you can quickly determine the type of schema you're working with.

```
AshsCatchyClusterName-shard-0:PRIMARY> db.zipcodes.findOne()
{
    "_id" : "01001",
    "city" : "AGAWAM",
    "loc" : [
        -72.622739,
        42.070206
    ],
    "pop" : 15338,
    "state" : "MA"
}

AshsCatchyClusterName-shard-0:PRIMARY> db.zipcodes.find().limit(5)
{ "_id" : "01001", "city" : "AGAWAM", "loc" : [ -72.622739, 42.070206 ], "pop" : 15338, "state" : "MA" }
{ "_id" : "01002", "city" : "CUSHMAN", "loc" : [ -72.51565, 42.377017 ], "pop" : 36963, "state" : "MA" }
{ "_id" : "01005", "city" : "BARRE", "loc" : [ -72.108354, 42.409698 ], "pop" : 4546, "state" : "MA" }
{ "_id" : "01007", "city" : "BELCHERTOWN", "loc" : [ -72.410953, 42.275103 ], "pop" : 10579, "state" : "MA" }
{ "_id" : "01008", "city" : "BLANDFORD", "loc" : [ -72.936114, 42.182949 ], "pop" : 1240, "state" : "MA" }
```

3. Struggling to get the States with populations over 10 million?

- The equivalent SQL for this aggregation looks like:
```
SELECT state, SUM(pop) AS totalPop
FROM zipcodes
GROUP BY state
HAVING totalPop >= (10*1000*1000)
```

4. If you can't quite combine everything into one aggregation command, perhaps try splitting it out and storing the results in a new collection. You can do this with the `$out` operator. [Details here](https://docs.mongodb.com/manual/reference/operator/aggregation/out/).

5. You don't necessarily need to sort all of your results in the aggregation stage. Perhaps try storing the result in a new collection (with step 3 above), and then using an easy `.sort({field_name:1})` or `.sort({field_name:-1})` depending on your sort direction. You can also do a `.count()` in your query for ease of use. Example: `db.zipcodes_temp.find().limit(1).sort({avgCityPop:1})` might be handy to find the State with the lowest average city population!

6. This is an Atlas challenge, and not a test of your aggregation skills. If you're still really stuck there is no shame in using [this link](https://docs.mongodb.com/manual/tutorial/aggregation-zip-code-data-set/) to get the correct aggregation operation for each challenge.
