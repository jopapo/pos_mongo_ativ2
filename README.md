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