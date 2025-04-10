1. Basic Node Retrieval
Task: List all movies released in the year 1999.
Goal: Practice filtering node properties.

    MATCH (m:Movie)  
    WHERE m.releaseYear = 1999 
    RETURN m;  

2. Find a Person by Name
Task: Retrieve details for the person named Keanu Reeves.
Goal: Query nodes by exact property match.

    MATCH (p:Person)
    WHERE p.name = "Keanu Reeves"
    RETURN p;

3. Find Actors in a Movie
Task: List all actors who acted in The Matrix.
Goal: Traverse relationships from a movie to actors.

    MATCH (p:Person)-[r:ACTED_IN]->(m:Movie {title: "The Matrix"})
    RETURN p;

4. Directors of a Movie
Task: Find the director(s) of The Matrix Revolutions.
Goal: Filter relationships by type (DIRECTED).

    MATCH (a:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(d:Person)  
    WHERE m.title = "The Matrix Revolutions"  
    RETURN distinct d.name;  

5. Movies by Genre and Year
Task: Find all Action movies released after 2000.
Goal: Combine relationship traversal and property filtering.

    MATCH (m:Movie)-[:IN_GENRE]->(g:Genre {name: "Action"})
    WHERE m.releaseYear > 2000
    RETURN m.title AS Title, m.releaseYear AS Year, g.name AS Genre
    ORDER BY Year;

6. Co-Actors of an Actor
Task: Find all actors who worked with Tom Hanks in any movie.
Goal: Use variable-length patterns and deduplication (DISTINCT).

    MATCH (hanks:Person {name: "Tom Hanks"})-[:ACTED_IN]->(movie)<-[:ACTED_IN]-(coactor:Person)
    RETURN DISTINCT coactor.name AS Actor

7. Movies with Multiple Relationships
Task: Find all people who either acted in, directed, or wrote Cloud Atlas.
Goal: Filter relationship types dynamically.

    MATCH (person:Person)-[role]->(movie:Movie {title: "Cloud Atlas"})
    WHERE type(role) IN ["ACTED_IN", "DIRECTED", "WROTE"]
    RETURN DISTINCT person.name AS Name, type(role) AS Role;

    MATCH (p:Person)-[rel:ACTED_IN|DIRECTED|WROTE]->(m:Movie {title: "Cloud Atlas"})
    RETURN DISTINCT p.name AS name, type(rel) AS role;

8. Shortest Path Between Actors
Task: Find the shortest path between Tom Hanks and Meg Ryan through co-acting.
Goal: Use shortestPath and variable-length relationships.

    MATCH (a1:Person {name: "Tom Hanks"}), (a2:Person {name: "Meg Ryan"})
    MATCH path = shortestPath((a1)-[:ACTED_IN*]-(a2))
    RETURN [n IN nodes(path) | CASE WHEN n:Person THEN n.name ELSE n.title END] AS Path;


9. Aggregation with HAVING
Task: List actors who have acted in at least 3 movies.
Goal: Use WITH and HAVING-like filtering.

    MATCH (actor:Person)-[:ACTED_IN]->(movie:Movie)
    WITH actor, COUNT(movie) AS movieCount
    WHERE movieCount >= 3
    RETURN actor.name AS Actor, movieCount AS MovieCount
    ORDER BY MovieCount DESC


10. Complex Graph Pattern (Advanced)
Task: Find all directors who also acted in their own movies.
Goal: Combine multiple relationships on the same node.

    MATCH (person:Person)-[:DIRECTED]->(movie:Movie)<-[:ACTED_IN]-(person)
    RETURN DISTINCT person.name AS Name, movie.title AS Movie


