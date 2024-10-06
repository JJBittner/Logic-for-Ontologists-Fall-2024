**The SPARQL Library of Common Core Ontologies**

Project 2 - J Bittner

The goal of this project is to develop a suite of SPARQL queries that will serve as quality control (QC) checks against the [Common Core Ontologies](https://github.com/CommonCoreOntology/CommonCoreOntologies) suite. These queries will be designed to identify and flag potential issues, ensuring the ontology's integrity, consistency, and adherence to predefined standards.

**Assignment Details**

Your task is to construct SPARQL queries to be included in the [CCO QC workflow](https://github.com/CommonCoreOntology/CommonCoreOntologies/actions). Ideally, your queries will be added to the CCO repository [here](https://github.com/CommonCoreOntology/CommonCoreOntologies/tree/develop/.github/deployment/sparql). 

Your queries will be ranked in terms of difficulty. The lowest - 8 - indicates a rather easy query, while the highest - 1 - will indicate a very sophisticated query. 

For our purposes, the more sophisticated queries will be worth more points than less sophisticated, and you are required to submit enough queries to acquire 100 points according to the following point system: 


  | **Query Sophistication** | **Points** |
  | ------------------------ | ---------- |
  |       1                  |      35    |
  |       2                  |      25    |
  |       3                  |      20    |
  |       4                  |      10    |
  |       5                  |       5    |
  |       6                  |       3    |
  |       7                  |       2    |
  |       8                  |       0    |

You're probably thinking, "why would I submit a level 8 if they're not worth any points?" Great question. Because everyone has to submit at least one level 8. Otherwise, you're permitted to submit in any distribution you choose. For example, you might submit 2 queries for level one (70 points), one for level 3 (20 points), one for 4 (10 points), and one for kata 8 (0 points but required). 

It is your responsibility and the responsibility of your peers reviewing your submission in PR to determine whether your submission is ranked appropriately. In the event that consensus is reached that your query is ranked inappropriately, you must work with your peers to revise the submission so that it is either more or less challenging, accordingly. You are not permitted to submit new queries with different strengths after PRs are open, but must instead revise your PRs. So, think hard about how challenging your submission is. 

**Template**

The SPARQL queries should have the template: 
Title
    (descriptive title of the query)
Constraint Description: 
    (description of the query functionality)
Severity:
    (select "Warning" or "Error")

Your query should end with a BIND clause and an associated ?error in the SELECT. For example: 

  - BIND (concat("WARNING: The following ontology elements have the same rdfs:label ", str(?element), " and ", str(?element2)) AS ?error)

**Guidance**

A few tips for developing effective SPARQL queries for the Common Core Ontologies (CCO):
  - Review the [existing SPARQL queries](https://github.com/CommonCoreOntology/CommonCoreOntologies/tree/develop/.github/deployment/sparql) so as not to duplicate work
  - Review [documentation and design patterns](https://github.com/CommonCoreOntology/CommonCoreOntologies/tree/develop/documentation) to understand stucture of CCO
  - Understand common issues in ontologies; [explore the OOPS!](https://oa.upm.es/35873/1/INVE_MEM_2014_192872.pdf) list here for inspiration
  - Observe annotation conventions, e.g. use of labels, comments, etc. must be present and accurate

When creating queries, start with simple quality control checks and build complexity through practice. Feel free to leverage generative AI for this project. Also, feel free to collaborate with peers. 

Be sure to test your queries. You may do this in Protege or in the [SPARQL playground](https://atomgraph.github.io/SPARQL-Playground/). 

Parent Count and Ancestor Count: (Principle of Single Inheritance) All non-root terms will have exactly one is_a parent and thus be connected by exactly one chain of is_a relations to the corresponding root.



# SPARQL queries for the Common Core Ontologies (CCO) suite #

## Query 1: Check for Missing rdfs:label ##

**Title**: Classes with Missing rdfs:label

**Constraint Description**: This query identifies classes that do not have an rdfs:label.

**Severity**: Error

```sparql 

PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
SELECT ?class ?error
WHERE {
  ?class a rdfs:Class .
  FILTER NOT EXISTS { ?class rdfs:label ?label }
  BIND (concat("ERROR: The class ", str(?class), " is missing an rdfs:label.") AS ?error)
}

```
```sparql 

PREFIX foaf: <http://xmlns.com/foaf/0.1/>
SELECT ?person ?email
WHERE {
  ?person foaf:mbox ?email .
  FILTER regex(?email, "^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}$")
}

```

**Level**: 8 (0 points)

## Query 2: Check for Multiple rdfs:labels ##

**Title**: Classes with Multiple rdfs:labels

Constraint Description: This query identifies classes that have more than one rdfs:label.

**Severity**: Warning

```sparql 

PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
SELECT ?class (COUNT(?label) AS ?labelCount) ?error
WHERE {
  ?class a rdfs:Class .
  ?class rdfs:label ?label .
  BIND (concat("WARNING: The class ", str(?class), " has multiple rdfs:labels.") AS ?error)
}
GROUP BY ?class ?error
HAVING (COUNT(?label) > 1)

```

**Level**: 7 (2 points)

## Query 3: Check for Missing skos:definition ##

**Title**: Classes with Missing skos:definition

Constraint Description: This query identifies classes that do not have a skos:definition.

**Severity**: Error

```sparql 

PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
SELECT ?class ?error
WHERE {
  ?class a rdfs:Class .
  FILTER NOT EXISTS { ?class skos:definition ?definition }
  BIND (concat("ERROR: The class ", str(?class), " is missing a skos:definition.") AS ?error)
}

```

Sophistication Level: 6 (3 points)

## Query 4: Check for Deprecated Classes ##

**Title**: Deprecated Classes

**Constraint Description**: This query identifies classes that are marked as deprecated using owl:deprecated.

**Severity**: Warning

```sparql 

PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
SELECT ?class ?error
WHERE {
  ?class a rdfs:Class .
  ?class owl:deprecated "true"^^xsd:boolean .
  BIND (concat("WARNING: The class ", str(?class), " is marked as deprecated.") AS ?error)
}

```

**Level**: 5 (5 points)

## Query 5: Check for Single Inheritance ##

**Title**: Classes with Multiple Parents

**Constraint Description**: This query identifies classes that have more than one rdfs:subClassOf parent, ensuring single inheritance.

**Severity**: Error

```sparql 

PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
SELECT ?class ?error
WHERE {
  ?class a rdfs:Class .
  ?class owl:deprecated "true"^^xsd:boolean .
  BIND (concat("WARNING: The class ", str(?class), " is marked as deprecated.") AS ?error)
}

```

**Level**: 3 (20 points)

## Query 6: Check for Properties with No Domain ##

**Title**: Properties with No Domain

**Constraint Description**: This query identifies properties that do not have a specified domain.

**Severity**: Warning

```sparql 

PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
SELECT ?property ?error
WHERE {
  ?property a rdf:Property .
  FILTER NOT EXISTS { ?property rdfs:domain ?domain }
  BIND (concat("WARNING: The property ", str(?property), " does not have a specified domain.") AS ?error)
}

```

**Level**: 2 (25 points)

## Query 7: Check for Unconnected Classes ##

**Title**: Unconnected Classes

**Constraint Description**: This query identifies classes that are not connected to the root owl:Thing, ensuring all classes are part of a single hierarchy.

**Severity**: Error

```sparql 

PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>

SELECT ?class ?error
WHERE {
  ?class a rdfs:Class .
  FILTER NOT EXISTS {
    ?class rdfs:subClassOf+ owl:Thing .
  }
  BIND (concat("ERROR: The class ", str(?class), " is not connected to the root owl:Thing.") AS ?error)
}

```

**Level**: 1 (35 points)

## Query 8: Check for Missing owl:equivalentClass ##

**Title**: Classes with Missing owl:equivalentClass

**Constraint Description**: This query identifies classes that do not have an owl:equivalentClass, ensuring equivalence axioms are declared where necessary.

**Severity** : Warning

```sparql 

PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
SELECT ?class ?error
WHERE {
  ?class a rdfs:Class .
  FILTER NOT EXISTS { ?class owl:equivalentClass ?equivalentClass }
  BIND (concat("WARNING: The class ", str(?class), " is missing an owl:equivalentClass.") AS ?error)
}

```
```sparql 

PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
SELECT ?class ?error
WHERE {
  ?class a rdfs:Class .
  FILTER NOT EXISTS { ?class owl:equivalentClass ?equivalentClass }
  FILTER regex(str(?class), "^http://example\\.org/classes/")
  BIND (concat("WARNING: The class ", str(?class), " is missing an owl:equivalentClass.") AS ?error)
}

```

**Level**: 4 (10 points)



### Summary of Points:
- Query 1: 0 points
- Query 2: 2 points
- Query 3: 3 points
- Query 4: 5 points
- Query 5: 20 points
- Query 6: 25 points
- Query 7: 35 points
- Query 8: 10 points

**Total Points**: 100 points

These queries ensure comprehensive quality control for the Common Core Ontologies suite, covering various aspects such as missing labels, cyclic inheritance, and class connectivity.
