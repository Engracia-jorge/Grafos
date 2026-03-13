# 🌐 Analise de redes sociais

Este projeto demonstra como modelar uma rede social em um banco de dados de grafos (Neo4j) e explorar conexões entre usuários,
posts, comentários e grupos. O objetivo é mostrar como consultas Cypher podem responder perguntas de negócio e gerar recomendações.

# 📂 Estrutura do Grafo

# Nós (labels):

-**Usuario**

-**Post**

-**Comentario**

-**Grupo**

# Relacionamentos (edges):

-`(Usuario)-[:SEGUE]->(Usuario)`

-`(Usuario)-[:POSTOU]->(Post)`

-`(Usuario)-[:COMENTOU]->(Comentario)`

-`(Usuario)-[:MEMBRO_DE]->(Grupo)`

-`(Post)-[:PERTENCE_A]->(Grupo)`


# Diagrama do grafo

<img width="532" height="371" alt="Image" src="https://github.com/user-attachments/assets/8be09fed-93b9-4019-aa1d-7159e07aba20" />


# 📖 Exemplos de Consultas Cypher

### 🔹 1. Encontrar amigos em comum
```
MATCH (u1:Usuario {id:'user_1'})-[:SEGUE]->(amigo)<-[:SEGUE]-(u2:Usuario {id:'user_2'})
RETURN amigo.nome AS amigo_em_comum;
```
**Explicação:** retorna os usuários que são seguidos tanto por user_1 quanto por user_2.

### 🔹 2. Recomendar novos amigos (amigos de amigos)
```
MATCH (u:Usuario {id:'user_1'})-[:SEGUE]->(amigo)-[:SEGUE]->(sugerido)
WHERE NOT (u)-[:SEGUE]->(sugerido) AND u <> sugerido
RETURN DISTINCT sugerido.nome AS sugestao_de_amigo;
```
**Explicação:** sugere conexões que ainda não existem, mas que são comuns entre amigos.

### 🔹 3. Posts mais populares em um grupo
```
MATCH (g:Grupo {nome:'Tecnologia'})<-[:PERTENCE_A]-(p:Post)<-[:CURTIU]-(u:Usuario)
RETURN p.titulo AS post, COUNT(u) AS curtidas
ORDER BY curtidas DESC
LIMIT 5;
```
**Explicação:** lista os 5 posts mais curtidos dentro do grupo "Tecnologia".

### 🔹 4. Usuários mais influentes
```
MATCH (u:Usuario)<-[:SEGUE]-(seguidores:Usuario)
RETURN u.nome AS usuario, COUNT(seguidores) AS qtd_seguidores
ORDER BY qtd_seguidores DESC
LIMIT 10;
```
**Explicação:** mostra os 10 usuários com maior número de seguidores.

### 🔹 5. Recomendar grupos para um usuário
```
MATCH (u:Usuario {id:'user_1'})-[:SEGUE]->(amigo)-[:MEMBRO_DE]->(g:Grupo)
WHERE NOT (u)-[:MEMBRO_DE]->(g)
RETURN DISTINCT g.nome AS grupo_recomendado;
```
**Explicação:** recomenda grupos que amigos do usuário participam, mas que ele ainda não faz parte.

# 💡 Benefícios da abordagem em grafos

**Descobrir amigos em comum.**

**Recomendar novas conexões.**

**Identificar conteúdos populares.**

**Medir influência social.**

**Sugerir grupos relevantes.**

