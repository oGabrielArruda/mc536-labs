# Lab06 - Patologias, Medicamentos e Efeitos Colaterais em Cypher

Estrutura de pastas:

~~~
├── README.md  <- arquivo apresentando a tarefa
~~~

# Equipe `Sexteto Sinistro [SEXTO]`

# Subgrupo `PONTE`
* `Antônio Hideto Borges Kotsubo` - `236041`
* `Gabriel Alves de Arruda` - `248132`
* `Guilherme Brentan de Oliveira` - `252764`

## Tarefa de Cypher sobre Patologias, Medicamentos e Efeitos Colaterais

## Exercício

Faça a projeção em relação a Patologia, ou seja, conecte patologias que são tratadas pela mesma droga.

### Resolução
~~~cypher
MATCH (p1:Pathology)<-[t1]-(d:Drug)-[t2]->(p2:Pathology)
WHERE t1.weight > 20 AND t2.weight > 20  // to reduce the number of operations
MERGE (p1)<-[r:Relates]->(p2)
ON CREATE SET r.weight=1
ON MATCH SET r.weight=r.weight+1
~~~

# Trabalhando com Efeitos Colaterais

## Exercício

Construa um grafo ligando os medicamentos aos efeitos colaterais (com pesos associados) a partir dos registros das pessoas, ou seja, se uma pessoa usa um medicamento e ela teve um efeito colateral, o medicamento deve ser ligado ao efeito colateral.

### Resolução
~~~cypher
LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/drug-use.csv' AS line
MERGE (per:Person {idPerson: line.idperson})
MERGE (d:Drug {code: line.codedrug})
MERGE (d)-[t:Treats]->(per)
ON CREATE SET t.weight=1
ON MATCH SET t.weight=t.weight+1

LOAD CSV WITH HEADERS FROM 'https://raw.githubusercontent.com/santanche/lab2learn/master/data/faers-2017/sideeffect.csv' AS line
MATCH (pat:Pathology {code: line.codePathology})
MATCH (per:Person {idPerson: line.idPerson})
MERGE (per)-[h:Has]->(pat)
ON CREATE SET h.weight=1
ON MATCH SET h.weight=h.weight+1

MATCH (d1:Drug)-[a]->(per1:Person)-[b]->(pat1:Pathology)
MERGE (d1)-[c:Causes]->(pat1)
ON CREATE SET c.weight=1
ON MATCH SET c.weight=c.weight+1
~~~

## Exercício

Que tipo de análise interessante pode ser feita com esse grafo?

Proponha um tipo de análise e escreva uma sentença em Cypher que realize a análise.

R: Nós podemos analisar ver quantos efeitos colaterais uma droga causa, pesquisando pelo seu nome

### Resolução
~~~cypher
MATCH p=(d:Drug)-[r:Causes]->()
WHERE d.name = 'drug_name'
RETURN p
~~~
