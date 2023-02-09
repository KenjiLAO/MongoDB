
### Enoncé

Jeu de données: Téléchargez ou générez un jeu de données de stations météorologiques, qui incluent la date, la température, la pression atmosphérique, etc.

Préparation des données: a. Importez les données de stations météorologiques dans MongoDB en utilisant la commande mongoimport. b. Assurez-vous que les données sont bien structurées et propres pour une utilisation ultérieure.

Indexation avec MongoDB: a. Créez un index sur le champ de la date pour améliorer les performances de la recherche. Utilisez la méthode createIndex (). b. Vérifiez que l'index a été créé en utilisant la méthode listIndexes ().

Requêtes MongoDB: a. Recherchez les stations météorologiques qui ont enregistré une température supérieure à 25°C pendant les mois d'été (juin à août). Utilisez la méthode find () et les opérateurs de comparaison pour trouver les documents qui correspondent à vos critères. b. Triez les stations météorologiques par pression atmosphérique, du plus élevé au plus bas. Utilisez la méthode sort () pour trier les résultats.

Framework d'agrégation: a. Calculez la température moyenne par station météorologique pour chaque mois de l'année. Utilisez le framework d'agrégation de MongoDB pour effectuer des calculs sur les données et grouper les données par mois. b. Trouvez la station météorologique qui a enregistré la plus haute température en été. Utilisez le framework d'agrégation de MongoDB pour effectuer des calculs sur les données et trouver la valeur maximale.

Export de la base de données: a. Exportez les résultats des requêtes dans un fichier CSV pour un usage ultérieur. Utilisez la commande mongoexport pour exporter des données de MongoDB.

### Rédaction

*Jeu de données: Téléchargez ou générez un jeu de données de stations météorologiques, qui incluent la date, la température, la pression atmosphérique, etc.*

Import du dataset météo depuis :
	https://www.kaggle.com/datasets/balabaskar/historical-weather-data-of-all-country-capitals

*Préparation des données: a. Importez les données de stations météorologiques dans MongoDB en utilisant la commande mongoimport. b. Assurez-vous que les données sont bien structurées et propres pour une utilisation ultérieure.*

Typage des données : 

```bash

{
	"_id" : "ObjectiId",
	"date" : "Date",
	"country" : "string",
	"city" : "string",
	"Latitude" : "double",
	"Longitude" : "double",
	"tavg" : "double",
	"tmin" : "double",
	"tmax" : "double",
	"wdir" : "double",
	"wspd" : "double",
	"pres" : "double"
}

```



*Indexation avec MongoDB: a. Créez un index sur le champ de la date pour améliorer les performances de la recherche. Utilisez la méthode createIndex (). b. Vérifiez que l'index a été créé en utilisant la méthode listIndexes ().*

Création d'un index sur le champ Date : 

```bash

db.weather.createIndex({"date": 1})

```

![[Pasted image 20230209102233.png]]


Vérification de la création de l'index :

```bash

db.weather.getIndexes()

```

![[Pasted image 20230209102457.png]]

Vérification en utilisant listIndexes(): 

```bash

db.runCommand({
	listIndexes: "weather" //Nom de la collection
})

```

![[Pasted image 20230209102747.png]]

Le deuxième [Object] dans  "firstBatch" est l'index créé juste au dessus.

*Requêtes MongoDB: a. Recherchez les stations météorologiques qui ont enregistré une température supérieure à 25°C pendant les mois d'été (juin à août). Utilisez la méthode find () et les opérateurs de comparaison pour trouver les documents qui correspondent à vos critères.*

Stations métérologiques qui ont enregistré une température supérieur à 25°C pendant les mois de Juin à Août 2019

```bash

db.weather.find({
	"date":{$lte:ISODate("2019-08-01"),$gte:ISODate("2019-06-01")},
	"tmax":{$gt: 25}
},{
	"_id" : 0,
	"country" : 1,
	"city": 1
})

```

![[Pasted image 20230209110100.png]]
*Il y en a d'autres, sinon le screen serait très grand*

*b. Triez les stations météorologiques par pression atmosphérique, du plus élevé au plus bas. Utilisez la méthode sort () pour trier les résultats.*

trie décroissant des stations par pression atmosphérique :

```bash

db.weather.find({
	"date":{$lte:ISODate("2019-08-01"),$gte:ISODate("2019-06-01")},
	"pres": {$exists:1}
},{
	"_id": 0,
	"country": 1,
	"city": 1,
	"pres": 1
}).sort({"pres": -1})

```

![[Pasted image 20230209111703.png]]
*Il y en a d'autres, sinon le screen serait très grand*

On peut penser à utiliser un .aggregate pour n'avoir que le nom d'une station qu'une seule fois

```bash

db.weather.aggregate( [  
{$group:{ _id: "$city", "pres": {$max: "$pres"} }}, { $sort: { "pres":-1 } } ] )

```

![[Pasted image 20230209124238.png]]


*Framework d'agrégation: a. Calculez la température moyenne par station météorologique pour chaque mois de l'année. Utilisez le framework d'agrégation de MongoDB pour effectuer des calculs sur les données et grouper les données par mois.*

Prenons 2020 comme année de référence, on va afficher à chaques mois la température moyenne perçu par chaques stations métérologiques.

```bash

db.weather.aggregate([{
	$match: {
		"tavg": {$exists: 1},
		"date":{$lte:ISODate("2020-12-31"),$gte:ISODate("2020-01-01")}
	}
},{
	$group: {
		"_id": {"city" : "$city",
			"year": {$year: "$date"},
			"month": {$month: "$date"}},
		"avg": {
			$avg: "$tavg"
		}
	}
},{
	$project: {
		"Temp_moyenne": { $divide:[
			{$subtract: [
				{$multiply: [
					'$avg', 10]},
					{$mod: [{$multiply: ['$avg', 10]}, 1]}]}, 10]}
	}
},{
	$sort: {"_id": 1}
}])


```

La partie `$project` permet d'avoir la température au dizième près

![[Pasted image 20230209135847.png]]
![[Pasted image 20230209135902.png]]

*b. Trouvez la station météorologique qui a enregistré la plus haute température en été. Utilisez le framework d'agrégation de MongoDB pour effectuer des calculs sur les données et trouver la valeur maximale.*

Prenons 2020 comme année de référence, on va afficher la plus haute température enregistrée en été en utilisant `aggregate()`

```bash 

db.weather.aggregate([{
	$match: {
		"tmax": {$exists: 1},
		"date":{$lte:ISODate("2020-08-31"),$gte:ISODate("2020-06-01")}
	}
},{
	$group: {
		"_id": {"city" : "$city",
			"year": {$year: "$date"},
			"month": {$month: "$date"}},
		"max": {
			$max: "$tmax"
		}
	}
},{
	$project: {
		"Temp_maximale": "$max"
	}
},{
	$sort: {"Temp_maximale": -1}
},{
	$limit: 1
}])



```

![[Pasted image 20230209141855.png]]

Température maximale trouvable aussi avec .find()

```bash 

db.weather.find({
	"date":{$lte:ISODate("2020-08-31"),$gte:ISODate("2020-06-01")}
},{
	"_id" : 0,
	"city": 1,
	"country": 1,
	"tmax": 1
}).sort({"tmax":-1}).limit(1)

```

![[Pasted image 20230209141930.png]]


*Export de la base de données: a. Exportez les résultats des requêtes dans un fichier CSV pour un usage ultérieur. Utilisez la commande mongoexport pour exporter des données de MongoDB.*

Exportation de la bdd

```bash

mongoexport --db=TP --collection=weather --type=csv --fields=_id,date,country,city,Latitude,Longitude,tavg,tmin,tmax,wdir,wspd,pres --out=data/weather.csv

```

![[Pasted image 20230209144309.png]]
