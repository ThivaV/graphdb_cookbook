# neo4j

![neo4j](../../docs/img/neo4j.png)

- [neo4j](https://neo4j.com)
- [neo4j auraDB](https://neo4j.com/cloud/platform/aura-graph-database/)
- [neo4j docker image](https://hub.docker.com/_/neo4j)

## What are graph databases?

- Graphs are mathematical structures consisting of Edges and Vertices
- In a Property Graph, we refer to these as Nodes and Relationships

## What are graph databases?

- Graphs are useful when:
    - When the problem requires understanding the relationship between entities.
    - When the problem involves a hierarchy.
    - When the problem requires exploring relationships of varying or unknown depth.
    - When the problem requires evaluating routes or paths through a network.
- Common Usecases:
    - E-commerce platforms combine ratings, purchase history and browsing history to provide **real-time recommendations**.
    - The ICIJ used Neo4j to explore and understand the network of global companies and identify persons with significant control.
    - Many enterprises use Neo4j for planning, cost analysis, impact analysis and also to troubleshoot problems when a problem arises.

## What gives Neo4j its advantage?

- Neo4j is a native graph database designed specifically for graph traversal.
- Where Joins between tables are computed at read-time, this information is saved in a way that allows for quick pointer-chasing in memory
- Queries in Graph Databases are proportional to the amount of data touched during a query, not the size of data overall.

## What are Graph Databases?

- Graph Elements
    - **Node**: A node will commonly represent an individual record, for example, a `thing` or a `fact`.
    - **Label**: Nodes can have one or more labels. Labels provide a way to group nodes and also serve as a starting point for any database queries.
    - **Relationship**: A relationship connects two nodes. Each relationship has exactly one `start` and `end` node. A relationship will have a single a `type`.
    - **Type**: Relationships are identified by their `type`.
    - **Property**: Both nodes and relationships can contain properties. A property is a key/value pair.

- Modeling Rules
    - **Nodes typically represent things**. Examples of entities that could typically be represented as a node are: person, product, event, book or subway station.
    - **Relationships are typically verbs**. We could use a relationship to represent a personal or professional connection (Person knows Person, Person married to Person), to state a fact (Person lives in Location, Person owns Car, Person rated Movie), or even to represent a hierarchy (Parent parent of Child, Software depends on Library).
    - **Verbs can also be nodes**. A verb may be modeled as a node when one or more facts need to be associated with it. For example, you may want to group multiple product purchases through a single `(:Order)` node.

## Non-graph to Graph

- **Document Store to Graph**
    - Data is organized into collections containing documents
    - Collection group data, so their names can be used as labels
    - Graphs do not support nested documents, but nested documents can become the structure in the graph
- **Key-Value Store to Graph**
    - `Key-value` stores are great for highly performant lookups on huge amounts of data
    - When the values are interconnected, you have a graph
    - Traversing a graph in a key-value store may involve writing complex code

## Cypher

- What is Cypher?

    * Cypher is a query language designed for graphs.
    * Pattern's in Cypher
        * Nodes are represented by parentheses `()`
        * We use a colon to signify the label(s), for example `(:Person)`
        * Relationships between nodes are written with two dashes, for example `(:Person)--(:Movie)`
        * The direction of a relationship is indicated using a greater than or less than symbol `<` or `>` , for example `(:Person)-→(:Movie)`
        * The type of the relationship is written using the square brackets between the two dashes: `[` and `]`, for example `[:ACTED_IN]`
        * Properties drawn in a `speech bubble` are specified in a JSON like syntax.
            * Properties in Neo4j are `key/value` pairs, for example `{name: 'Tom Hanks'}`
    * `(m:Movie {title: 'Cloud Atlas'})<-[:ACTED_IN]-(p:Person)`
        * The two node types in this pattern are `Movie` and `Person`. The `Person` nodes have a directed `ACTED_IN` relationship to `Movie` nodes. The specific `Movie` node in this pattern is filtered by the title property with a value of Cloud Atlas. So this pattern represents all people in the graph who acted in the movie, Cloud Atlas.
    * Which Cypher clause do you use to read data from Neo4j?
        * `MATCH`
    * What Cypher keyword can you use to filter the nodes retrieved during the execution of a `MATCH` clause?
        * `WHERE`
        * We want to know what the tagline is for the movie, The Matrix: `MATCH (m:Movie) WHERE m.title = 'The Matrix' RETURN m.tagline`
        * Find the year that Kevin Bacon was born: `MATCH (p:Person) WHERE p.name = 'Kevin Bacon' RETURN p.born`

- Cypher patterns:

    * `MATCH (p:Person {name: 'Tom Hanks'})-[:ACTED_IN]->()`
        * We can extend the pattern in the `MATCH` clause to traverse through all relationships with a type of `ACTED_IN` to any node. Our domain model shows that the `ACTED_IN` relationship goes in an outgoing direction from the Person node so we can add the direction in our pattern. We often refer to this as a `traversal`.
    * `MATCH (p:Person {name: 'Tom Hanks'})-[:ACTED_IN]->(m) RETURN m.title`
        * Our data model dictates that the node at the other end of that relationship will be `Movie` node, so we don’t necessarily need to specify the `:Movie` label in the node - instead we will use the variable `m`.
    * `MATCH (p:Person {name: 'Tom Hanks'})-[:ACTED_IN]->(m:Movie) RETURN m.title`
        * This code returns the titles of all movies that Tom Hanks acted in.
        If our graph had different labels, for example Television and Movie nodes this query would have returned all Television and Movie nodes that Tom Hanks acted in. That is, if we had multiple types of nodes at the end of the ACTED_IN relationships in our graph, we could make sure that we only return movies.
    * `MATCH (m:Movie)<-[:ACTED_IN]-(p:Person) RETURN m.title, p.name`
        * Started with the `Movie` node and list those who acted in the movie
    * Which `MATCH` clauses will return the names of the directors of the movie, The Matrix?
        * `MATCH (m:Movie {title: 'The Matrix'})<-[:DIRECTED]-(p:Person) RETURN p.name`
        * `MATCH (m:Movie {title: 'The Matrix'})<-[:DIRECTED]-(p) RETURN p.name`
    * Example Cypher queries:

        - Create <u>Person</u> `Nodes`:

        ```
        CREATE(Peter:Person{name:'Peter Kim', born:1998, gender:'female'});
        CREATE(Robert:Person{name:'Robert Junior', born:1956, gender:'male'});
        CREATE(Iron:Person{name:'Iron Junior', born:1986, gender:'female'});
        ```

        - Create <u>Movie</u> `Nodes`:

        ```
        CREATE(Iron:Movie{title:'The Iron Man', release:2008});
        CREATE(Matrix:Movie{title:'The Matrix', release:1999});
        ```

        - Create `Relationships`:

        ```
        MATCH (Peter:Person {name: "Peter Kim"})
        MATCH (Matrix:Movie {title: "The Matrix"})
        CREATE (Peter)-[:ACTED_IN]->(Matrix);
        ```

        - Retrieve <u>Movies</u>:

        ```
        MATCH (:Movie) RETURN n LIMIT 25;
        ```

- Writing data to Neo4j:

    - **Create nodes**:
        * We use the `MERGE` keyword to create a pattern in the database: `MERGE (p:Person {name: 'Michael Caine'}) RETURN p`
            * **Note:** If the run the same cypher query multiple times it won't create any additional nodes, it because `MERGE` looks for the node in the grpah and it doesn't exist it creates it.
        * Using `CREATE` instead of `MERGE` to create nodes:
            * Cypher has a `CREATE` clause you can use for creating nodes. The benefit of using `CREATE` is that it does not look up the primary key before adding the node. You can use `CREATE` if you are sure your data is clean and you want greater speed during import. We use `MERGE` in this training because it eliminates duplication of nodes.
            * We can also use `CREATE` but it will create multiple nodes, if the same query runs multiple times. So, it's advisable to use `MERGE` instead of `CREATE` to create nodes in Neo4j.
        * Executing multiple Cypher clauses, We can also chain multiple `MERGE` clauses together within a single Cypher code block.
            ```
            MERGE (p:Person {name: 'Katie Holmes'}) 
            MERGE (m:Movie {title: 'The Dark Knight'}) 
            RETURN p, m
            ```
    - **Create Relationships**:
        * Just like you can use MERGE to create nodes in the graph, you use MERGE to create relationships between two nodes. First you must have references to the two nodes you will be creating the relationship for. When you create a relationship between two nodes, it must have:
            * `Type`
            * `Direction`
            ```
            MATCH (p:Person {name: 'Michael Caine'})
            MATCH (m:Movie {title: 'The Dark Knight'})
            MERGE (p)-[:ACTED_IN]->(m)
            ```
        * We can confirm that this relationship exists as follows:
        ```
        MATCH (p:Person {name: 'Michael Caine'})-[:ACTED_IN]-(m:Movie {title: 'The Dark Knight'})
        RETURN p, m
        ```
        * By default, in Neo4j Browser, the visualization connects nodes that have relationships between them.
        Notice also that you need not specify direction in the MATCH pattern since the query engine will look for all nodes that are connected, regardless of the direction of the relationship.        
        For example, if we specified this relationship pattern:
        ```
        MATCH (p:Person {name: 'Michael Caine'})<-[:ACTED_IN]-(m:Movie {title: 'The Dark Knight'})
        RETURN p, m
        ```
        This query returns no nodes since there are no nodes with the `ACTED_IN` relationship to Person nodes in the graph.
        * We can also chain multiple `MERGE` clauses together within a single Cypher code block:
        ```
        MERGE (p:Person {name: 'Chadwick Boseman'})
        MERGE (m:Movie {title: 'Black Panther'})
        MERGE (p)-[:ACTED_IN]-(m)
        ```

        **Note** that in this `MERGE` clause where we create the relationships, we did not specify the direction of the relationship. By default, if you do not specify the direction when you create the relationship, it will always be assumed **left-to-right**.
        * What `MERGE` does is create the node or relationship if it does not exist in the graph. You can execute this Cypher code multiple times and it will not create any new nodes or relationships.
        ```
        MERGE (p:Person {name: 'Emily Blunt'})-[:ACTED_IN]->(m:Movie {title: 'A Quiet Place'})
        RETURN p, m
        ```
        * Example: You will do a `MATCH` to find the person. Then you will do a MERGE to create the movie node. Remember that our other Movie nodes contain a property called title. Then you will do a MERGE to create the relationship.
            - Find the Person node for Daniel Kaluuya.
            - Create the Movie node, Get Out.
            - Add the ACTED_IN relationship between Daniel Kaluuya and the movie, Get Out.
            ```
            MATCH (p:Person {name: 'Daniel Kaluuya'})
            MERGE (m:Movie {title: 'Get Out'})
            MERGE (p)-[:ACTED_IN]->(m)
            RETURN p, m
            ```
- Update Properties in Neo4j:

    * We can `add`, `modify`, or `remove` properties from nodes and relationships.
    * `ADD` properties:
        * Adding properties for a `node` or `relationship`: There are `two ways` that you can set a property for a node or relationship.
            1. Inline as part of the `MERGE` clause:
                - You have already seen how to create the primary key property for a node. You can also set a property for a relationship inline as follows:
                ```
                MATCH (p:Person {name: 'Michael Caine'})
                MERGE (m:Movie {title: 'Batman Begins'})
                MERGE (p)-[:ACTED_IN {roles: ['Alfred Penny']}]->(m)
                RETURN p,m
                ```
                - In this code, the actor, Michael Caine exists but the movie, Batman Begins does not. We find the Person node and we create the Movie node. Then, we create the `ACTED_IN` relationship between the **Michael Caine** node and the newly-created Batman Begins node. And we set the roles property for this relationship to an array of values - containing one value, Alfred Penny. Notice that for inline property setting, we use the JSON-style of adding the property key/value pairs in braces `{ .. }`, just like we did when we specified the property for the node.
            2. Using the `SET` keyword for a reference to a node or relationship:
                - We also have the option to use the `SET` keyword for setting a property value. In the context of particular MERGE or `MATCH` clause where you have defined a variable to reference the node or relationship, you can set property values.
                ```
                MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
                WHERE p.name = 'Michael Caine' AND m.title = 'The Dark Knight'
                SET r.roles = ['Alfred Penny']
                RETURN p, r, m
                ```
        * Setting multiple properties:
            - If you need to set multiple properties, you separate them with a comma `(,)`. For example:
            ```
            MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
            WHERE p.name = 'Michael Caine' AND m.title = 'The Dark Knight'
            SET r.roles = ['Alfred Penny'], m.released = 2008
            RETURN p, r, m
            ```
    * `UPDATE` properties:
        * If we have a reference to a node or relationship, you can also use `SET` to modify the property. 
        * For example, if we wanted to **modify** Michael Caine’s role to be something different, we could do the following:
        ```
        MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
        WHERE p.name = 'Michael Caine' AND m.title = 'The Dark Knight'
        SET r.roles = ['Mr. Alfred Penny']
        RETURN p, r, m
        ```
    * `REMOVE` proerties:
        * You can remove or delete a property from a node or relationship by using the `REMOVE` keyword, or setting the property to `null`.
        * Here we remove the roles property of this relationship:
        ```
        MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
        WHERE p.name = 'Michael Caine' AND m.title = 'The Dark Knight'
        REMOVE r.roles
        RETURN p, r, m
        ```
        * Here we remove the born property from an actor:
        ```
        MATCH (p:Person)
        WHERE p.name = 'Gene Hackman'
        SET p.born = null
        RETURN p
        ```
        * **Note**: We should never remove the property that is used as the primary key for a node.
    * Example:
        * we created a new Movie node for the movie Get Out. However, we forgot to add a a tagline or release year.
        * If you execute the code in the sandbox window, it will return the Movie node, but the tagline and released properties have no values. That is, these properties have not been added to the node.
        * Write the Cypher code to find this Movie node and add the tagline and released properties for the node with the values below.
            - `tagline`: Gripping, scary, witty and timely!
            - `released`: 2017
        - Retrieve the data:
        ```
        MATCH (m:Movie {title: 'Get Out'})
        RETURN m.title, m.tagline, m.released
        ```
        - Add properties:
        ```
        MATCH (m:Movie)
        WHERE m.title='Get Out'
        SET m.tagline='Gripping, scary, witty and timely!', m.released=2017
        ```

- MERGE Processing:

    * We have learned that you can use `MERGE` to create nodes and relationships in the graph. `MERGE` operations work by first trying to find a pattern in the graph. If the pattern is found then the data already exists and is not created. If the pattern is not found, then the data can be created.
    * Customizing `MERGE` behavior:
        - We can also specify behavior at runtime that enables you to set properties when the node is created or when the node is found. We can use the `ON CREATE SET` or `ON MATCH SET` conditions, or the `SET` keywords to set any additional properties.
        - Example: If the Person node for McKenna Grace does not exist, it is created and the createdAt property is set. If the node is found, then the updatedAt property is set. In both cases, the born property is set.
            - Run this Cypher code at least 2 times to observe what properties are set. We can see the properties in table view.
            ```
            // Find or create a person with this name
            MERGE (p:Person {name: 'McKenna Grace'})

            // Only set the `createdAt` property if the node is created during this query
            ON CREATE SET p.createdAt = datetime()

            // Only set the `updatedAt` property if the node was created previously
            ON MATCH SET p.updatedAt = datetime()

            // Set the `born` property regardless
            SET p.born = 2006

            RETURN p
            ```
        - If We want to set multiple properties for an `ON CREATE SET` or `ON MATCH SET` clause, you separate them by commas. For example:
        ```
        ON CREATE SET m.released = 2020, m.tagline = `A great ride!'
        ```
    * Merging with relationships:
        - We can use `MERGE` to create nodes or relationships:
        ```
        // Find or create a person with this name
        MERGE (p:Person {name: 'Michael Caine'})

        // Find or create a movie with this title
        MERGE (m:Movie {title: 'The Cider House Rules'})

        // Find or create a relationship between the two nodes
        MERGE (p)-[:ACTED_IN]->(m)
        ```
        - Another way your can create these nodes and relationship is as follows:
        ```
        MERGE (p:Person {name: 'Michael Caine'})-[:ACTED_IN]->(m:Movie {title: 'The Cider House Rules'})
        RETURN p, m
        ```
        - Here is what happens in the query processor:
            1. Neo4j will attempt to find a Person node with the name Michael Caine.
            2. If it does not exist, it creates the node.
            3. Then, it will attempt to expand the `ACTED_IN` relationships in the graph for this node.
            4. If there are any `ACTED_IN` relationships from this node, it looks for a Movie with the title 'The Cider House Rules'.
            5. If there is no node for the Movie, it creates the node.
            6. If there is no relationship between the two nodes, it then creates the `ACTED_IN` relationship between them.
        - Example:
            - Suppose we do not know if we have the movie Rocketman in our graph. If it is not in the graph, we want to set the createdAt property with the value of `datetime()`. The `datetime()` function returns the current date and time. If it is in the graph, we want to set the matchedAt property with the value of `datetime()`. In either case, we want to set the updatedAt property with the value of `datetime()`.
                - If the node already exists (`ON MATCH SET` clause):
                    * Set the `matchedAt` property for the node referenced by m to `datetime()`.
                - If the node does not exist (`ON CREATE SET` clause):
                    * Set the `createdAt` property to `datetime()`.
                - For either case:
                    * Set the `updatedAt` property to `datetime()`.
            - Execute your code twice to ensure that the the `MERGE` processing occurs. That is, the newly created node will have a createdAt property and the updated node will have a matchedAt property. In both cases, the node will have the updatedAt property set.

            ```
            MERGE (m:Movie {title: 'Rocketman'})

            // perform the ON MATCH setting of the matchedAt property
            ON MATCH SET m.matchedAt=datetime()

            // perform the ON CREATE setting of the createdAt property
            ON CREATE SET m.createdAt=datetime()

            // set the updatedAt property
            SET m.updatedAt=datetime()

            RETURN m
            ```
- Delete data in Neo4j:

    * In a Neo4j database we can delete:
        - nodes
        - relationships
        - properties
        - labels
    * To delete any data in the database, you must first retrieve it, then you can delete it.
    * **Deleting a node**:
        - Suppose we have created a Person node for Jane Doe: `MERGE (p:Person {name: 'Jane Doe'})`
        - We delete this node as follows where you first retrieve the node. Then with a reference to the node you can delete it.
        ```
        MATCH (p:Person)
        WHERE p.name = 'Jane Doe'
        DELETE p
        ```
    * **Deleting a relationship**:
        - Suppose we had our Jane Doe node again where she was added as an actor in the movie, The Matrix. Run this code to create the node and the relationship.
        ```
        MATCH (m:Movie {title: 'The Matrix'})
        MERGE (p:Person {name: 'Jane Doe'})
        MERGE (p)-[:ACTED_IN]->(m)
        RETURN p, m
        ```
        - To leave the Jane Doe node in the graph, but **remove the relationship** we retrieve the relationship and delete it.
        ```
        MATCH (p:Person {name: 'Jane Doe'})-[r:ACTED_IN]->(m:Movie {title: 'The Matrix'})
        DELETE r
        RETURN p, m
        ```
        - If we attempt to delete the Jane Doe node, we will receive an error because it has relationships in the graph.
        ```
        MATCH (p:Person {name: 'Jane Doe'})
        DELETE p
        ```
    * **Deleting a node and its relationships**:
        - Neo4j provides a feature where you cannot delete a node if it has incoming or outgoing relationships. This prevents the graph from having orphaned relationships.
        - So, if We want to delete a node first We want to detach all its relationships:
        ```
        MATCH (p:Person {name: 'Jane Doe'})
        DETACH DELETE p
        ```
        - We can also delete all nodes and relationships in a database with this code:
        ```
        MATCH (n)
        DETACH DELETE n
        ```
    * **Deleting labels**:
        - A best practice is to have at least one label for a node.
        - Run this Cypher code to create this Person node in the graph:
        ```
        MERGE (p:Person {name: 'Jane Doe'})
        RETURN p
        ```
        - Next, run this code to add a new label to this node:
        ```
        MATCH (p:Person {name: 'Jane Doe'})
        SET p:Developer
        RETURN p
        ```
        - To remove the newly-added label, Developer, you use the `REMOVE` clause. Run this code:
        ```
        MATCH (p:Person {name: 'Jane Doe'})
        REMOVE p:Developer
        RETURN p
        ```
        The Jane Doe node has two labels, `Person` and `Developer`. You can use a `MATCH` to find that node. Note that you could have specified `MATCH (p:Developer {name: 'Jane Doe'})` or `MATCH (p:Person:Developer {name: 'Jane Doe'})` to find the same node. Once we have a reference to that node, we can remove the label with the `REMOVE` clause.
        - Delete the Jane Doe node by running this code:
        ```
        MATCH (p:Person {name: 'Jane Doe'})
        DETACH DELETE p
        ```
        **Note**: that `DELETE p` would also work in this case since we have not created any relationships.

- What labels exist in the graph?
    * This code returns all node labels defined in the graph.
    ```
    CALL db.labels()
    ```

## Jupyter notebooks:

- [neo4j auradb langchain groq notebook](./neo4j_auradb_langchain_groq.ipynb)

## References:

- [Colab Init neo4j auradb](https://colab.research.google.com/drive/1-jUyQOOVcg6H2Ohki0NSgcYp176ZAwjX?usp=sharing)
- [Complete Session On Knowledge Graph and GraphDb With Langchain](https://www.youtube.com/watch?v=hsOJhs3_UCM)
- [There are many resources available to you for learning more about Neo4j](https://neo4j.com/developer/resources/)
- [Neo4j Community Site where you can ask or answer questions about Neo4j and discuss with other users](https://community.neo4j.com)
- [Neo4j documentation](https://neo4j.com/docs/)
- [Neo4j Sandboxes for experimenting with graphs](https://sandbox.neo4j.com/?ref=graph-academy)
- [Videos on the Neo4j YouTube channel](https://www.youtube.com/channel/UCvze3hU6OZBkB1vkhH2lH9Q)
- [Become a Neo4j certified developer](https://graphacademy.neo4j.com/categories/certification/)
- [GitHub repository](https://github.com/neo4j-contrib)
- [Neo4j events all over the world](https://neo4j.com/events/world/all/)
- [Graph Gists for learning more use cases for Neo4j](https://neo4j.com/graphgists/)
- [Attend a Neo4j meetup](https://www.meetup.com/topics/neo4j/)
- [View questions/answers raised about Neo4j](https://stackoverflow.com/tags/neo4j/hot)
