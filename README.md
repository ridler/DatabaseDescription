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

![](img/users)
![](img/friends_with)
![](img/status_updates)

## CQL queries
``` SQL
INSERT INTO users (name, password, location)
VALUES ("Lana Del Rey", "tropico", "Cony Island, NY")

SELECT COUNT (*)
FROM users
WHERE location = "Hollywood, CA"

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
	photo blob );
```
	
# 2: XML language

## Language Definition
- This model is general enough to represent any system that manages profiles with a picture, posts that can contain pictures, and friends or connections to other users with profiles.
- This model supports the `newPost` operation which allows a user to create a new post, and the `suggestedFriends` operation which suggests new connections for users based on the friends of their friends.

``` xml
<?xml version="1.0"?>
<profile>
	<name></name>
	<profilePhoto></profilePhoto>
	<newPost>
		<source></source>
		<tagBox></tagBox>
		<inputText></inputText>
		<inputPhoto></inputPhoto>
	</newPost>
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
	<suggestedFriends>
		<source></source>
		<inputFriends>
			<friendID></friendID>
		</inputFriends>
		<userID></userID>
	</suggestedFriends>
</profile>
```

## XML Schema
- This does not include types for attributes of operations.
- Photos are represented as a URI (string)

``` xml
<?xml version="1.0"? encoding="ISO-8859-1" ?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">
	<xs:element name="name" type="xs:string"/>
	<xs:element name="photo" type="xs:string"/>
	<xs:element name="source" type="xs:string"/>
	<xs:element name="tag" type="xs:string"/>
	<xs:element name="time" type="xs:dateTime"/>
	<xs:element name="location" type="xs:string"/>
	<xs:element name="body" type="xs:string"/>
	<xs:element name="link" type="xs:string"/>
	<xs:element name="userID" type="xs:int"/>
	<xs:element name ="friendID" type="xs:int"/>

	<xs:element name="tags">
		<xs:complexType>
			<xs:list ref="tag"/>
		</xs:complexType>
	</xs:element>

	<xs:element name="post">
		<xs:complexType>
			<xs:sequence>
				<xs:element ref="time"/>
				<xs:element ref="location"/>
				<xs:list ref="tag"/>
				<xs:element ref="photo"/>
			</xs:sequence>
		</xs:complexType>
	</xs:element>

	<xs:element name="friends">
		<xs:complexType>
			<xs:sequence>
				<xs:list ref="friendID"/>
			</xs:sequence>
		</xs:complexType>
	</xs:element>

</xs:schema>
```

# 3: OLAP and Relational DB

## Relational Schema

Table: users

userID   | name        | email      | password     | location   |
---------|-------------|------------|--------------|------------|
PK       |             |            |              |            |

Table: friends

userID   | friendID |
---------|----------|
PK FK    | PK FK    |

Table: posts

userID   | body     | time   |
---------|----------|--------|
PK       |          |        |

- Functional Dependencies:
- Multivalued Dependencies:

## OLTP Queries

Create a new user and automatically make them friends with the original user (MySpace model):

``` SQL
INSERT INTO users (name, email, password, location)
VALUES ("Kanye West", "ye@thebomb.com", "yeezus", "Chicago, IL");

INSERT INTO friends VALUES (1, 2);
```
A user can add all people as freinds who live in Santa Barbara, CA and who have created a post in the last month:

``` SQL
INSERT INTO friends VALUES (1, 
	SELECT userID
	FROM users u JOIN posts p
		ON u.userID = p.userID
	WHERE u.location = "Santa Barbara, CA" AND p.time > "12/1/2013"
);
```
