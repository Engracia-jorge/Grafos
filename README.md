# Desafio 1 Grafos
# 🎬 Neo4j - Filmes, Séries e Usuários

Este projeto demonstra como modelar um grafo no **Neo4j** com entidades relacionadas a usuários, filmes, séries, gêneros, atores e diretores.  
O objetivo é explorar relacionamentos como **assistiu**, **atuou**, **pertence** e **dirigiu**.

---

## 📌 Estrutura do Grafo

### Entidades
- **User** → Representa usuários (ex: Ana, Carlos)
- **Movie** → Representa filmes (ex: Matrix, Inception)
- **Serie** → Representa séries (ex: Breaking Bad)
- **Genero** → Representa gêneros (ex: Ficção Científica, Drama, Ação)
- **Actor** → Representa atores (ex: Keanu Reeves, Leonardo DiCaprio)
- **Director** → Representa diretores (ex: Lana Wachowski, Christopher Nolan)

### Relacionamentos
- `(User)-[:ASSISTIU]->(Movie|Serie)`
- `(Actor)-[:ATUOU]->(Movie|Serie)`
- `(Movie|Serie)-[:PERTENCE]->(Genero)`
- `(Director)-[:DIRIGIU]->(Movie|Serie)`

---
## Diagrama do grafo

<img width="753" height="336" alt="Image" src="https://github.com/user-attachments/assets/f9fbbc83-a649-45d1-9432-b9563546b31c" />


---
## 🚀 Script Cypher

O script abaixo cria até **10 elementos** e popula o banco com exemplos de filmes, séries, atores e diretores:

```cypher
// Usuários
CREATE (:User {name: "Ana"});
CREATE (:User {name: "Carlos"});

// Filmes/Séries
CREATE (:Movie {title: "Matrix"});
CREATE (:Movie {title: "Inception"});
CREATE (:Serie {title: "Breaking Bad"});

// Gêneros
CREATE (:Genero {name: "Ficção Científica"});
CREATE (:Genero {name: "Drama"});
CREATE (:Genero {name: "Ação"});

// Atores
CREATE (:Actor {name: "Keanu Reeves"});
CREATE (:Actor {name: "Leonardo DiCaprio"});
CREATE (:Actor {name: "Bryan Cranston"});

// Diretores
CREATE (:Director {name: "Lana Wachowski"});
CREATE (:Director {name: "Christopher Nolan"});
CREATE (:Director {name: "Vince Gilligan"});

// Relacionamentos
MATCH (u:User {name:"Ana"}), (m:Movie {title:"Matrix"})
CREATE (u)-[:ASSISTIU]->(m);

MATCH (u:User {name:"Carlos"}), (s:Serie {title:"Breaking Bad"})
CREATE (u)-[:ASSISTIU]->(s);

MATCH (a:Actor {name:"Keanu Reeves"}), (m:Movie {title:"Matrix"})
CREATE (a)-[:ATUOU]->(m);

MATCH (a:Actor {name:"Leonardo DiCaprio"}), (m:Movie {title:"Inception"})
CREATE (a)-[:ATUOU]->(m);

MATCH (a:Actor {name:"Bryan Cranston"}), (s:Serie {title:"Breaking Bad"})
CREATE (a)-[:ATUOU]->(s);

MATCH (m:Movie {title:"Matrix"}), (g:Genero {name:"Ficção Científica"})
CREATE (m)-[:PERTENCE]->(g);

MATCH (m:Movie {title:"Inception"}), (g:Genero {name:"Ação"})
CREATE (m)-[:PERTENCE]->(g);

MATCH (s:Serie {title:"Breaking Bad"}), (g:Genero {name:"Drama"})
CREATE (s)-[:PERTENCE]->(g);

MATCH (d:Director {name:"Lana Wachowski"}), (m:Movie {title:"Matrix"})
CREATE (d)-[:DIRIGIU]->(m);

MATCH (d:Director {name:"Christopher Nolan"}), (m:Movie {title:"Inception"})
CREATE (d)-[:DIRIGIU]->(m);

MATCH (d:Director {name:"Vince Gilligan"}), (s:Serie {title:"Breaking Bad"})
CREATE (d)-[:DIRIGIU]->(s);
