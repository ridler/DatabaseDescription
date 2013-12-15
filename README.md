# 1: Cassandra
## Application = Simple Facebook Profiles
-	Facebook sells the service of self-expression via a customizable profile that keeps a structured format.
-	The data represents peoples’ experiences (photos), thoughts (status updates) and preferences (likes)

## Why Cassandra is the best fit
-	Cassandra is most suitable for content management.  Facebook profiles have a lot of content that is in the form of structured data, such as photo albums and status updates, which are almost like a mini-blogging system.  More reasons to use Cassandra include:
-	There is a vast population of users with Facebook profiles, so the fact that Cassandra handles data across many servers will be very beneficial.  This decentralization feature is also related to scalability, so when new machines are added, service can increase linearly.
-	A relational DBMS is not the best option because it will be slower in terms of read-write time, and not as flexible for storing and managing profile content.
-	Using HBase or Mongo would be a very similar experience, but Cassandra’s column family and keyspace models are more suited for structured document storage.  Hbase would encourage heavy use of Hadoop, which may not be wanted, and Mongo would probably be less scalable and slower.

## Column families:
-	Notes: the bold words are keys.  All values have a timestamp that is not shown for simplicity.
users				
| **taggr** | name | password | email | location |
------------|------|----------|-------|----------|
|	    | Tagg Ridler | batman11 | tagg@yahoo.com | Boulder,CO |
| **mileyc** | name | password | location | |
| 	     | Miley Cyrus | cantstop | Hollywood, CA | |	
| **baracko** | name | password | email | |
| 	      | Barack Obama | potus | bo@whitehouse.gov | |



## CQL queries
``` SQL
INSERT INTO users (name, password, location)
VALUES (Lana Del Rey, tropico, Cony Island, NY)

SELECT COUNT (*)
FROM users
WHERE location = ‘Hollywood, CA’

CREATE COLUMNFAMILY Users (
	KEY uuid PRIMARY KEY,
	name text,
	password text,
	email text,
	location text );
CREATE COLUMNFAMILY FriendsWith (
	KEY uuid PRIMARY KEY,
	Friends text );
CREATE COLUMNFAMILY StatusUpdates (
	KEY uuid PRIMARY KEY,
	body text,
	tags text,
	photo blob);
```
	
# 2: XML language
``` xml
<?xml version="1.0"?>
<profile>
	<name></name>
	<profilePhoto></profilePhoto>
	<posts>
		<post>
			<time></time>
			<location></location>
			<tags>
				<tag></tag>
			</tags>
			<body>
				<photo></photo>
				<text></text>
			</body>
		</post>
	</posts>
	<friends>
		<friend>
			<name></name>
			<profilePhoto></profilePhoto>
			<link></link>
		</friend>
	</friends>
</profile>
```
