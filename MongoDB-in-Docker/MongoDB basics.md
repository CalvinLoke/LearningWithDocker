# Preface
While MongoDB is not directly related to Docker, it can be run as a service/container in a Docker environment. Hence, basic understanding of Docker and the different commands are recommended. 

# Setting up Mongo 
## Installing MongoDB in Docker
The image for MongoDB exists on DockerHub, and can be pulled from the repository using the following command: 

`docker pull mongo` 

It will pull the latest version by default, and if there is a need to pull an alternative or a different version, modify the command as follows:

`docker pull mongo:<version number>` 

To ensure that mongo has been successfully installed into your system, verify by checking if `mongo` exists in `docker images`. Visit [here](https://hub.docker.com/_/mongo) if you would like to read about the different versions of MongoDB that are available on DockerHub. It also contains the full documentation for the operation of MongoDB in a Docker environment. 

This document will heavily reference this documentation, though cut down for easier digestion and understanding. This documentation will also utilize 

## Running Mongo
To to run Mongo as a service, use the `docker run` command. 

`docker run -d -p 27017-27019:27017-27019 --name mongoTest mongo`

This command will run a mongo container in detached mode, with published ports of 27017-27019. The name modifier simply gives the container a recognizable name. 

To run MongoDB with a persistent path, modify the `docker run` command by appending `-v mongodata:/data/db`. 

Ensure that the directory is created first using:

`sudo mkdir -p /mongodata`

So the final modified command would be: 

`docker run -d -p 27017-27019:27017-27019 -v mongodata:/data/db --name mongoTest mongo`

To access the database, use the `docker exec`command. 

`docker exec -it mongoTest bash`

This will run the container in `interactive mode` with the bash shell. 

# Mongo Commands
This section contains the commands used for MongoDB. 

## Managing databases
### Show all databases
`show dbs`

### Show current database
`db`

### Create or Switch database
`use <database name>`

Note that newly created databases using this command will not show up in the `show dbs` command as it lacks any collections. Create a collection first using the `db.createCollection()` command. 

### Dropping a database
`db.dropDatabase()`

Note that this will drop the current database (eg switched to or created by the `use` command. 

### Creating a collection in a database
`db.createCollection(' <collection name> ')` 

This creates a collection with the desired name in the current active database. 

## Data handling/manipulation
Note this command should only be used if you are aware of the current database you are working with. Use the `db` or `use <database name>` commands to ensure that you are working with the correct database. 

### Single Data Entry
`db.<collection name>.insert({ <data> })` 

<data> can be a wide variety of data types, `int`, `string`, `arrays` etc. A single entry can contain multiple data fields, and to enter multiple fields, separate the different fields with a *comma*. 
  
In the following example, a 3 field data entry will be performed on an arbitary database in the collection *people*

`db.people.insert({
  name: 'John Doe',
  age: 46,
  likes: ['Fruits', 'Soccer'] 
 })`
 
The example demonstrates a data entry of 3 fields, `name`, `age` and `likes`. The `name` field is a `string` type, while the `age` field is `int` type and the likes is `array` type. The number of fields is not limited to three, and more fields can be added if there is a need. 

### Multiple Data Entries
`db.<collection name>.insertMany( [{ <data0> }, { <data1> }, { <data2> }] )` 

For multiple data entries, it is similar to that of a single data entry, except an `array` will be passed instead. Building on to the example above, multiple entries into the collection `people` will be done. 

`db.people.insertMany( [
  {
    name: 'Max Smith',
    age: 26
  },
  {
    name: 'Steve Irwin',
    age: 44,
    likes: ['Crocodiles', 'Wildlife']
  }
 ])`
 
Note how the entry for `Max Smith` is missing the `likes` field. Unlike relational databases, not all the fields are required to be filled up for a data entry.

### Data query
`db.<collection name>.find({ category: '<category name>' })`

Similar to relational databases, what ever that goes into the {} brackets will serve as the search criteria, just like the `WHERE` clause of SQL based databases. 

Note that if there are no search conditions, it will automatically return *all* of the database entries. 

However, due to the unformatted nature of the returned results, it is highly recommended to append `pretty()` at the end of the query for better formatting of the returned results. 

`db.<collection name>.find({ category: '<category name>' }).pretty()`

To sort the queried data, append `sort({ <category name>:1/0 })` to the query. Put `1` for ascending sort and `0` for descending sort. 

To limit the number of queried data, append `limit( <number> )`.

For a specified behaviour based on the number of returned queries, append `forEach( functionName(doc) { <actions> })`

The following table summarizes the different modifiers(?) that can be appended to the `find` query. 

| Modifier | Function |
| --- | --- |
| pretty() | Formats the returned queried results |
| sort() | Sorts the queried results |
| forEach( function(doc) { } ) | Executes function based on no. of returns? |

To find a specific entry, use `findOne({ category: <category name> })` instead. 

### Updating data
`db.<category name>.update({ <search crtieria> }, { <fields to be updated> })`

Note that by doing this, if the updated **are less than the orignal number of fields**, the updated table will have **remove the field that was not updated**. It is very important to take note of this whenever you are updating the data! Essentially, only do this if you wish to update the numer/type of categories in that data entry. 

It is also recommended that the `<search criteria>` be the `id field` as opposed to a data entry. The following example builds upon the initial examples and will be used to demonstrate the usage of the update function:

`db.people.update({ name: 'John Doe' },
  {
    name: 'John Smith',
    age: 99
  },
  {
    upsert: true
  }
 )`
 
The `upsert: true` checks if the entry of interest is found. If it is not, it will create a new entry with the categories and fields input into the `update()` command. 

In order to update the data entry *without affecting the number of categories*, perform the update command as follows instead:

`db.people.update({ name: 'John Doe' },
  {
    $set: {
    name: 'John Smith',
    age: 99,
    gender: 'male'
    }
  },
 )`
 
By using the `$set` instead, it will update the categories that were input into the orignal `update` function, while preserving the contents of the data in other categories. It will also add a category, that was originally not in the data entry, such as the `gender` category as illustrated in the example.

Comments can also be added using `$set`. They can simply be added by adding a updating the row/entry as a `comment` category. The example below illustrates this: 

`db.people.update({ name: 'John Doe' },
  {
    $set: 
    {
      comments: { 'This is a comment'}
    }
  },
 )`

To update numeric fields,`$inc` can be used to increase the value of the field. 

`db.people.update({ name: 'John Doe' },
  {
    $inc: { age: 5}
  }
 )`
 
As illustrated above, this would *increase* the value of the data field by 5. 

To rename a category, `$rename` can be used.

`db.people.update({ name: 'John Doe' },
  {
    $rename: { age: 'years alive'}
  }
 )`

### Deleting data 
`db.<category name>.remove({ <search criteria> })`

This command will delete the data entry/row based on the search criteria. 

## Sample data for importing
As there is no proper ingestion methods for the current database, dummy or sample data is required to test the database. 

[This repository is an excellent starting point](https://github.com/ozlerhakan/mongodb-json-files) as it provides sample `.json` files to import data into.

The command to import data in the MongoDB shell is as follows

`mongoimpott --db <database name> --collection <collection name> <path>`

Alternatively, importing data can be done in Compass. 

















 






