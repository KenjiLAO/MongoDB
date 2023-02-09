Mongodb : c'est une base de données

Stocke les documents sous json = format universel

Les document spermettent de stocker les informations

Une collection nest un groupe de dpcuments MongoDB. C'est l'équivalent d'une table dans une système de gestion de base combinées.
Les documents présents peuvent avoir des champs différents.

Un document est un objet. 

Un document est un ensemble de clé-valeur

Compass permet de dialoguer avec la bdd


Un document JSON a une taille de 16mo

Ceci représente un objet vide en JSON :

```JSON 
{}
```

Les propriétés d'un objet JSON sont materialisées par des clé et des valeurs

```JSON 
{
	key:value,
	anotherKey: anotherValue,
	key1 : {
				anotherKeyAgain : anotherValueAgain,
				id : 0
			},
	myArray : []
}
```

Très facile de lire un JSON, sous partie du javascript.
Le fichier .json peut etre ouvert de partout : word, ect...

JSON prend en charge de façon native plusieurs types de données :
- booleen
- numerique
- chaine de caractères
- tableau
- objet
- null (indique une abscence de valeur)

MongoDB vient ajouter des types :
- le type Date : stocke sous la forme d'un entier signé de 8 octets
	le nombre de secondes écoulées depuis l'époque Unix (01/01/1970 à minuit)
- le type ObjectId : stocke sur 12 octets, utilisé en interne par MongoDB afin de générer des identifiants
- le type NumberLong et le type NumberLong : par défault, MongoDB consodère toute valeur numérique comme un nombre à virgule codé sur 8 octets. Représente des entiers signés sur 8 et 4 octets respectivement.
- le type BinData : pour stocker des chaines de caractères ne possèdant pas de représentation dans l'encodage UTF-8, ou n'importe quel contenu bianaire 

### Compass

Mot réservé dans le shell de mongoDB : 
- db : stocke le nom de la bdd dans laquelle on utilise la base, la bdd en cours d'utilisation.

#### Entrer dans une bdd 

``` bash
use "databaseName"
```

#### Ajouter un user : 

``` bash
db.createUser ({"user" : "superUser" , "pwd" : "mypassword" , "role" : [{"readWriteAnyDatabase", "db" : "admin"}, "readWriteAnyDatabase"]})

```

#### Créer une bdd : 

``` BASH
use "newDatabase" //Pas de message d erreur, la bdd est créée que si on ajoute des documents
db.maCollection.insertOne({"une_cle":"une_valeur"})
```

#### Supprimer une bdd :

``` bash 
use"databaseToDelete"
db.dropDatabase()
```

Permet d'effectuer des commandes sous forme d'objets, les helpers :

```bash
db.runCommand({ "collstats" : "maCollection"})
```

Les helpers :

```bash
db.adminCommand("currentOp")
```

### Gestion des collections



#### Les collations

```
	{
		**locale**: < string >,
		caseLevel: < boolean >,
		caseFirst: < string >,
		strength: < entier >,
		numericOrdering: < boolean >,
		...
	}
```

Au sein de ce type de document, le champ "locale" est obligatoire

#### Créer une collection

``` bash
	db.createCollection("maCollection", {"collation" : {"locale" : "fr"}})
```


### Les documents

#### Insertion d'un document

``` bash
db.maCollection.insert(<documents ou un tableau de document>)
```

deprecated : risque de ne plus exister, amené à disparaitre

``` bash
db.personne.insert([
	{"nom" : "LAO" , "prenom" : "Kenji"},
	{"nom" : "JEAN" , "prenom" : "Thomas", "age" : 20}
])
```

L'insert fonctionne car il n'y a pas de restictions sur la bdd

#### Modification d'un document

```bash
db.collection.updateOne(<filtre>, <modifications>)

db.personnes.update(
	{"nom": "dupont"},
	{
		$set : {"nom": "Dupont"}
	}
)

//Le mieux est de cibler Id dans un update comme dans Sql
db.personnes.update(
	{"_id_": ObjectId("dupont")},
	{
		$set : {"nom": "Dupont"}
	}
)

```

Upsert : combinaison d'update et d'insert, si le champ n'existe pas il le crée et l'ajoute au document

``` bash
db.personnes.updateOne(
	{"prenom": "Remi"},
	{
		$set : {"prenom": "remi"}
	},
	{
		"upsert": true
	}
)
```

Effectuer des recherches sur la methode `findAndModidfy`

#### Validation des documents

``` bash
var athletes = [{
	"nom": "Eclair",
	"prenom": "Jean-Michel",
	"discipline": "Course"},
	{
	"nom": "Cavalera",
	"prenom": "Max",
	"discipline": "Saut de haies"},
	{
	"nom": "Hammer",
	"prenom": "Hemsi"
}]

db.athletes.insertMany(atheltes)
```

Exemple de schéma de validation : 

```bash
var proprietes = {
	"nom" : {
		"bsonType": "string",
		"pattern": "^[A-Z].*",
		"description": "Chaine de caractères + expressions régulière - obligatoire"},
	"prenom": {
		"bsonType": "string",
		"description": "Chaîne de caractères + expressions régulières - obligatoire"},
	"discipline": {
		"enum": ["Course", "Saut de haies", "Lancer de marteau"],
		"description": "Enumeration - obligatoire"}
}
```

Maintenant que nos règles sont établies, nous allons modifier la collection à l'aide de la commande `collMod`

```bash
db.runCommand({
	"collMod": "athletes",
	"validator": {
		$jsonSchema: {
			"bsonType": "object",
			"required": ["nom", "prenom", "discipline"],
			"properties": proprietes //variable au dessus
			}
		}
	}
})
```

Essayer de lever une erreur qui permet de vérifier que les schémas de validation appliqués fonctionnent

Les méthodes `find` et `findOne` ont la meme signature et permettent d'effectuer des requetes Mongo

```bash
db.collection.find(<requete>, <projection>)
```
Par défaut, find retourne 20 requêtes

Chadcun de ces paramètres sont tous deux des documents

```bash
DBQuery.shellBatchSize = 40
```

mongorc.js est un fichier de config qui se trouve à la racine du répertoire utilisateur

/home/userName

```bash
db.maCollection.find().limit(12)
db.maCollection.find().limit(12).pretty()
```


On peut utiliser des opérateurs afin d'affiner la recherche ;

```bash
db.maCollection.find({"age": {$eq: 76}})
db.maCollection.find({"age": 76}, {"prenom": true}) //Projection affiche prenom et par défaut ProjectId sinon mettre "ProjectId" : 0
```

$eq = equals

D'autres opérateurs :

- $ne : différent de
- $gt : supérieur à
- $gte: superieur ou égal à
- $lt: inférieur à
- $lte: inférieur ou égal à
- $in et $nin :  présence ou abscence

On peut combiner ces opérateurs pour effectuer des recherches sur des intervales : 
```bash 
db.maCollection.find({"age": {$lt: 50, $gt: 20}})
db.maCollection.find({"age": {$nin: [23,45,78]}})
db.maCollection.find({"age": {$exists: true}})
```

![[Pasted image 20230206152552.png]]

##### L'operateur $expr

Il permet d'utiliser des expressions dans nos requetes; Ces expressions pourront contenir des operateurs, des objets ou encore des chemins d'objets pointant vers des champs. (field path)

On va chercher à afficher les noms des documents dont la longueur du nom multiplié par 12 est supérieur à l'age

```bash 
db.personnes.find({
	"nom": {$exists: 1},
	"age": {$exists: 1},
	$expr: {$gt: [{ $multiply: [{ $strLenCP:"nom"}, 12]}, "$age"]}
},{
	"_id": 0,
	"nom": 1
})
```

Afficher les comptes dont la somme des opérations de débits est supérieur au montant du credit (ceux qui sont à découvert)

```bash
db.banque.find({"debit": {$exists:1}, $expr : {$gt: [{$sum: "$debit"}, "$credit"]}})
```

##### L'operateur $type

```bash
{ champ: {$type: < typeBSON > } }
{ champ: {$type: [< typeBSON >, < typeBSON >] } }


db.personnes.insertOne({
	"nom": "Zidane",
	"prenom": "Z",
	"age": numberInt(50) // Le type entier inseré est Int et pas double
})

```

##### L'opérateur $mod

```bash 
{ champ: { $mod: [diviseur, reste ] } }
{ champ: { $mod: [$age, 0 ] } } //Cherche les ages paires
```

##### L'opérateur $where

L'opérateur permet d'executer le code javascript venant d'une fonction

Privilégier $expr

```bash

db.personnes.find({$where: "this.nom.length > 6"})

db.personnes.find({$where: function() {
	return obj.nom.length > 6
}})

```

##### Les opérateurs de tableau

```bash 

// Ajoute au document ciblé
{ $push: { <champ>: <valeur>, ...} }

// Enlève au document ciblé
{ $pull: { <champ>: <valeur>, ...} }


db.hobbies.updateOne({"_id": 1}, {$push: {"passions": "Tekken 7"}}) 

db.hobbies.updateOne({"_id": 1}, {$pull: {"passions": "Tekken 7"}})

```

L'opérateur $addToSet permet d'éviter l'ajout de doublons

##### Exercice

Récupère les document dont l'interet est jardinage

```bash 
db.personnes.find({"interets": "jardinage"})

```

Personnes qui aime le jardinage bridge

```bash
db.personnes.find({"interets": ["jardinage", "bridge"]})

db.personnes.find({"interets": ["bridge", "jardinage"]}) //ne marche pas

//Affiche tous 
db.personnes.find({"interets":{ $all: ["bridge", "jardinage"]}})

Affiche tous les jardinage dans l index 1
db.personnes.find({"interets.1": "jardinage"})

//affiche tous les documents qui ont 2 interets
db.personnes.find({"interets": { $gt: 2 }})

//Pareil en ciblant avec l index 1
db.personnes.find({"interets.1": {$exists:1}})

```

##### L'operateur $elemMatch

```bash
//Cherche les élèves qui ont une note entre 0 et 10
db.eleves.find({"notes": { $elemMatch: {$gt: 0, $lt: 10}}})

//Cherche tous les élèves qui ont eu 5 et 7.50
db.eleves.find({"notes": { $all : [5,7.50]}})

```

##### Les tableaux de documents

Inserer des documents dans un tableau

```bash

db.eleves.find({
	"notes.note": 10
})

```

Rnevoyer les documents dont les élèves ont au moins une note entre 10 et 15 dans une matière quelconque

```bash

db.eleves.find({
	"notes": {
		$elemMatch: { 
			"note": { $gt: 10 , $lte: 15}
			}
		}
})

// Remonte toutes les notes
db.eleves.find({
	"notes.note": {$gt: 10, $lte: 15}
})

//tri toutes les notes qui ont - de 10 à l index 0 ( Maths )
db.eleves.find({
	"notes.0.note": {$lt: 10}
})

```


##### Le tri

``` bash

curseur.sort(<tri>)

1 : ordre croissant
-1 : ordre décroissant



```

Les données chargée en mémoire peuvent être limitées avec `filter(<number>)`

##### Les index

gain de temps pour la recherche

Evite de parcourir toute la collection

Diminue le temps de requetes , le temps de lecture

Ralentit le temps d'écriture

Indexer les recherches courantes faites par le client

Algolia et ELK last research

La nature de l'application devra impacter la logique d'indexation : est-elle orientée écriture (write-heavy) ou lecture (read-heavy) ?

Les clés primaires et étrangères sont automatiquement indexés

```bash

db.collection.createIndex(<champ + type>, <options?>)

db.personnes.insert(  [  {"nom": "Durand", "prenom": "René", "interets": ["jardinage", "bricolage"], "age": 77},  {"nom": "Durand", "prenom": "Gisèle", "interets": ["bridge", "cuisine"], "age": 75},  {"nom": "Dupont", "prenom": "Gaston", "interets": ["jardinage",   "pétanque"], "age": 79},  {"nom": "Dupont", "prenom": "Catherine", "interets": ["cuisine"], "age": 66},  {"nom": "Duport", "prenom": "Eric", "interets": ["cuisine", "pétanque"], "age": 57},  {"nom": "Duport", "prenom": "Arlette","interets": ["jardinage"], "age": 80},  {"nom": "Lejeune", "prenom": "Jean","interets": ["jardinage"], "age": 75},  {"nom": "Lejeune", "prenom": "Mariette","interets": ["jardinage", "bridge"], "age": 66}    )

//Affiche les ages décroissants
db.createIndex({"age": -1})

```

```bash 

//Affiche les index créés dans le document
db.personnes.getIndexes()

```

![[Pasted image 20230208093635.png]]



Un index peut porter sur plus d'un champ ; c'est un index composé

```bash

//Supprime l index
db.personnes.dropIndex("age_-1")

//Ajout d un index composé
db.personnes.createIndex({"age": -1}, {"name"; "index_age"})

//Faire une tache de fond
db.personnes.createIndex({"age": -1}, {"name": "index_age", "background": true}, {"collation": {"locale": "fr"}})


db.personnes.createIndex({"age": -1, "nom": 1}, {"name"; "index_age"}, {"name": "idx_age_nom", "collation" : {"locale";"fr"}})

```

###### Les index géospaciaux

MongoDB permet l'utilisation de deux types d'index qui permettent de gérer les requêtes geospatiales : 
- les index de type `2dsphere` sont utilisés par des requêtes geospatiales intervenant sur une surface spherique 
- les index `2d` concernent des requêtes intervenant sur un plan Euclidien

Pour un champ nommé `donneesSpatiales` d'une collection `cartographie` , on peut par exemple créer un index de type `2d` avec la commande 

```bash

db.cartographie.createIndex({"donneesSpatiales": "2d"})

```

Pour la création d'un index `2dsphere` , on utilisera plutôt :

```bash

db.cartographie.createIndex({"donneesSpatiales": "2dsphere"})

```

Les index 2d font intervenir des coordonnées de type `legacy`

```bash

db.plan.insertOne({"nom": "firstPoint", "geoData": [1,1]})

db.plan.insertOne({"nom": "firstPoint", "geoData": [4.7,44.5]})

db.plan.insertOne({"nom": "firstPoint", "geoData": {"lon": 4.7, "lat": 44.5}})

```

###### Les objets GeoJSON

Tracé fesable sur https://geojson.io/#map=2/3.37/12.35

```bash

{type: <type objet, coordinates: <coordonnées> }

```

###### Le type Point

```bash 

{
	"type": "Point",
	"coordinates": [14.0,1.0]
}

```

###### Le type MultiPoint

```bash

{
	"type": "MultiPoint",
	"coordinates": [
		[13.0, 1.0], [13.0, 3.0]
	]
}

```

###### Le type LineString

``` bash

{
	"type": "LineString",
	"coordinates": [
		[13.0, 1.0], [13.0, 3.0] //Ligne
	]
}

```

###### Le type Polygon

C'est une suite de LineString

``` bash

{
	"type": "Polygon",
	"coordinates": [
		[13.0, 1.0], [13.0, 3.0]
	],
	[
		[13.0, 1.0], [13.0, 3.0]
	]
}

{    "type": "Polygon",    "coordinates": [        [            [              -35.15625,              35.460669951495305            ],            [              18.28125,              -44.087585028245165            ],            [              59.765625,              13.239945499286312            ],            [              30.585937499999996,              33.7243396617476            ],            [              -35.15625,              35.460669951495305            ]        ]     ]  }

```

###### Le type MutliPolygon

C'est une suite de Polygon

``` bash

{
	"type": "MultiPolygon",
	"coordinates": [
		[13.0, 1.0], [13.0, 3.0]
	],
	[
		[13.0, 1.0], [13.0, 3.0]
	]
}

{    "type": "MultiPolygon",    "coordinates": [        [            [                [                    -4.921875,                    64.16810689799152                ],                [                    -35.15625,                    41.50857729743935                ],                [                    7.3828125,                    19.973348786110602                ],                [                    37.6171875,                    41.77131167976407                ],                [                    44.6484375,                    71.41317683396566                ],                [                    16.5234375,                    71.74643171904148                ],                [                    -4.921875,                    64.16810689799152                ]            ],            [                [                    -1.0546875,                    53.330872983017066                ],                [                    -9.140625,                    48.69096039092549                ],                [                    0.3515625,                    40.713955826286046                ],                [                    8.7890625,                    41.244772343082076                ],                [                    10.546875,                    49.83798245308484                ],                [                    -1.0546875,                    53.330872983017066                ]            ]        ],        [            [                [                    -22.67578125,                    68.07330474079025                ],                [                    -29.355468750000004,                    64.16810689799152                ],                [                    -18.45703125,                    60.930432202923335                ],                [                    -10.72265625,                    65.36683689226321                ],                [                    -22.67578125,                    68.07330474079025                ]            ]        ]     ]  }

```

###### Exercices


```bash 

db.avignon.createIndex({"localisation": "2dsphere"})

db.avignon2d.createIndex({"localisation": "2d"})

```

###### L'opérateur $nearSphere:

```bash 

{
	$nearSphere: {
		$geometry: {
			"type": "Point",
			"coordinate": [<longitude>, <latitude>]
			},
			$minDistance: <distance en metres>,
			$maxDistance: <distance en metres>
		}
}

{
	$nearSphere: [<x>, <y>],
	$minDistance: <distance en radian>
	$maxDistance: <distance en radian>
}

var opera = {"type": "Point", "coordinates": [43.949749, 4.805325]}

db.avignon.find({
	"localisation": {
		$nearSphere: {
			$geometry: opera
			}
		}
},{
	"_id": 0, "nom": 1
})

```

![[Pasted image 20230208112143.png]]

###### L'opérateur $geoWithin

Cet opérateur n'effectue aucun tri et ne necessite pas la création d'un index géospatiale, on l'utilise de la manière suivante 

Capture de document dont les données géospatiales sont présent dans le document

```bash

{
	<champ des documents contenant les coordonnées> : {
		$geoWithin: {
			<opérateur de forme>: <coordonnées>
			}
		} 
}

```

Creation d'un polygone pour l'exemple :

```bash

var polygon = [
	[43.9548, 4.80143],
	[43.95476, 4.80779],
	[43.95045, 4.81097],
	[43.4657, 4.80449]
]

```

Requete :

```bash

db.avignon.find({
	"localisation": {
		$geoWithin : {
			$polygon: polygon
			}
		}
},{
	"_id": 0, "nom": 1
})

```

###### Signature pour le cas de geoJSON 

```bash

{
	<champ des documents contenant les coordonnées>: {
	$geoWithin: {
		"type": <Polygon ou MultiPolygon>,
		"coordinates": [<coordonnées>]
		}
}
}

```

```bash

var polygone = [
	[43.9548, 4.80143],
	[43.95476, 4.80779],
	[43.95045, 4.81097],
	[43.4657, 4.80449],
	[43.9548, 4.80143]
]

db.avignon.find({
	"localisation": {
		$geoWithin: {
			$geometry : {
				"type": "Polygon",
				"coordinates": [polygone]
				}
			}
		}
}, {"_id": 0, "nom": 1})

```

##### Le framework d'agrégation

MongoDB met à disposition un puissant outil d'analyse et de traitement de l'information: le pipeline d'agrégation ( ou framework )

```bash 

db.personnes.aggregate(pipeline, options)

```


- pipeline: designe un tableau d'étapes
- options: désigne un document

	Parmi ces options, nous retiendrons :
	- collation, permet d'effectuer une collation à l'opération d'agregation
	- bypassDocumentValidation: fonctionne avec un opérateur appelé $out et permet de passe au travars de la validation des documents
	- allowDiskUse: donne la possibilité de faire deborder les opérations d'écriture sur le disque

On peut appeler aggregate sans arguments :

```bash

db.personnes.aggregate()

```

Creation de la variable pipeline :

```bash 

var pipeline = [
db.personnes.aggregate(pipeline)
db.personnes.aggregate(pipeline, {
	"collation": {
		"locale": "fr"
		}
})
]

```

###### Le filtrage avec $match

Cela permet d'obtenir des pipelines performants avec des temps d'executions courts. Normalement, $match doit intervenir le plus en amont possible dans le pipeline car $match agit comme un filtre en réduisant le nombre de documents à traiter plus en aval dans le pipeline. ( Dans l'idéal, on devrait le trouver comme premier opérateur)

la syntaxe est la suivante : 

```bash

{$match: {<requete>}}

```


Commencçons par la première étape 

```bash

var pipeline = [{
	$match: {
		"interets": "jardinage"
		}
}]

db.personnes.aggregate(pipeline)

```

Cela correspond à la requête :

```bash

db.personnes.find({"interets": "jardinage"})

```


```bash 

var pipeline = [{
	$match : {
		"interets": "jardinage"
	},
	$match: {
		"nom": /^L/,
		"age": {$gt: 70}
	}
}]

db.personnes.aggregate(pipeline)

```

![[Pasted image 20230208120341.png]]

###### Selection/Modification de champs : $project

Permet de faire des projections

Syntaxe : 

```bash

{$project: {<spec>}}

```

```bash

var pipeline = [{
	$match : {
		"interets": "jardinage" //Filtrage par jardinage
	}
},{
	$project: {
		"_id": 0,
		"nom": 1,
		"prenom" 1,
		"super_senior": {$gte: ["$age", 70]}
	}
},{
	$match: {
		"super_senior": true
	}
}]

db.presonnes.aggregate(pipeline)

```

```bash

db.personnes.updateOne({
	"nom": "Durand"}, 
	{$set: {"adresse": {"cp": 84140, "ville": "Montfavet"}}})

db.personnes.updateOne({"nom": "Dupont"}, {$set: {"adresse": {"cp": 13480, "ville": "Calas"}}})

var pipeline = [{
	$match : {
		"interets": "jardinage" //Filtrage par jardinage
	}
},{
	$project: {
		"_id": 0,
		"nom": 1,
		"prenom" 1,
		"ville": {"$adresse.ville"}
	}
},{
	$match: {
		"ville": {$exists: true}
	}
}]

db.presonnes.aggregate(pipeline)

```

###### L'opérateur $addFields

$addFields = object
```bash


{ $addFields: {<nouveau champ>: <expression>, ... } }

db.personnes.aggregate({
	$addFields: {
		"numero_securite_sociale": ""
	}
})

db.achats.insertMany([{     "nom": "Pascal",     "prenom": "Léo",     "achats": [112.29, 88.36, 72.01],     "reductions": [12.30, 2.01]  },  {     "nom": "Perez",     "prenom": "Alex",     "achats": [20.01, 296.35],     "reductions": [9.91, 0.87]  }])


db.achats.aggregate([
	{$addFields: {
		"total_achats": { $sum: "$achats" },
		"total_reduc": { $sum: "$reductions" }}}, 
	{$addFields: {
		"total_final": { $subtract: [ "$total_achats",  "$total_reduc" ] } } }, 
	{$project: {"_id": 0,"nom": 1, "prenom":1,"Total payé": "$total_final"}}])

```

Meme utilité que $project


###### L'opérateur $group

``` bash

{
	$group: {
		"_id": <expression>,
		<champ>: { <operateur d accumulation> }
		}
}

```

Operateur d'accumulation : `$push`, `$sum`, `$avg`, `$min`, `$max`

```bash

var pipeline = [{
	$group: {
		"_id": "$age",
		"nombre_personnes": {
			$sum: 1
		}
	}
},{
	$sort: {
		"nombre_personnes": 1
	}
}]

db.personnes.aggregate(pipeline)

db.personnes.aggregate([
	{
		$sortByCount: "$age"
	}
])

var pipeline = ({
	$group: {
		"_id": null,
		"nombre_personnes": {$sum: 1}
	}
})

var pipeline = [{
	$match: {
		"age": { $exists: 1}
	}
},{
	$group: {
		"_id": null,
		"avg": {
			$avg: "$age"
		}
	}
},{
	$project: {
		"_id":0,
		"Age_moyen": {
			$ceil: "$avg"
		}
	}
}]

```