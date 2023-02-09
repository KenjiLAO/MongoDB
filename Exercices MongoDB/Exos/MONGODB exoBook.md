#### Enoncé

Créez une base de données sample nommée "sample_db" et une collection appelée "employees". Insérez les documents suivants dans la collection "employees":

{ name: "John Doe", age: 35, job: "Manager", salary: 80000 }

{ name: "Jane Doe", age: 32, job: "Developer", salary: 75000 }

{ name: "Jim Smith", age: 40, job: "Manager", salary: 85000 }

Écrivez une requête MongoDB pour trouver tous les documents dans la collection "employees".

Écrivez une requête pour trouver tous les documents où l'âge est supérieur à 33.

Écrivez une requête pour trier les documents dans la collection "employees" par salaire décroissant.

Écrivez une requête pour sélectionner uniquement le nom et le job de chaque document.

Écrivez une requête pour compter le nombre d'employés par poste.

Écrivez une requête pour mettre à jour le salaire de tous les développeurs à 80000.

#### Redaction

##### Entrer dans une base de données : 

``` bash
use sample_db
```

![[Pasted image 20230206160848.png]]

##### Créer une collection employees en inserant les données : 

``` bash
db.employees.insertMany([
{"name":"John Doe","age":20, "job": "Manager", "salary": 80000},
{"name":"Jane Doe","age":24, "job": "Developer", "salary": 75000},
{"name":"Jim Smith","age":40, "job": "Manager", "salary": 85000}])
```

![[Pasted image 20230206160928.png]]

##### Trouver tous les documents employees :

```bash
db.employees.find()

//Si on veut que les employees sans les id
db.employees.find({}, {
	"_id": 0,
	"name": 1,
	"age": 1,
	"job": 1,
	"salary": 1
})
```

![[Pasted image 20230206160945.png]]

Sans id
![[Pasted image 20230206161000.png]]

**Tous les résultats seront fait sans inclure l'id sauf si demandé.**

##### Trouver tous les documents où l'âge est supérieur à 33 :

```bash 
db.employees.find({
	"age": {
		$gt : 33
	}
}, {
	"_id": 0,
	"name": 1,
	"age": 1,
	"job": 1,
	"salary": 1
})

```

![[Pasted image 20230206161538.png]]

##### Trouver tous les documents de "employees" par salaire décroissant

``` bash
db.employees.find().sort({"salary": -1})
```

![[Pasted image 20230206162114.png]]

##### Trouver tous les documents en affichant seulement le nom et le job de chaques documents

``` bash
db.employees.find({}, {
	"_id": 0,
	"name": 1,
	"job": 1
})
```

![[Pasted image 20230206162543.png]]

##### Compter le nombre d'employés par poste

```bash
db.employees.aggregate({$group: {"_id": "$job", count: {$sum:1}}})
```

![[Pasted image 20230206164446.png]]

##### Mettre à jour le salaire de tous les développeur à 80000

``` bash

db.employees.updateMany({"job": "Developer"}, {$set : {"salary": 80000}})

```
![[Pasted image 20230206165130.png]]
