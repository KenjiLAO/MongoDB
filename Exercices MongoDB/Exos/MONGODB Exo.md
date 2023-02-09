##### Enoncé
Voici la base de donnees qui permet d'effectuer la serie d'exercices :

```
db.salles.insertMany([ 
   { 
       "_id": 1, 
       "nom": "AJMI Jazz Club", 
       "adresse": { 
           "numero": 4, 
           "voie": "Rue des Escaliers Sainte-Anne", 
           "codePostal": "84000", 
           "ville": "Avignon", 
           "localisation": { 
               "type": "Point", 
               "coordinates": [43.951616, 4.808657] 
           } 
       }, 
       "styles": ["jazz", "soul", "funk", "blues"], 
       "avis": [{ 
               "date": new Date('2019-11-01'), 
               "note": NumberInt(8) 
           }, 
           { 
               "date": new Date('2019-11-30'), 
               "note": NumberInt(9) 
           } 
       ], 
       "capacite": NumberInt(300), 
       "smac": true 
   }, { 
       "_id": 2, 
       "nom": "Paloma", 
       "adresse": { 
           "numero": 250, 
           "voie": "Chemin de l'Aérodrome", 
           "codePostal": "30000", 
           "ville": "Nîmes", 
           "localisation": { 
               "type": "Point", 
               "coordinates": [43.856430, 4.405415] 
           } 
       }, 
       "avis": [{ 
               "date": new Date('2019-07-06'), 
               "note": NumberInt(10) 
           } 
       ], 
       "capacite": NumberInt(4000), 
       "smac": true 
   }, 
    { 
       "_id": 3, 
       "nom": "Sonograf", 
       "adresse": { 
           "voie": "D901", 
           "codePostal": "84250", 
           "ville": "Le Thor", 
           "localisation": { 
               "type": "Point", 
               "coordinates": [43.923005, 5.020077] 
           } 
       }, 
       "capacite": NumberInt(200), 
       "styles": ["blues", "rock"] 
   } 
]) 
```

Exercice 1

Affichez l’identifiant et le nom des salles qui sont des SMAC.

Exercice 2

Affichez le nom des salles qui possèdent une capacité d’accueil strictement supérieure à 1000 places.

Exercice 3

Affichez l’identifiant des salles pour lesquelles le champ adresse ne comporte pas de numéro.

Exercice 4

Affichez l’identifiant puis le nom des salles qui ont exactement un avis.

Exercice 5

Affichez tous les styles musicaux des salles qui programment notamment du blues.

Exercice 6

Affichez tous les styles musicaux des salles qui ont le style « blues » en première position dans leur tableau styles.

Exercice 7

Affichez la ville des salles dont le code postal commence par 84 et qui ont une capacité strictement inférieure à 500 places (pensez à utiliser une expression régulière).

Exercice 8

Affichez l’identifiant pour les salles dont l’identifiant est pair ou le champ avis est absent.

Exercice 9

Affichez le nom des salles dont au moins un des avis comporte une note comprise entre 8 et 10 (tous deux inclus).

Exercice 10

Affichez le nom des salles dont au moins un des avis comporte une date postérieure au 15/11/2019 (pensez à utiliser le type JavaScript Date).

Exercice 11

Affichez le nom ainsi que la capacité des salles dont le produit de la valeur de l’identifiant par 100 est strictement supérieur à la capacité.

Exercice 12

Affichez le nom des salles de type SMAC programmant plus de deux styles de musiques différents en utilisant l’opérateur $where qui permet de faire usage de JavaScript.

Exercice 13

Affichez les différents codes postaux présents dans les documents de la collection salles.

Exercice 14

Mettez à jour tous les documents de la collection salles en rajoutant 100 personnes à leur capacité actuelle.

Exercice 15

Ajoutez le style « jazz » à toutes les salles qui n’en programment pas.

Exercice 16

Retirez le style «funk» à toutes les salles dont l’identifiant n’est égal ni à 2, ni à 3.

Exercice 17

Ajoutez un tableau composé des styles «techno» et « reggae » à la salle dont l’identifiant est 3.

Exercice 18

Pour les salles dont le nom commence par la lettre P (majuscule ou minuscule), augmentez la capacité de 150 places et rajoutez un champ de type tableau nommé contact dans lequel se trouvera un document comportant un champ nommé telephone dont la valeur sera « 04 11 94 00 10 ».

Exercice 19

Pour les salles dont le nom commence par une voyelle (peu importe la casse, là aussi), rajoutez dans le tableau avis un document composé du champ date valant la date courante et du champ note valant 10 (double ou entier). L’expression régulière pour chercher une chaîne de caractères débutant par une voyelle suivie de n’importe quoi d’autre est [^aeiou]+$.

Exercice 20

En mode upsert, vous mettrez à jour tous les documents dont le nom commence par un z ou un Z en leur affectant comme nom « Pub Z », comme valeur du champ capacite 50 personnes (type entier et non décimal) et en positionnant le champ booléen smac à la valeur « false ».

Exercice 21

Affichez le décompte des documents pour lesquels le champ _id est de type « objectId ».

Exercice 22

Pour les documents dont le champ _id n’est pas de type « objectId », affichez le nom de la salle ayant la plus grande capacité. Pour y parvenir, vous effectuerez un tri dans l’ordre qui convient tout en limitant le nombre de documents affichés pour ne retourner que celui qui comporte la capacité maximale.

Exercice 23

Remplacez, sur la base de la valeur de son champ _id, le document créé à l’exercice 20 par un document contenant seulement le nom préexistant et la capacité, que vous monterez à 60 personnes.

Exercice 24

Effectuez la suppression d’un seul document avec les critères suivants : le champ _id est de type « objectId » et la capacité de la salle est inférieure ou égale à 60 personnes.

Exercice 25

À l’aide de la méthode permettant de trouver un seul document et de le mettre à jour en même temps, réduisez de 15 personnes la capacité de la salle située à Nîmes.

#### Rédaction

##### Exercice 1

Affiche le nom et l'identifiant qui sont des SMAC

```bash 
db.salles.find({"smac" : true}, {"_id": 1, "nom": 1})
```

![[Pasted image 20230206170629.png]]

##### Exercice 2

Affiche le nom des salles qui ont une capactié d'accueil strictement supérieur à 1000 places

```bash
db.salles.find({"capacite" : {$gt : 1000}}, {"_id": 0, "nom": 1})
```

![[Pasted image 20230206171034.png]]

##### Exercice 3 

Affiche l'identifiant des salles où le champ adresse n'a pas de numero

```bash
db.salles.find({"adresse.numero" : {$exists : true }}, {"_id" : 1})
```

![[Pasted image 20230206172522.png]]

##### Exercice 4

Affiche l’identifiant et le nom des salles qui ont exactement un avis.

```bash
db.salles.find({
	"avis": {$exists:1},
	"avis.1": {$exists : false}}, {"_id": 1, "nom": 1})
```

![[Pasted image 20230207111412.png]]

##### Exercice 5

Affiche tous les styles musicaux des salles qui programment notamment du blues.

```bash

db.salles.find({"styles": { $all : ["blues"] }}, {"_id": 1, "nom": 1})

```

![[Pasted image 20230207112233.png]]

##### Exercice 6 

Affiche tous les styles musicaux des salles qui ont le style « blues » en première position dans leur tableau styles.

```bash

db.salles.find({"styles.0": "blues"})

```

![[Pasted image 20230207112505.png]]


##### Exercice 7

Affiche la ville des salles dont le code postal commence par 84 et qui ont une capacité strictement inférieure à 500 places (pensez à utiliser une expression régulière).

```bash

db.salles.find({
	"capacite": {$lt : 500},
	"adresse.codePostal": {$regex : "84"}
},{"_id": 0, "adresse.ville": 1})

```

![[Pasted image 20230207113058.png]]


##### Exercice 8

Affiche l’identifiant pour les salles dont l’identifiant est pair ou le champ avis est absent.

```bash

db.salles.find({
	$or: [{
		"_id": { $mod: [ 2 , 0 ] }},
		{
		"avis": {$exists: 0}
		}]
}, {"_id": 1})

```

![[Pasted image 20230207141035.png]]


##### Exercice 9

Affiche le nom des salles dont au moins un des avis comporte une note comprise entre 8 et 10 (tous deux inclus).

```bash 

db.salles.find({
	"avis": {$exists: 1},
	"avis": {$elemMatch: {"note": {$gte: 8, $lte: 10}}}
}, {"_id": 0, "nom": 1})

```

![[Pasted image 20230207121633.png]]


##### Exercice 10 

Affiche le nom des salles dont au moins un des avis comporte une date postérieure au 15/11/2019 (pensez à utiliser le type JavaScript Date)

```bash

db.salles.find({
	"avis": {$exists: 0},
	"avis": { $elemMatch: {"date": {$gt: ISODate("2019-11-15T00:00:00.000Z")}} }
}, {"_id": 0, "nom": 1})

```

![[Pasted image 20230207123459.png]]

##### Exercice 11 

Affiche le nom ainsi que la capacité des salles dont le produit de la valeur de l’identifiant par 100 est strictement supérieur à la capacité.

```bash

db.salles.find({
	"_id": {$exists: 1},
	"capacite": {$exists: 1},
	$expr: {$gt: [{ $multiply: ["$_id", 100]}, "$capacite"]}
}, {"_id": 0, "nom": 1, "capacite": 1})

```

![[Pasted image 20230207124412.png]]


##### Exercice 12

Affiche le nom des salles de type SMAC programmant plus de deux styles de musiques différents en utilisant l’opérateur $where qui permet de faire usage de JavaScript.

```bash

db.salles.find({
	$where: "this.smac == true && this.styles?.length >2"
}, {"nom": 1})
```


##### Exercice 13

Affiche les différents codes postaux présents dans les documents de la collection salles.

```bash 

db.salles.find({}, {"_id": 0, "adresse.codePostal": 1})

```

![[Pasted image 20230207144206.png]]


##### Exercice 14

Mette à jour tous les documents de la collection salles en rajoutant 100 personnes à leur capacité actuelle.

```bash

db.salles.updateMany({},{$inc: {"capacite": 100}})

```

![[Pasted image 20230207153842.png]]
![[Pasted image 20230207153917.png]]


##### Exercice 15

Ajout du style « jazz » à toutes les salles qui n’en programment pas.

``` bash 

db.salles.updateMany({
	"styles": {$nin: ["jazz"]}
},{
	$push: {"styles": "jazz"}
})

```

![[Pasted image 20230207160751.png]]
![[Pasted image 20230207160827.png]]

##### Exercice 16

Retire le style «funk» à toutes les salles dont l’identifiant n’est égal ni à 2, ni à 3.

```bash

db.salles.updateMany({"_id": {$ne : [2,3]}}, {$pull: {"styles": "funk"}})

```

![[Pasted image 20230207161134.png]]
![[Pasted image 20230207161226.png]]



##### Exercice 17

Ajout d'un tableau composé des styles «techno» et « reggae » à la salle dont l’identifiant est 3.

```bash

db.salles.updateOne({"_id": 3}, {$push: {"styles": {$each: ["reggae", "techno"]}}})

```

![[Pasted image 20230207164143.png]]
![[Pasted image 20230207164207.png]]


##### Exercice 18

Pour les salles dont le nom commence par la lettre P (majuscule ou minuscule), augmentez la capacité de 150 places et rajoutez un champ de type tableau nommé contact dans lequel se trouvera un document comportant un champ nommé telephone dont la valeur sera « 04 11 94 00 10 ».

```bash

db.salles.updateMany({
	"nom": {$regex : /^p/, $options: "i"}	
},{
	$set: {"telephone": "04 11 94 00 10"},
	$inc: {"capacite": 150}
})

```

![[Pasted image 20230207171549.png]]
![[Pasted image 20230207171600.png]]


##### Exercice 19

Pour les salles dont le nom commence par une voyelle (peu importe la casse, là aussi), rajoutez dans le tableau avis un document composé du champ date valant la date courante et du champ note valant 10 (double ou entier). L’expression régulière pour chercher une chaîne de caractères débutant par une voyelle suivie de n’importe quoi d’autre est [^aeiou]+$.

```bash

db.salles.updateMany({
	"nom": {$regex: /^[aeiou].*/, $options: "i"}
}, {
	$push: { "avis": { "date": new Date(), "note": 10 } }
})

```
![[Pasted image 20230208091033.png]]
![[Pasted image 20230208091101.png]]


##### Exercice 20

En mode upsert, mettre à jour tous les documents dont le nom commence par un z ou un Z en leur affectant comme nom « Pub Z », comme valeur du champ capacite 50 personnes (type entier et non décimal) et en positionnant le champ booléen smac à la valeur « false ».

```bash

db.salles.updateMany({
	"nom": {$regex: /^z/, $options: "i"}
},{
	$set: {
	"nom": "Pub Z",
	"capacite": 50,
	"smac": false
	}
},{
	upsert: true
})

```

![[Pasted image 20230208153149.png]]



##### Exercice 21

Affichez le décompte des documents pour lesquels le champ _id est de type « objectId ».

```bash

db.salles.countDocuments({
	"_id": { $type: "objectId" }
})

```

![[Pasted image 20230208152927.png]]

##### Exercice 22

Pour les documents dont le champ _id n’est pas de type « objectId », affichez le nom de la salle ayant la plus grande capacité. Pour y parvenir, vous effectuerez un tri dans l’ordre qui convient tout en limitant le nombre de documents affichés pour ne retourner que celui qui comporte la capacité maximale.

```bash

db.salles.find({
	"_id": {$not: {$type: "objectId"}},
}, {"_id": 0, "nom": 1})
.sort({"capacite": -1})
.limit(1)

```

![[Pasted image 20230208124243.png]]


##### Exercice 23

Remplacez, sur la base de la valeur de son champ _id, le document créé à l’exercice 20 par un document contenant seulement le nom préexistant et la capacité, que vous monterez à 60 personnes.

```bash 

db.salles.updateOne({
	"_id": ObjectId("63e3b244e5c828b99b577a15")
},{
	$set: {"capacite": 60},
	$unset: {"smac":""}
})

```

![[Pasted image 20230208155037.png]]
![[Pasted image 20230208155050.png]]


##### Exercice 24

Effectuez la suppression d’un seul document avec les critères suivants : le champ _id est de type « objectId » et la capacité de la salle est inférieure ou égale à 60 personnes.

```bash

db.salles.deleteOne({
	"_id": {$type: "objectId"},
	"capacite":{ $lte: 60}
})

```

![[Pasted image 20230208145229.png]]


##### Exercice 25

À l’aide de la méthode permettant de trouver un seul document et de le mettre à jour en même temps, réduisez de 15 personnes la capacité de la salle située à Nîmes.

```bash 

db.salles.updateOne({
	"adresse.ville": "Nîmes"
}, {
	$inc: {"capacite": -15}
})

```

![[Pasted image 20230208152106.png]]

![[Pasted image 20230208152229.png]]



