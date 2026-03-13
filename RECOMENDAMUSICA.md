## Desafio 2 – Algoritmo de Recomendação de Música com Grafos


# 🎯 Objetivo

O sistema tem como finalidade recomendar músicas para usuários com base em suas interações (escutar, curtir, seguir)
e nas conexões entre músicas, artistas e gêneros. A escolha por grafos se deve à capacidade de representar relações
complexas e explorar caminhos entre entidades de forma eficiente.

---

## 🧩 Modelagem do Grafo

### Entidades (nós do grafo)

**Usuário**

**Música**

**Artista**

**Gênero*+


### Interações (arestas com propriedades)

**Usuário → Música**

-`ESCUTAR (propriedade: frequência, duração, timestamp)`

-`CURTIR (propriedade: intensidade, data)`

**Usuário → Artista**

-`SEGUIR (propriedade: desde quando segue, nível de engajamento)`

**Música → Artista**

-`CRIADA_POR (propriedade: colaboração, participação)`

**Música → Gênero**

-`PERTENCE_A (propriedade: peso de classificação, subgênero)`

---

## Exemplo em Cypher (Neo4j)
```
cypher
// Criar nós
CREATE (:Usuario {id:'user_1', nome:'Ana'});
CREATE (:Musica {id:'song_A', titulo:'Rock Song'});
CREATE (:Artista {id:'artist_X', nome:'Banda X'});
CREATE (:Genero {id:'rock'});

// Criar interações
MATCH (u:Usuario {id:'user_1'}), (m:Musica {id:'song_A'})
CREATE (u)-[:ESCUTAR {vezes:10, ultima:'2026-03-08'}]->(m);

MATCH (u:Usuario {id:'user_1'}), (m:Musica {id:'song_A'})
CREATE (u)-[:CURTIR {data:'2026-03-01'}]->(m);

MATCH (u:Usuario {id:'user_1'}), (a:Artista {id:'artist_X'})
CREATE (u)-[:SEGUIR {desde:'2025-12-15'}]->(a);

MATCH (m:Musica {id:'song_A'}), (a:Artista {id:'artist_X'})
CREATE (m)-[:CRIADA_POR {colaboracao:false}]->(a);

MATCH (m:Musica {id:'song_A'}), (g:Genero {id:'rock'})
CREATE (m)-[:PERTENCE_A {peso:1.0}]->(g);
```
---
## ⚙️ Funcionamento do Algoritmo

### Coleta de dados

Logs de escuta, curtidas, seguidores e metadados de músicas.


### Diagrama do grafo

Inserção dos nós e arestas no banco de grafos (ex.: Neo4j).

Cada interação recebe propriedades (peso, data, frequência).

<img width="761" height="374" alt="Image" src="https://github.com/user-attachments/assets/8135c7bf-8c8e-4353-bcc6-e7dea4b4ce83" />


### Exploração de ligações


O algoritmo percorre o grafo para encontrar músicas relacionadas ao perfil do usuário.


## Algoritmos aplicados


**PageRank Personalizado:** identifica músicas mais relevantes a partir das conexões do usuário.

**Node2Vec / DeepWalk:** gera embeddings dos nós e calcula similaridade entre usuários e músicas.

**Detecção de Comunidades (Louvain):** recomenda músicas dentro da comunidade musical do usuário.


### Ranking e recomendação

As músicas encontradas são ordenadas por relevância (popularidade, proximidade, novidade).

O sistema retorna uma lista personalizada de recomendações.

---

## 🔎Consultas Cypher de recomendação

### 1. Recomendar músicas de artistas seguidos

```
MATCH (u:Usuario {id:'user_1'})-[:SEGUIR]->(a:Artista)<-[:CRIADA_POR]-(m:Musica)
RETURN m.titulo AS recomendacao
ORDER BY m.titulo;
```
👉 Retorna músicas criadas por artistas que o usuário segue.

### 2. Recomendar músicas semelhantes às já curtidas

```
MATCH (u:Usuario {id:'user_1'})-[:CURTIR]->(m1:Musica)-[:PERTENCE_A]->(g:Genero)<-[:PERTENCE_A]-(m2:Musica)
WHERE NOT (u)-[:CURTIR]->(m2)
RETURN DISTINCT m2.titulo AS recomendacao, g.id AS genero
ORDER BY genero;
```
👉 Sugere músicas do mesmo gênero das que o usuário já curtiu.


### 3. Recomendar músicas populares entre usuários semelhantes

```
MATCH (u:Usuario {id:'user_1'})-[:ESCUTAR]->(m:Musica)<-[:ESCUTAR]-(other:Usuario)-[:ESCUTAR]->(rec:Musica)
WHERE NOT (u)-[:ESCUTAR]->(rec)
RETURN rec.titulo AS recomendacao, COUNT(*) AS popularidade
ORDER BY popularidade DESC;
```
👉 Sugere músicas que outros usuários com gostos semelhantes escutam.

### 4. Recomendar músicas novas de artistas já escutados

```
MATCH (u:Usuario {id:'user_1'})-[:ESCUTAR]->(m:Musica)-[:CRIADA_POR]->(a:Artista)<-[:CRIADA_POR]-(novas:Musica)
WHERE NOT (u)-[:ESCUTAR]->(novas)
RETURN DISTINCT novas.titulo AS recomendacao, a.nome AS artista;
```
👉 Sugere músicas de artistas que o usuário já ouviu, mas ainda não escutou.

---

## 📌 Benefícios da abordagem em grafos

**Captura relações complexas além de simples “usuário ouviu música”.**

**Permite recomendações exploratórias e descoberta de novos artistas.**

**Escalável para milhões de usuários e músicas.**

**Fácil integração com dados sociais (seguidores, playlists colaborativas).**
