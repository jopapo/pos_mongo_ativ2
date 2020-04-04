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
```
> db.italians.count({cat:{$exists:true},age:{$gt:60}})
1458
```

6. Liste/Conte todos os jovens com cachorro 
```
> db.italians.count({age:{$lte:18,$gte:12},dog:{$exists:true}})
341
```

7. Utilizando o $where, liste todas as pessoas que tem gato e cachorro 
Só retornei a contagem pois a listagem ficou grande.
```
> db.italians.find({$where:function(){ return this.cat && this.dog }}).count()
2443
```

8. Liste todas as pessoas mais novas que seus respectivos gatos. 
```
> db.italians.count({$expr:{$lt:['$age','$cat.age']}})
645
```
... ou (prova real) ...
```
> db.italians.find({$where:function(){ return this.cat && this.age < this.cat.age }}).count()
645
```

9. Liste as pessoas que tem o mesmo nome que seu bichano (gatou ou cachorro) 
```
> db.italians.find({$where:function(){ return (this.cat && this.cat.name == this.firstname) || (this.dog && this.dog.name == this.firstname) }}).count()
80
```

10. Projete apenas o nome e sobrenome das pessoas com tipo de sangue de fator RH negativo 
Coloquei o bloodType também como prova real do filtro.
```
> db.italians.find({bloodType:{$regex:/\-$/}},{firstname:1,surname:1,bloodType:1})
{ "_id" : ObjectId("5e88eefd9a94af6327cebf5b"), "firstname" : "Marta", "surname" : "Sanna", "bloodType" : "O-" }
{ "_id" : ObjectId("5e88eefd9a94af6327cebf5c"), "firstname" : "Sara", "surname" : "Gatti", "bloodType" : "A-" }
{ "_id" : ObjectId("5e88eefd9a94af6327cebf61"), "firstname" : "Giorgio", "surname" : "Ferraro", "bloodType" : "B-" }
{ "_id" : ObjectId("5e88eefd9a94af6327cebf66"), "firstname" : "Marta", "surname" : "Mazza", "bloodType" : "O-" }
{ "_id" : ObjectId("5e88eefd9a94af6327cebf67"), "firstname" : "Roberta", "surname" : "De Rosa", "bloodType" : "A-" }
{ "_id" : ObjectId("5e88eefd9a94af6327cebf69"), "firstname" : "Luca", "surname" : "Rinaldi", "bloodType" : "A-" }
{ "_id" : ObjectId("5e88eefd9a94af6327cebf6a"), "firstname" : "Emanuele", "surname" : "Piras", "bloodType" : "B-" }
{ "_id" : ObjectId("5e88eefd9a94af6327cebf6b"), "firstname" : "Giacomo", "surname" : "Orlando", "bloodType" : "AB-" }
{ "_id" : ObjectId("5e88eefd9a94af6327cebf6c"), "firstname" : "Sara", "surname" : "Fontana", "bloodType" : "A-" }
{ "_id" : ObjectId("5e88eefd9a94af6327cebf6e"), "firstname" : "Angelo", "surname" : "Grasso", "bloodType" : "B-" }
{ "_id" : ObjectId("5e88eefd9a94af6327cebf70"), "firstname" : "Maria", "surname" : "Ricci", "bloodType" : "A-" }
{ "_id" : ObjectId("5e88eefd9a94af6327cebf71"), "firstname" : "Enrico", "surname" : "Galli", "bloodType" : "A-" }
{ "_id" : ObjectId("5e88eefd9a94af6327cebf73"), "firstname" : "Giovanna", "surname" : "Marino", "bloodType" : "O-" }
{ "_id" : ObjectId("5e88eefd9a94af6327cebf74"), "firstname" : "Daniele", "surname" : "Caputo", "bloodType" : "A-" }
{ "_id" : ObjectId("5e88eefd9a94af6327cebf77"), "firstname" : "Sergio", "surname" : "Ferri", "bloodType" : "A-" }
{ "_id" : ObjectId("5e88eefd9a94af6327cebf78"), "firstname" : "Simone", "surname" : "Ferraro", "bloodType" : "A-" }
{ "_id" : ObjectId("5e88eefd9a94af6327cebf7a"), "firstname" : "Roberta", "surname" : "Conti", "bloodType" : "O-" }
{ "_id" : ObjectId("5e88eefd9a94af6327cebf7c"), "firstname" : "Elisabetta", "surname" : "Riva", "bloodType" : "B-" }
{ "_id" : ObjectId("5e88eefd9a94af6327cebf7d"), "firstname" : "Roberto", "surname" : "Caputo", "bloodType" : "AB-" }
{ "_id" : ObjectId("5e88eefd9a94af6327cebf7e"), "firstname" : "Fabrizio", "surname" : "Esposito", "bloodType" : "AB-" }
...
> db.italians.find({bloodType:{$regex:/\-$/}},{firstname:1,surname:1}).count()
4922
```

11. Projete apenas os animais dos italianos. Devem ser listados os animais com nome e idade. Não mostre o identificado do mongo (ObjectId) 
```
> db.italians.find({$where:function(){return this.cat || this.dog}},{'cat.name':1,'cat.age':1,'dog.name':1,'dog.age':1,_id:0})
{ "cat" : { "name" : "Pasquale", "age" : 1 } }
{ "dog" : { "name" : "Alex", "age" : 11 } }
{ "cat" : { "name" : "Silvia", "age" : 9 } }
{ "cat" : { "name" : "Eleonora", "age" : 11 } }
{ "cat" : { "name" : "Chiara", "age" : 6 }, "dog" : { "name" : "Elisabetta", "age" : 13 } }
{ "cat" : { "name" : "Dario", "age" : 10 } }
{ "cat" : { "name" : "Elisabetta", "age" : 15 } }
{ "cat" : { "name" : "Gianni", "age" : 13 }, "dog" : { "name" : "Giorgia", "age" : 2 } }
{ "cat" : { "name" : "Alessio", "age" : 2 } }
{ "cat" : { "name" : "Luca", "age" : 17 } }
{ "cat" : { "name" : "Filipo", "age" : 11 } }
{ "cat" : { "name" : "Simone", "age" : 14 } }
{ "cat" : { "name" : "Emanuele", "age" : 17 } }
{ "cat" : { "name" : "Sergio", "age" : 2 } }
{ "cat" : { "name" : "Gabiele", "age" : 8 }, "dog" : { "name" : "Cinzia", "age" : 5 } }
{ "cat" : { "name" : "Marta", "age" : 5 } }
{ "cat" : { "name" : "Sergio", "age" : 15 }, "dog" : { "name" : "Matteo", "age" : 7 } }
{ "dog" : { "name" : "Angela", "age" : 3 } }
{ "cat" : { "name" : "Davide", "age" : 6 } }
{ "cat" : { "name" : "Daniele", "age" : 6 } }
...
> db.italians.find({$where:function(){return this.cat || this.dog}},{'cat.name':1,'cat.age':1,'dog.name':1,'dog.age':1,_id:0}).count()
7669
```

12. Quais são as 5 pessoas mais velhas com sobrenome Rossi? 
```
> db.italians.find({surname:'Rossi'},{firstname:1,surname:1,age:1,_id:0}).sort({age:-1}).limit(5)
{ "firstname" : "Claudio", "surname" : "Rossi", "age" : 78 }
{ "firstname" : "Silvia", "surname" : "Rossi", "age" : 78 }
{ "firstname" : "Veronica", "surname" : "Rossi", "age" : 77 }
{ "firstname" : "Carlo", "surname" : "Rossi", "age" : 77 }
{ "firstname" : "Giulia", "surname" : "Rossi", "age" : 75 }
```

13. Crie um italiano que tenha um leão como animal de estimação. Associe um nome e idade ao bichano
```
> db.italians.insert({"firstname":"João","surname":"Poffo","age":36,"username":"hurrah","email":"hurrah@furb.br","bloodType":"A+","id_num":"XXX","registeredDate":new Date(),"jobs":["Arquiteto deSoftware"],"favFruits":["Maçã","Morango","Banana"],"movies":[{title:"Clube da Luta","rating":10},{"title":"Coração Valente","rating":10},{"title":"Senhor dos Anéis - A Sociedade do Anel","rating":10},{"title":"O Virgem de 40 anos","rating":8}],"lion":{"name":"Gatuno","age":7}})
WriteResult({ "nInserted" : 1 })
```

14. Infelizmente o Leão comeu o italiano. Remova essa pessoa usando o Id. 
```
> db.italians.findOne({surname:'Poffo'},{firstname:1,surname:1,_id:1})
{
        "_id" : ObjectId("5e88fbf67d0307cdedfffed7"),
        "firstname" : "João",
        "surname" : "Poffo"
}
> db.italians.deleteOne({_id: ObjectId("5e88fbf67d0307cdedfffed7")})
{ "acknowledged" : true, "deletedCount" : 1 }
```

15. Passou um ano. Atualize a idade de todos os italianos e dos bichanos em 1. 
Como se trata de idade, é necessário incrementar do pai e da mãe.
```
> db.italians.updateMany({}, {$inc:{'age':1}})
{ "acknowledged" : true, "matchedCount" : 10000, "modifiedCount" : 10000 }
> db.italians.updateMany({cat:{$exists:true}}, {$inc:{'cat.age':1}})
{ "acknowledged" : true, "matchedCount" : 6069, "modifiedCount" : 6069 }
> db.italians.updateMany({dog:{$exists:true}}, {$inc:{'dog.age':1}})
{ "acknowledged" : true, "matchedCount" : 3967, "modifiedCount" : 3967 }
> db.italians.updateMany({father:{$exists:true}}, {$inc:{'father.age':1}})
{ "acknowledged" : true, "matchedCount" : 1940, "modifiedCount" : 1940 }
> db.italians.updateMany({mother:{$exists:true}}, {$inc:{'mother.age':1}})
{ "acknowledged" : true, "matchedCount" : 987, "modifiedCount" : 987 }
```

16. O Corona Vírus chegou na Itália e misteriosamente atingiu pessoas somente com gatos e de 66 anos. Remova esses italianos. 
Estranho _de 66 anos_, mas é esse o enunciado. Se fosse _maior que_ é só substituir o _$eq_ por _$gt_.
```
> db.italians.deleteMany({age:{$eq:66},cat:{$exists:true}})
{ "acknowledged" : true, "deletedCount" : 69 }
```

17. Utilizando o framework agregate, liste apenas as pessoas com nomes iguais a sua respectiva mãe e que tenha gato ou cachorro. 
```
> db.italians.aggregate([{$match:{$expr:{$eq:['$mother.firstname','$firstname']},$or:[{cat:{$exists:true}},{dog:{$exists:true}}]}}])
{ "_id" : ObjectId("5e88fd917d0307cded00291c"), "firstname" : "Eleonora", "surname" : "Villa", "username" : "user920", "age" : 17, "email" : "Eleonora.Villa@outlook.com", "bloodType" : "AB+", "id_num" : "570385132326", "registerDate" : ISODate("2017-01-05T15:16:13.407Z"), "ticketNumber" : 2668, "jobs" : [ "Farmácia", "Processos Metalúrgicos" ], "favFruits" : [ "Tangerina", "Mamão" ], "movies" : [ { "title" : "Parasita (2019)", "rating" : 4.21 }, { "title" : "A Felicidade Não se Compra (1946)", "rating" : 2.41 } ], "mother" : { "firstname" : "Eleonora", "surname" : "Villa", "age" : 34 }, "cat" : { "name" : "Giovanna", "age" : 11 } }
{ "_id" : ObjectId("5e88fd927d0307cded002caa"), "firstname" : "Giacomo", "surname" : "Donati", "username" : "user1830", "age" : 80, "email" : "Giacomo.Donati@hotmail.com", "bloodType" : "O-", "id_num" : "572865720628", "registerDate" : ISODate("2013-12-08T11:23:05.383Z"), "ticketNumber" : 9552, "jobs" : [ "Ciências Sociais e Humanas" ], "favFruits" : [ "Kiwi" ], "movies" : [ { "title"
: "O Senhor dos Anéis: O Retorno do Rei (2003)", "rating" : 1.29 }, { "title" : "Três Homens em Conflito (1966)", "rating" : 3.17 }, { "title" : "Três Homens em Conflito (1966)", "rating" : 2.08 } ], "mother" : { "firstname" : "Giacomo", "surname" : "Donati", "age" : 111 }, "cat" : { "name" : "Michela", "age" : 5 }, "dog" : { "name" : "Federica", "age" : 11 } }
{ "_id" : ObjectId("5e88fd947d0307cded003281"), "firstname" : "Pietro", "surname" : "Basile", "username" : "user3325", "age" : 49, "email" : "Pietro.Basile@yahoo.com", "bloodType" : "O+", "id_num" : "210344774158", "registerDate" : ISODate("2007-07-04T05:38:20.636Z"), "ticketNumber" : 5210, "jobs" : [ "Odontologia", "Rochas Ornamentais" ], "favFruits" : [ "Pêssego", "Maçã", "Maçã" ], "movies" : [ { "title" : "12 Homens e uma Sentença (1957)", "rating" : 2.81 }, { "title" : "Pulp Fiction: Tempo de Violência (1994)", "rating" : 1.09 }, { "title" : "Batman: O Cavaleiro das Trevas (2008)", "rating" : 1.81 } ], "mother" : { "firstname" : "Pietro", "surname" : "Basile", "age"
: 80 }, "cat" : { "name" : "Enrico", "age" : 14 }, "dog" : { "name" : "Cristian", "age" : 15 } }
{ "_id" : ObjectId("5e88fd967d0307cded003b88"), "firstname" : "Giovanna", "surname" : "Martino",
"username" : "user5636", "age" : 16, "email" : "Giovanna.Martino@yahoo.com", "bloodType" : "AB+", "id_num" : "720762886864", "registerDate" : ISODate("2016-12-09T19:43:52.818Z"), "ticketNumber"
: 1718, "jobs" : [ "Engenharia Têxtil", "Engenharia Metalúrgica" ], "favFruits" : [ "Uva", "Mamão", "Tangerina" ], "movies" : [ { "title" : "A Vida é Bela (1997)", "rating" : 4.78 } ], "mother"
: { "firstname" : "Giovanna", "surname" : "Martino", "age" : 48 }, "dog" : { "name" : "Elisa", "age" : 17 } }
{ "_id" : ObjectId("5e88fd9c7d0307cded004435"), "firstname" : "Luca", "surname" : "Pellegrino", "username" : "user7857", "age" : 21, "email" : "Luca.Pellegrino@gmail.com", "bloodType" : "O+", "id_num" : "331255713476", "registerDate" : ISODate("2007-03-08T04:44:00.612Z"), "ticketNumber" : 6474, "jobs" : [ "Fabricação Mecânica", "Esporte" ], "favFruits" : [ "Goiaba" ], "movies" : [ { "title" : "O Silêncio dos Inocentes (1991)", "rating" : 2.28 }, { "title" : "O Senhor dos Anéis: O
Retorno do Rei (2003)", "rating" : 4.65 } ], "mother" : { "firstname" : "Luca", "surname" : "Pellegrino", "age" : 46 }, "cat" : { "name" : "Federica", "age" : 9 }, "dog" : { "name" : "Fabio", "age" : 14 } }
{ "_id" : ObjectId("5e88fd9c7d0307cded004492"), "firstname" : "Barbara", "surname" : "Orlando", "username" : "user7950", "age" : 40, "email" : "Barbara.Orlando@yahoo.com", "bloodType" : "AB+", "id_num" : "323603872113", "registerDate" : ISODate("2007-08-02T20:01:03.132Z"), "ticketNumber" :
469, "jobs" : [ "Agroecologia" ], "favFruits" : [ "Melancia", "Banana", "Mamão" ], "movies" : [ { "title" : "Matrix (1999)", "rating" : 4.14 } ], "mother" : { "firstname" : "Barbara", "surname"
: "Orlando", "age" : 59 }, "cat" : { "name" : "Sonia", "age" : 15 }, "dog" : { "name" : "Pietro", "age" : 17 } }
{ "_id" : ObjectId("5e88fd9f7d0307cded0047ff"), "firstname" : "Valeira", "surname" : "Lombardo",
"username" : "user8827", "age" : 53, "email" : "Valeira.Lombardo@yahoo.com", "bloodType" : "AB+", "id_num" : "777560055717", "registerDate" : ISODate("2010-01-28T16:24:19.280Z"), "ticketNumber"
: 9975, "jobs" : [ "Aquicultura" ], "favFruits" : [ "Melancia" ], "movies" : [ { "title" : "A Origem (2010)", "rating" : 3.22 }, { "title" : "A Lista de Schindler (1993)", "rating" : 1.09 } ], "mother" : { "firstname" : "Valeira", "surname" : "Lombardo", "age" : 77 }, "cat" : { "name" : "Elena", "age" : 14 } }
{ "_id" : ObjectId("5e88fda27d0307cded004cbc"), "firstname" : "Paolo", "surname" : "Martini", "username" : "user10040", "age" : 49, "email" : "Paolo.Martini@outlook.com", "bloodType" : "B-", "id_num" : "508730047702", "registerDate" : ISODate("2009-06-01T08:47:56.967Z"), "ticketNumber" : 1019, "jobs" : [ "Engenharia Civil" ], "favFruits" : [ "Goiaba", "Kiwi" ], "movies" : [ { "title" : "O Senhor dos Anéis: As Duas Torres (2002)", "rating" : 1.08 }, { "title" : "Os Bons Companheiros (1990)", "rating" : 3.67 } ], "mother" : { "firstname" : "Paolo", "surname" : "Martini", "age"
: 79 }, "father" : { "firstname" : "Salvatore", "surname" : "Martini", "age" : 74 }, "cat" : { "name" : "Alessio", "age" : 4 } }
```

18. Utilizando aggregate framework, faça uma lista de nomes única de nomes. Faça isso usando apenas o primeiro nome 
Ordenei pela frequencia já que fica uma lista meio grande trazer todos. Inclusive, o enunciado não fala pra pegar os nomes das mães, pais, cachorros ou gatos.
```
> db.italians.aggregate([{$group:{_id:'$firstname', count:{$sum:1}}},{$sort:{count:-1}}])
{ "_id" : "Antonio", "count" : 122 }
{ "_id" : "Giovanni", "count" : 120 }
{ "_id" : "Stefano", "count" : 119 }
{ "_id" : "Fabio", "count" : 118 }
{ "_id" : "Sergio", "count" : 118 }
{ "_id" : "Angela", "count" : 118 }
{ "_id" : "Michela", "count" : 117 }
{ "_id" : "Enrico", "count" : 116 }
{ "_id" : "Roberta", "count" : 115 }
{ "_id" : "Alex", "count" : 115 }
{ "_id" : "Emanuele", "count" : 114 }
{ "_id" : "Alessia", "count" : 112 }
{ "_id" : "Federica", "count" : 112 }
{ "_id" : "Eleonora", "count" : 111 }
{ "_id" : "Claudia", "count" : 111 }
{ "_id" : "Barbara", "count" : 111 }
{ "_id" : "Teresa", "count" : 110 }
{ "_id" : "Simona", "count" : 110 }
{ "_id" : "Paola", "count" : 109 }
{ "_id" : "Angelo", "count" : 108 }
...
```

19. Agora faça a mesma lista do item acima, considerando nome completo. 
```
> db.italians.aggregate([{$group:{_id:{'firstname': '$firstname','surname':'$surname'}, count:{$sum:1}}},{$sort:{count:-1}}])
{ "_id" : { "firstname" : "Antonio", "surname" : "Colombo" }, "count" : 7 }
{ "_id" : { "firstname" : "Luca", "surname" : "Fiore" }, "count" : 6 }
{ "_id" : { "firstname" : "Roberta", "surname" : "Ruggiero" }, "count" : 5 }
{ "_id" : { "firstname" : "Emanuele", "surname" : "Bellini" }, "count" : 5 }
{ "_id" : { "firstname" : "Fabio", "surname" : "Conti" }, "count" : 5 }
{ "_id" : { "firstname" : "Cinzia", "surname" : "Basile" }, "count" : 5 }
{ "_id" : { "firstname" : "Claudia", "surname" : "Palumbo" }, "count" : 5 }
{ "_id" : { "firstname" : "Giuseppe", "surname" : "Messina" }, "count" : 5 }
{ "_id" : { "firstname" : "Enzo ", "surname" : "Parisi" }, "count" : 5 }
{ "_id" : { "firstname" : "Roberta", "surname" : "Ferri" }, "count" : 5 }
{ "_id" : { "firstname" : "Pietro", "surname" : "Grasso" }, "count" : 5 }
{ "_id" : { "firstname" : "Claudia", "surname" : "Sala" }, "count" : 5 }
{ "_id" : { "firstname" : "Cristian", "surname" : "Pellegrino" }, "count" : 5 }
{ "_id" : { "firstname" : "Roberta", "surname" : "Pagano" }, "count" : 5 }
{ "_id" : { "firstname" : "Cristina", "surname" : "Rinaldi" }, "count" : 5 }
{ "_id" : { "firstname" : "Vincenzo", "surname" : "Pagano" }, "count" : 5 }
{ "_id" : { "firstname" : "Marta", "surname" : "Galli" }, "count" : 5 }
{ "_id" : { "firstname" : "Massimiliano", "surname" : "Parisi" }, "count" : 5 }
{ "_id" : { "firstname" : "Mirko", "surname" : "Barbieri" }, "count" : 5 }
{ "_id" : { "firstname" : "Stefania", "surname" : "Fiore" }, "count" : 5 }
...
```

20. Procure pessoas que gosta de Banana ou Maçã, tenham cachorro ou gato, mais de 20 e  menos de 60 anos. 
