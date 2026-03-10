## Documento Técnico – Algoritmo de Recomendação de Música com Grafos


# 🎯 Objetivo

O sistema tem como finalidade recomendar músicas para usuários com base em suas interações (escutar, curtir, seguir)
e nas conexões entre músicas, artistas e gêneros. A escolha por grafos se deve à capacidade de representar relações
complexas e explorar caminhos entre entidades de forma eficiente.


## 🧩 Modelagem do Grafo

# Entidades (nós)

Usuário

Música

Artista

Gênero


## Interações (arestas com propriedades)


(Usuario)-[:ESCUTAR {vezes, ultima}]->(Musica)

(Usuario)-[:CURTIR {data}]->(Musica)

(Usuario)-[:SEGUIR {desde}]->(Artista)

(Musica)-[:CRIADA_POR {colaboracao}]->(Artista)

(Musica)-[:PERTENCE_A {peso}]->(Genero)


## ⚙️ Funcionamento do Algoritmo

# Coleta de dados

Logs de escuta, curtidas, seguidores e metadados de músicas.


# Diagrama do grafo


Inserção dos nós e arestas no banco de grafos (ex.: Neo4j).

Cada interação recebe propriedades (peso, data, frequência).


# Exploração de ligações


O algoritmo percorre o grafo para encontrar músicas relacionadas ao perfil do usuário.


# Algoritmos aplicados


PageRank Personalizado: identifica músicas mais relevantes a partir das conexões do usuário.

Node2Vec / DeepWalk: gera embeddings dos nós e calcula similaridade entre usuários e músicas.

Detecção de Comunidades (Louvain): recomenda músicas dentro da comunidade musical do usuário.


# Ranking e recomendação


As músicas encontradas são ordenadas por relevância (popularidade, proximidade, novidade).

O sistema retorna uma lista personalizada de recomendações.


## 🔎 Exemplos de Queries Cypher

# Músicas de artistas seguidos

MATCH (u:Usuario {id:'user_1'})-[:SEGUIR]->(a:Artista)<-[:CRIADA_POR]-(m:Musica)
RETURN m.titulo AS recomendacao;

# Músicas semelhantes às curtidas

MATCH (u:Usuario {id:'user_1'})-[:CURTIR]->(m1:Musica)-[:PERTENCE_A]->(g:Genero)<-[:PERTENCE_A]-(m2:Musica)
WHERE NOT (u)-[:CURTIR]->(m2)
RETURN DISTINCT m2.titulo AS recomendacao;

# Músicas populares entre usuários semelhantes

MATCH (u:Usuario {id:'user_1'})-[:ESCUTAR]->(m:Musica)<-[:ESCUTAR]-(other:Usuario)-[:ESCUTAR]->(rec:Musica)
WHERE NOT (u)-[:ESCUTAR]->(rec)
RETURN rec.titulo AS recomendacao, COUNT(*) AS popularidade
ORDER BY popularidade DESC;


## 📌 Benefícios da abordagem em grafos

Captura relações complexas além de simples “usuário ouviu música”.

Permite recomendações exploratórias e descoberta de novos artistas.

Escalável para milhões de usuários e músicas.

Fácil integração com dados sociais (seguidores, playlists colaborativas).
