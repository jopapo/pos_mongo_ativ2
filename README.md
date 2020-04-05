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
```
> db.italians.aggregate([{$match:{$and:[{$or:[{favFruits:{$elemMatch:{$in:['Banana','Maçã']}}}]},{$or:[{cat:{$exists:true}},{dog:{$exists:true}}]},{age:{$gt:20,$lt:60}}]}}])
{ "_id" : ObjectId("5e88fd907d0307cded002602"), "firstname" : "Stefania", "surname" : "Martinelli", "username" : "user126", "age" : 47, "email" : "Stefania.Martinelli@live.com", "bloodType" : "O+", "id_num" : "050857405732", "registerDate" : ISODate("2018-01-05T15:02:42.989Z"), "ticketNumber" : 5272, "jobs" : [ "Ciências do Consumo" ], "favFruits" : [ "Pêssego", "Maçã" ], "movies" : [
{ "title" : "Parasita (2019)", "rating" : 0.6 }, { "title" : "Clube da Luta (1999)", "rating" : 0.15 }, { "title" : "Coringa (2019)", "rating" : 3.56 }, { "title" : "Um Sonho de Liberdade (1994)", "rating" : 2.09 } ], "dog" : { "name" : "Patrizia", "age" : 14 } }
{ "_id" : ObjectId("5e88fd907d0307cded002610"), "firstname" : "Alex", "surname" : "Negri", "username" : "user140", "age" : 32, "email" : "Alex.Negri@gmail.com", "bloodType" : "O+", "id_num" : "040216683336", "registerDate" : ISODate("2015-06-24T23:30:58.964Z"), "ticketNumber" : 5102, "jobs" : [ "Redes de Computadores" ], "favFruits" : [ "Kiwi", "Maçã" ], "movies" : [ { "title" : "Vingadores: Ultimato (2019)", "rating" : 0.21 }, { "title" : "Cidade de Deus (2002)", "rating" : 1.85
}, { "title" : "Gladiador (2000)", "rating" : 0.09 } ], "dog" : { "name" : "Pasquale", "age" : 5
} }
{ "_id" : ObjectId("5e88fd907d0307cded002611"), "firstname" : "Gabiele", "surname" : "Carbone", "username" : "user141", "age" : 56, "email" : "Gabiele.Carbone@uol.com.br", "bloodType" : "AB+", "id_num" : "778723804554", "registerDate" : ISODate("2014-06-20T13:00:16.699Z"), "ticketNumber" :
2406, "jobs" : [ "Zootecnia", "Engenharia Civil" ], "favFruits" : [ "Melancia", "Banana" ], "movies" : [ { "title" : "Interestelar (2014)", "rating" : 1.34 }, { "title" : "A Viagem de Chihiro (2001)", "rating" : 4.6 }, { "title" : "A Vida é Bela (1997)", "rating" : 4.56 }, { "title" : "O Poderoso Chefão (1972)", "rating" : 0.9 } ], "cat" : { "name" : "Carlo", "age" : 11 } }
{ "_id" : ObjectId("5e88fd907d0307cded002612"), "firstname" : "Maria", "surname" : "Ferraro", "username" : "user142", "age" : 50, "email" : "Maria.Ferraro@gmail.com", "bloodType" : "O+", "id_num" : "323423265657", "registerDate" : ISODate("2020-01-31T07:25:07.677Z"), "ticketNumber" : 7847,
"jobs" : [ "Secretariado" ], "favFruits" : [ "Uva", "Maçã" ], "movies" : [ { "title" : "A Vida é
Bela (1997)", "rating" : 4.33 }, { "title" : "O Resgate do Soldado Ryan (1998)", "rating" : 4.61
}, { "title" : "Harakiri (1962)", "rating" : 3.4 }, { "title" : "Interestelar (2014)", "rating" : 4.86 } ], "dog" : { "name" : "Monica", "age" : 8 } }
{ "_id" : ObjectId("5e88fd907d0307cded002619"), "firstname" : "Eleonora", "surname" : "Ferrari",
"username" : "user149", "age" : 58, "email" : "Eleonora.Ferrari@uol.com.br", "bloodType" : "B-",
"id_num" : "188350572327", "registerDate" : ISODate("2019-11-16T23:47:43.539Z"), "ticketNumber" : 2872, "jobs" : [ "Hotelaria" ], "favFruits" : [ "Banana" ], "movies" : [ { "title" : "Star Wars, Episódio V: O Império Contra-Ataca (1980)", "rating" : 1.06 } ], "cat" : { "name" : "Martina", "age" : 15 } }
{ "_id" : ObjectId("5e88fd907d0307cded00261a"), "firstname" : "Anna", "surname" : "Russo", "username" : "user150", "age" : 45, "email" : "Anna.Russo@gmail.com", "bloodType" : "O+", "id_num" : "300328608765", "registerDate" : ISODate("2014-01-21T12:30:27.103Z"), "ticketNumber" : 9703, "jobs" : [ "Comunicação das Artes do Corpo", "Matemática" ], "favFruits" : [ "Banana", "Kiwi", "Mamão"
], "movies" : [ { "title" : "Matrix (1999)", "rating" : 2.95 }, { "title" : "À Espera de um Milagre (1999)", "rating" : 2.51 }, { "title" : "Harakiri (1962)", "rating" : 4.41 }, { "title" : "Cidade de Deus (2002)", "rating" : 4.64 } ], "father" : { "firstname" : "Giovanna", "surname" : "Russo", "age" : 72 }, "cat" : { "name" : "Paolo", "age" : 9 }, "dog" : { "name" : "Claudio", "age" : 3 } }
{ "_id" : ObjectId("5e88fd907d0307cded002634"), "firstname" : "Simona", "surname" : "Fontana", "username" : "user176", "age" : 44, "email" : "Simona.Fontana@outlook.com", "bloodType" : "A-", "id_num" : "350008863866", "registerDate" : ISODate("2008-04-28T19:14:57.101Z"), "ticketNumber" : 5151, "jobs" : [ "Secretariado", "Ecologia" ], "favFruits" : [ "Banana", "Laranja" ], "movies" : [
{ "title" : "Coringa (2019)", "rating" : 2.52 }, { "title" : "Parasita (2019)", "rating" : 1.42 }, { "title" : "Coringa (2019)", "rating" : 3.15 }, { "title" : "O Silêncio dos Inocentes (1991)", "rating" : 2.79 } ], "cat" : { "name" : "Roberto", "age" : 2 }, "dog" : { "name" : "Gianni", "age" : 13 } }
{ "_id" : ObjectId("5e88fd907d0307cded002642"), "firstname" : "Elisabetta", "surname" : "Caputo", "username" : "user190", "age" : 23, "email" : "Elisabetta.Caputo@live.com", "bloodType" : "AB-", "id_num" : "101416558014", "registerDate" : ISODate("2010-05-24T07:06:58.452Z"), "ticketNumber"
: 8654, "jobs" : [ "Gastronomia", "Produção Cultural" ], "favFruits" : [ "Banana", "Banana", "Laranja" ], "movies" : [ { "title" : "A Viagem de Chihiro (2001)", "rating" : 1.41 }, { "title" : "A Origem (2010)", "rating" : 1.11 } ], "dog" : { "name" : "Anna", "age" : 6 } }
{ "_id" : ObjectId("5e88fd907d0307cded002649"), "firstname" : "Stefano", "surname" : "Valentini", "username" : "user197", "age" : 21, "email" : "Stefano.Valentini@yahoo.com", "bloodType" : "B-", "id_num" : "772818774287", "registerDate" : ISODate("2015-06-19T10:59:20.220Z"), "ticketNumber"
: 5081, "jobs" : [ "Transporte" ], "favFruits" : [ "Maçã" ], "movies" : [ { "title" : "Os Bons Companheiros (1990)", "rating" : 0 }, { "title" : "A Vida é Bela (1997)", "rating" : 0.41 }, { "title" : "A Viagem de Chihiro (2001)", "rating" : 2.84 } ], "father" : { "firstname" : "Fabio", "surname" : "Valentini", "age" : 42 }, "dog" : { "name" : "Domenico", "age" : 11 } }
{ "_id" : ObjectId("5e88fd907d0307cded002657"), "firstname" : "Enrico", "surname" : "Parisi", "username" : "user211", "age" : 57, "email" : "Enrico.Parisi@live.com", "bloodType" : "AB-", "id_num" : "444637861166", "registerDate" : ISODate("2011-06-07T13:50:41.222Z"), "ticketNumber" : 1626,
"jobs" : [ "Design de Interiores" ], "favFruits" : [ "Maçã", "Pêssego", "Melancia" ], "movies" :
[ { "title" : "A Origem (2010)", "rating" : 2.54 }, { "title" : "Os Sete Samurais (1954)", "rating" : 0.38 }, { "title" : "Batman: O Cavaleiro das Trevas (2008)", "rating" : 1.58 } ], "cat" : {
"name" : "Elisa", "age" : 1 }, "dog" : { "name" : "Maurizio", "age" : 12 } }
{ "_id" : ObjectId("5e88fd907d0307cded00265f"), "firstname" : "Angela", "surname" : "Gatti", "username" : "user219", "age" : 30, "email" : "Angela.Gatti@uol.com.br", "bloodType" : "B+", "id_num" : "040385353236", "registerDate" : ISODate("2008-05-11T22:44:45.657Z"), "ticketNumber" : 6698, "jobs" : [ "Engenharia Cartográfica e de Agrimensura", "Engenharia de Inovação" ], "favFruits" : [ "Kiwi", "Goiaba", "Maçã" ], "movies" : [ { "title" : "Clube da Luta (1999)", "rating" : 1.01 } ], "dog" : { "name" : "Claudio", "age" : 6 } }
{ "_id" : ObjectId("5e88fd907d0307cded002665"), "firstname" : "Laura", "surname" : "Greco", "username" : "user225", "age" : 42, "email" : "Laura.Greco@uol.com.br", "bloodType" : "O-", "id_num" : "831202745157", "registerDate" : ISODate("2017-12-08T08:57:35.643Z"), "ticketNumber" : 5358, "jobs" : [ "Esporte", "Ciências Sociais e Humanas" ], "favFruits" : [ "Maçã", "Melancia" ], "movies" : [ { "title" : "À Espera de um Milagre (1999)", "rating" : 3.08 }, { "title" : "Batman: O Cavaleiro das Trevas (2008)", "rating" : 0.39 }, { "title" : "Três Homens em Conflito (1966)", "rating" : 0.94 }, { "title" : "A Lista de Schindler (1993)", "rating" : 1.49 }, { "title" : "Intocáveis (2011)", "rating" : 3.91 } ], "father" : { "firstname" : "Chiara", "surname" : "Greco", "age" :
79 }, "dog" : { "name" : "Veronica", "age" : 9 } }
{ "_id" : ObjectId("5e88fd907d0307cded002666"), "firstname" : "Giovanna", "surname" : "Rizzo", "username" : "user226", "age" : 42, "email" : "Giovanna.Rizzo@hotmail.com", "bloodType" : "B-", "id_num" : "537622483377", "registerDate" : ISODate("2011-01-21T04:47:44.925Z"), "ticketNumber" : 7499, "jobs" : [ "Zootecnia" ], "favFruits" : [ "Maçã", "Kiwi" ], "movies" : [ { "title" : "O Poderoso Chefão II (1974)", "rating" : 4.49 }, { "title" : "1917 (2019)", "rating" : 1.68 }, { "title" : "Os Bons Companheiros (1990)", "rating" : 0.25 }, { "title" : "Um Estranho no Ninho (1975)", "rating" : 3.77 }, { "title" : "Coringa (2019)", "rating" : 3.53 } ], "cat" : { "name" : "Giorgio", "age" : 15 } }
{ "_id" : ObjectId("5e88fd907d0307cded00266d"), "firstname" : "Eleonora", "surname" : "Vitale", "username" : "user233", "age" : 59, "email" : "Eleonora.Vitale@yahoo.com", "bloodType" : "B-", "id_num" : "686734457677", "registerDate" : ISODate("2010-02-24T19:00:50.197Z"), "ticketNumber" : 3220, "jobs" : [ "Irrigação e Drenagem" ], "favFruits" : [ "Banana" ], "movies" : [ { "title" : "O
Senhor dos Anéis: As Duas Torres (2002)", "rating" : 0.93 } ], "cat" : { "name" : "Giusy", "age"
: 12 } }
{ "_id" : ObjectId("5e88fd907d0307cded002681"), "firstname" : "Daniele", "surname" : "Morelli", "username" : "user253", "age" : 52, "email" : "Daniele.Morelli@outlook.com", "bloodType" : "O+", "id_num" : "623842725637", "registerDate" : ISODate("2019-07-23T16:15:15.489Z"), "ticketNumber" :
1220, "jobs" : [ "Engenharia Biomédica" ], "favFruits" : [ "Goiaba", "Banana", "Uva" ], "movies"
: [ { "title" : "Forrest Gump: O Contador de Histórias (1994)", "rating" : 3.89 }, { "title" : "Harakiri (1962)", "rating" : 0.9 }, { "title" : "Seven: Os Sete Crimes Capitais (1995)", "rating"
: 2.32 }, { "title" : "Seven: Os Sete Crimes Capitais (1995)", "rating" : 1.47 } ], "cat" : { "name" : "Patrizia", "age" : 9 } }
{ "_id" : ObjectId("5e88fd907d0307cded002682"), "firstname" : "Angela", "surname" : "Marini", "username" : "user254", "age" : 34, "email" : "Angela.Marini@uol.com.br", "bloodType" : "A+", "id_num" : "412514706446", "registerDate" : ISODate("2009-10-14T05:13:47.349Z"), "ticketNumber" : 9866, "jobs" : [ "Automação Industrial", "Saúde Coletiva" ], "favFruits" : [ "Goiaba", "Maçã", "Mamão" ], "movies" : [ { "title" : "Parasita (2019)", "rating" : 2.6 } ], "cat" : { "name" : "Rita", "age" : 8 }, "dog" : { "name" : "Claudia", "age" : 3 } }
{ "_id" : ObjectId("5e88fd907d0307cded002694"), "firstname" : "Giorgio", "surname" : "Parisi", "username" : "user272", "age" : 59, "email" : "Giorgio.Parisi@uol.com.br", "bloodType" : "A+", "id_num" : "540778572372", "registerDate" : ISODate("2008-05-10T15:20:07.320Z"), "ticketNumber" : 7265, "jobs" : [ "Gestão de Segurança Privada", "Processos Químicos" ], "favFruits" : [ "Banana", "Pêssego" ], "movies" : [ { "title" : "Pulp Fiction: Tempo de Violência (1994)", "rating" : 2.01 }, { "title" : "Cidade de Deus (2002)", "rating" : 3.07 }, { "title" : "Parasita (2019)", "rating"
: 3.61 } ], "cat" : { "name" : "Davide", "age" : 17 }, "dog" : { "name" : "Veronica", "age" : 2 } }
{ "_id" : ObjectId("5e88fd907d0307cded002698"), "firstname" : "Alessio", "surname" : "Testa", "username" : "user276", "age" : 49, "email" : "Alessio.Testa@uol.com.br", "bloodType" : "AB-", "id_num" : "744540272507", "registerDate" : ISODate("2009-04-07T11:07:54.343Z"), "ticketNumber" : 2811, "jobs" : [ "Administração Pública" ], "favFruits" : [ "Maçã" ], "movies" : [ { "title" : "O Silêncio dos Inocentes (1991)", "rating" : 2.89 }, { "title" : "O Poderoso Chefão II (1974)", "rating" : 2.96 } ], "father" : { "firstname" : "Daniele", "surname" : "Testa", "age" : 84 }, "cat" : { "name" : "Maurizio", "age" : 14 }, "dog" : { "name" : "Simone", "age" : 16 } }
{ "_id" : ObjectId("5e88fd907d0307cded00269a"), "firstname" : "Luigi", "surname" : "Colombo", "username" : "user278", "age" : 31, "email" : "Luigi.Colombo@gmail.com", "bloodType" : "B+", "id_num" : "584813077223", "registerDate" : ISODate("2019-12-31T14:23:39.722Z"), "ticketNumber" : 2544,
"jobs" : [ "Escrita Criativa" ], "favFruits" : [ "Maçã" ], "movies" : [ { "title" : "Coringa (2019)", "rating" : 0.87 }, { "title" : "Harakiri (1962)", "rating" : 4.3 }, { "title" : "O Silêncio
dos Inocentes (1991)", "rating" : 3.38 }, { "title" : "A Origem (2010)", "rating" : 1.63 } ], "cat" : { "name" : "Claudio", "age" : 16 } }
{ "_id" : ObjectId("5e88fd907d0307cded00269b"), "firstname" : "Gabiele", "surname" : "Fiore", "username" : "user279", "age" : 46, "email" : "Gabiele.Fiore@outlook.com", "bloodType" : "O-", "id_num" : "660075410412", "registerDate" : ISODate("2013-12-28T17:04:53.751Z"), "ticketNumber" : 7053, "jobs" : [ "Ciências Sociais e Humanas" ], "favFruits" : [ "Maçã" ], "movies" : [ { "title" : "Forrest Gump: O Contador de Histórias (1994)", "rating" : 0.2 }, { "title" : "Batman: O Cavaleiro das Trevas (2008)", "rating" : 0.35 }, { "title" : "Três Homens em Conflito (1966)", "rating" :
3.59 }, { "title" : "Guerra nas Estrelas (1977)", "rating" : 0.87 }, { "title" : "Coringa (2019)", "rating" : 1.45 } ], "cat" : { "name" : "Simona", "age" : 18 } }
...
```


## Exercício 3 - Stockbrokers

1. Liste as ações com profit acima de 0.5 (limite a 10 o resultado) 
2. Liste as ações com perdas (limite a 10 novamente) 
3. Liste as 10 ações mais rentáveis  
4. Qual foi o setor mais rentável? 
5. Ordene as ações pelo profit e usando um cursor, liste as ações. 
6. Renomeie o campo “Profit Margin” para apenas “profit”. 
7. Agora liste apenas a empresa e seu respectivo resultado 
8. Analise as ações. É uma bola de cristal na sua mão... Quais as três ações você investiria? 
9. Liste as ações agrupadas por setor


## Exercício 4 – Fraude na Enron! 

1. Liste as pessoas que enviaram e-mails (de forma distinta, ou seja, sem repetir). Quantas pessoas são? 
2. Contabilize quantos e-mails tem a palavra “fraud”
