### Enoncé

Exercice 1

Écrivez le pipeline qui affichera dans un champ nommé ville le nom de celles abritant une salle de plus de 50 personnes ainsi qu’un booléen nommé grande qui sera positionné à la valeur « vrai » lorsque la salle dépasse une capacité de 1 000 personnes. Voici le squelette du code à utiliser dans le shell :

```
var pipeline = [ 
... 
] 
```

db.salles.aggregate(pipeline) 

Exercice 2

Écrivez le pipeline qui affichera dans un champ nommé apres_extension la capacité d’une salle augmentée de 100 places, dans un champ nommé avant_extension sa capacité originelle, ainsi que son nom.

Exercice 3

Écrivez le pipeline qui affichera, par numéro de département, la capacité totale des salles y résidant. Pour obtenir ce numéro, il vous faudra utiliser l’opérateur $substrBytes dont la syntaxe est la suivante :

```
{$substrBytes: [ < chaîne de caractères >, < indice de départ >, 
< longueur > ]} 
```

Exercice 4

Écrivez le pipeline qui affichera, pour chaque style musical, le nombre de salles le programmant. Ces styles seront classés par ordre alphabétique.

Exercice 5

À l’aide des buckets, comptez les salles en fonction de leur capacité :

celles de 100 à 500 places

celles de 500 à 5000 places

Exercice 6

Écrivez le pipeline qui affichera le nom des salles ainsi qu’un tableau nommé avis_excellents qui contiendra uniquement les avis dont la note est de 10.

### Rédaction

#### Exercice 1

Écrire le pipeline qui affichera dans un champ nommé ville le nom de celles abritant une salle de plus de 50 personnes ainsi qu’un booléen nommé "grande" qui aura comme valeur « vrai » lorsque la salle dépasse une capacité de 1 000 personnes.

`$match` va filtrer notre recherche
`$project` va afficher nos résultats et apporte des modifications à notre résultat final

```bash

var pipeline = [{
	$match: {
		"capacite": {$gt: 50}
	}
},{
	$project: {
		"_id": 0, //On ne veut pas afficher _id
		"ville": "$nom",
		"grande": {$gt: ["$capacite", 1000]}
	}
}]

db.salles.aggregate(pipeline)

```


![[Pasted image 20230209163231.png]]

#### Exercice 2

Écrire le pipeline qui affichera dans un champ nommé "apres_extension", la capacité d’une salle augmentée de 100 places, dans un champ nommé avant_extension sa capacité originelle, ainsi que son nom.

```bash

var pipeline = [{
	$project: {
		"_id": 0, //On ne veut pas afficher _id
		"apres_extension": {$add: ["$capacite", 100]},//Ajoute 100 à capacite
		"avant_extension": "$capacite"
	}
}]

db.salles.aggregate(pipeline)

```

![[Pasted image 20230209163900.png]]

#### Exercice 3

Écrire le pipeline qui affichera, par numéro de département, la capacité totale des salles y résidant.

``` bash

var pipeline = [{
	$match: {
		"adresse.codePostal": {$exists: 1}
	}
},{
	$project: {
		"_id": 0, //On ne veut pas afficher _id
		"Numero departement": {$substrBytes: ["$adresse.codePostal", 0, 2] },
		"Capacite totale": "$capacite"
	}
}]

db.salles.aggregate(pipeline)

```

![[Pasted image 20230209195247.png]]


Exercice 4

Écrire le pipeline qui affichera, pour chaque style musical, le nombre de salles le programmant. Ces styles sont classés par ordre alphabétique.

```bash

var pipeline = [
{
	$match: {
		"capacite": {$gt: 50}
	}
},{
	$project: {
		"_id": 0, //On ne veut pas afficher _id
		"ville": "$nom",
		"grande": {$gt: ["$capacite", 1000]}
	}
}]

db.salles.aggregate(pipeline)

```

Exercice 5

Compte des salles en fonction de leur capacité :

celles de 100 à 500 places

```bash 

var pipeline = [
	{ 
		$bucket: { 
			groupBy: "$capacite", 
			boundaries: [100, 500], //Limites
				default: "Salles qui n'ont pas une capacité de 100 à 500", 
			output: { 
				count: { $sum: 1 } 
			} 
		} 
	}
]

db.salles.aggregate(pipeline)

```

![[Pasted image 20230209194443.png]]


celles de 500 à 5000 places

```bash 

var pipeline = [
	{ 
		$bucket: { 
			groupBy: "$capacite", 
			boundaries: [500, 5000], //Limites
				default: "Salles qui n'ont pas une capacité de 500 à 5000", 
			output: { 
				count: { $sum: 1 } 
			} 
		} 
	}
]

db.salles.aggregate(pipeline)

```

![[Pasted image 20230209194622.png]]


Exercice 6

Écrivez le pipeline qui affichera le nom des salles ainsi qu’un tableau nommé avis_excellents qui contiendra uniquement les avis dont la note est de 10.

```bash 

var pipeline = [
  {
    $group: {
      _id: "$nom",
      avis_excellents: { 
        $push: {
          $cond: [
            { $eq: ["$avis.note", 10] },
            "$avis",
            null
          ]
        }
      }
    }
  },
  {
    $project: {
      _id: 0,
      nom: "$_id",
      avis_excellents: {
        $filter: {
          input: "$avis_excellents",
          as: "avis",
          cond: { $ne: ["$$avis", null] }
        }
      }
    }
  }
];

db.salles.aggregate(pipeline)

```