### Enoncé

Exercice 1 Modifiez la collection salle afin que soient dorénavant validés les documents destinés à y être insérés ; cette validation aura lieu en mode « strict » et portera sur les champs suivants :

nom sera obligatoire et devra être de type chaîne de caractères.

capacite sera obligatoire et devra être de type entier (int).

Dans le champ adresse, les champs codePostal et ville, tous deux de type chaîne de caractères, seront obligatoires.

Que constatez-vous lors de la tentative d’insertion suivante, et quelle en est la cause ?

```
db.salles.insertOne( 
{"nom": "Super salle", "capacite": 1500, "adresse": {"ville": "Musiqueville"}} 
) 
```

Que proposez-vous pour régulariser la situation ?

Exercice 2

Rajoutez à vos critères de validation existants un critère supplémentaire : le champ _id devra dorénavant être de type entier (int) ou ObjectId.

Que se passe-t-il si vous tentez de mettre à jour l’ensemble des documents existants dans la collection à l’aide de la requête suivante :

```
db.salles.updateMany({}, {$set: {"verifie": true}}) 
```

Supprimez les critères rajoutés à l’aide de la méthode delete en JavaScript

Exercice 3

Rajoutez aux critères de validation existants le critère suivant :

Le champ smac doit être présent OU les styles musicaux doivent figurer parmi les suivants : "jazz", "soul", "funk" et "blues".

Que se passe-t-il lorsque nous exécutons la mise à jour suivante ?

db.salles.update({"_id": 3}, {$set: {"verifie": false}})

### Rédaction

#### Exercice 1

```bash 

var proprietes = {
	"nom" : {
		"bsonType": "string",
		"pattern": "^[A-Z].*",
		"description": "Chaine de caractères - obligatoire"},
	"capacite": {
		"bsonType": "int",
		"description": "Entier - obligatoire"},
	"adresse.codePostal": {
		"bsonType": "string",
		"description": "Chaîne de charactère - obligatoire"},
	"adresse.ville": {
		"bsonType": "string",
		"description": "Chaîne de charactère - obligatoire"}
}

db.runCommand({
	"collMod": "salles",
	"validator": {
		$jsonSchema: {
			"bsonType": "object",
			"required": ["nom", "capacite", "adresse.codePostal", "adresse.ville"],
			"properties": proprietes
		}
	}
})

db.salles.insertOne( 
{"nom": "Super salle", "capacite": 1500, "adresse": {"ville": "Musiqueville"}} 
)

```

- Une erreur survient :

![[Pasted image 20230208165511.png]]

On essaye d'insérer un document qui n'a pas tous les champs de remplit ( il manque le champ "adresse.codePostal"). Le champ "adresse.codePostal" est considéré obligatoire donc tous documents qui ne remplit pas ces conditions ne sont pas ajoutés.

- Proposition de la solution

Enlever l'obligation du code postal ou ajouter un champ "codePostal" dans l'insert

#### Exercice 2

```bash 

var proprietes = {
	"_id": {
		"bsonType": ["int", "objectId"],
		"description": "Entier ou ObjectId - obligatoire"},
	"nom" : {
		"bsonType": "string",
		"pattern": "^[A-Z].*",
		"description": "Chaine de caractères - obligatoire"},
	"capacite": {
		"bsonType": "int",
		"description": "Entier - obligatoire"},
	"adresse.codePostal": {
		"bsonType": "string",
		"description": "Chaîne de charactère - obligatoire"},
	"adresse.ville": {
		"bsonType": "string",
		"description": "Chaîne de charactère - obligatoire"}
}

db.runCommand({
	"collMod": "salles",
	"validator": {
		$jsonSchema: {
			"bsonType": "object",
			"required": ["_id","nom", "capacite", "adresse.codePostal", "adresse.ville"],
			"properties": proprietes
		}
	}
})


```

Que se passe-t-il si vous tentez de mettre à jour l’ensemble des documents existants dans la collection à l’aide de la requête suivante :

``` bash

db.salles.updateMany({}, {$set: {"verifie": true}}) 

```

- Cela ne fonctionne pas

![[Pasted image 20230208165728.png]]

- Suppression des critères 

``` bash

db.salles.updateMany({}, {$unset: {"verifie": true}}) 

```

![[Pasted image 20230208171740.png]]

#### Exercice 3

```bash 

var proprietes = {
	"nom" : {
		"bsonType": "string",
		"pattern": "^[A-Z].*",
		"description": "Chaine de caractères - obligatoire"},
	"capacite": {
		"bsonType": "int",
		"description": "Entier - obligatoire"},
	"adresse.codePostal": {
		"bsonType": "string",
		"description": "Chaîne de charactère - obligatoire"},
	"adresse.ville": {
		"bsonType": "string",
		"description": "Chaîne de charactère - obligatoire"},
	$or: [{
		"styles": {
			"bsonType" : ["array"],
			"enum": ["jazz", "soul", "funk", "blues"],
			"description": "Tableau de chaîne de caractère - obligatoire"}
		,
	},{
		"smac": {
			"bsonType": "boolean",
			"description": "Booléen - obligatoire"
		}
	}]
}

db.runCommand({
	"collMod": "salles",
	"validator": {
		$jsonSchema: {
			"bsonType": "object",
			$or [{"required": ["nom", "capacite", "adresse.codePostal", "adresse.ville", "styles"],
			"required": ["nom", "capacite", "adresse.codePostal", "adresse.ville", "smac"]}
			"properties": proprietes
		}
	}
})

db.salles.insertOne( 
{"nom": "Super salle", "capacite": 1500, "adresse": {"ville": "Musiqueville"}} 
)

```