# Atividade 2 - Pós DataScience - NoSQL - MONGODB

1. ativar o ambiente
```
# docker-compose up mongo-server
```

use a flag -d se quiser que rode em segundo plano.

2. abrir a console para consultas ao mongo
```
# docker exec -it pos_mongo_ativ2_mongo-server_1 mongo
```

## Exercício 1- Aquecendo com os pets 

1. Adicione outro Peixe e um Hamster com nome Frodo 
```
> db.pets.insert({name:"Frodo", species:"Peixe"})
WriteResult({ "nInserted" : 1 })
> db.pets.insert({name:"Frodo", species:"Hamster"})
WriteResult({ "nInserted" : 1 })
```

2. Faça uma contagem dos pets na coleção 
```
> db.pets.count()
8
```

3. Retorne apenas um elemento o método prático possível 
```
> db.pets.findOne()
{
        "_id" : ObjectId("5e88e9deda33d5899646ad20"),
        "name" : "Mike",
        "species" : "Hamster"
}
```

4. Identifique o ID para o Gato Kilha. 
```
> db.pets.find({name:"Kilha"})
{ "_id" : ObjectId("5e88ea05da33d5899646ad22"), "name" : "Kilha", "species" : "Gato" }
```
ou...
```
> db.pets.find({name:"Kilha"},{'_id':1})
{ "_id" : ObjectId("5e88ea05da33d5899646ad22") }
```

5. Faça uma busca pelo ID e traga o Hamster Mike 
```
> db.pets.find({_id:ObjectId('5e88e9deda33d5899646ad20')})
{ "_id" : ObjectId("5e88e9deda33d5899646ad20"), "name" : "Mike", "species" : "Hamster" }
```

6. Use o find para trazer todos os Hamsters 
```
> db.pets.find({species:"Hamster"})
{ "_id" : ObjectId("5e88e9deda33d5899646ad20"), "name" : "Mike", "species" : "Hamster" }
{ "_id" : ObjectId("5e88ea90da33d5899646ad27"), "name" : "Frodo", "species" : "Hamster" }
```

7. Use o find para listar todos os pets com nome Mike 
```
> db.pets.find({name:"Mike"})
{ "_id" : ObjectId("5e88e9deda33d5899646ad20"), "name" : "Mike", "species" : "Hamster" }
{ "_id" : ObjectId("5e88ea10da33d5899646ad23"), "name" : "Mike", "species" : "Cachorro" }
```

8. Liste apenas o documento que é um Cachorro chamado Mike
```
> db.pets.find({name:"Mike",species:"Cachorro"})
{ "_id" : ObjectId("5e88ea10da33d5899646ad23"), "name" : "Mike", "species" : "Cachorro" }
```

## Exercício 2 – Mama mia!

Para importar o arquivo _italian-people.js_ no docker foi necessário criar o volume no docker-compose e ao acessar a console do mongo executar:
```
> load('/mongo-seed/italian-people.js')
```

1. Liste/Conte todas as pessoas que tem exatamente 99 anos. Você pode usar um count para indicar a quantidade.
Usei um count da idade de 56 para provar que o resultado com 99 anos era de 0 registros.
```
> db.italians.count({age: 99})
0
> db.italians.count({age: 56})
111
```

2. Identifique quantas pessoas são elegíveis atendimento prioritário (pessoas com mais de 65 anos)
```
> db.italians.count({age: {'$gt': 65}})
1773
```

3. Identifique todos os jovens (pessoas entre 12 a 18 anos). 
```
> db.italians.count({age: {'$lte': 18, '$gte': 12}})
842
```

4. Identifique quantas pessoas tem gatos, quantas tem cachorro e quantas não tem nenhum dos dois 
6084 possuem gato:
```
> db.italians.count({cat:{$exists:true}})
6084
```

4028 possuem cachorro:
```
> db.italians.count({dog:{$exists:true}})
4028
```

2331 não possuem gato nem cachorro:
```
> db.italians.count({dog:{$exists:false},cat:{$exists:false}})
2331
```

5. Liste/Conte todas as pessoas acima de 60 anos que tenham gato 
6. Liste/Conte todos os jovens com cachorro 
7. Utilizando o $where, liste todas as pessoas que tem gato e cachorro 
8. Liste todas as pessoas mais novas que seus respectivos gatos. 
9. Liste as pessoas que tem o mesmo nome que seu bichano (gatou ou cachorro) 
10. Projete apenas o nome e sobrenome das pessoas com tipo de sangue de fator RH negativo 
11. Projete apenas os animais dos italianos. Devem ser listados os animais com nome e idade. Não mostre o identificado do mongo (ObjectId) 
12. Quais são as 5 pessoas mais velhas com sobrenome Rossi? 
13. Crie um italiano que tenha um leão como animal de estimação. Associe um nome e idade ao bichano
14. Infelizmente o Leão comeu o italiano. Remova essa pessoa usando o Id. 
15. Passou um ano. Atualize a idade de todos os italianos e dos bichanos em 1. 
16. O Corona Vírus chegou na Itália e misteriosamente atingiu pessoas somente com gatos e de 66 anos. Remova esses italianos. 
17. Utilizando o framework agregate, liste apenas as pessoas com nomes iguais a sua respectiva mãe e que tenha gato ou cachorro. 
18. Utilizando aggregate framework, faça uma lista de nomes única de nomes. Faça isso usando apenas o primeiro nome 
19. Agora faça a mesma lista do item acima, considerando nome completo. 
20. Procure pessoas que gosta de Banana ou Maçã, tenham cachorro ou gato, mais de 20 e  menos de 60 anos. 
