# neo4j

![neo4j](../../docs/img/neo4j.png)

- [neo4j](https://neo4j.com)
- [neo4j auraDB](https://neo4j.com/cloud/platform/aura-graph-database/)
- [neo4j docker image](https://hub.docker.com/_/neo4j)

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
- Jupyter notebooks:
    - [neo4j auradb langchain groq notebook](./neo4j_auradb_langchain_groq.ipynb)

- References:
    - [Colab Init neo4j auradb](https://colab.research.google.com/drive/1-jUyQOOVcg6H2Ohki0NSgcYp176ZAwjX?usp=sharing)
    - [Complete Session On Knowledge Graph and GraphDb With Langchain](https://www.youtube.com/watch?v=hsOJhs3_UCM)