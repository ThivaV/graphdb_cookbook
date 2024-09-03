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

- Sample Cypher queries:

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