### Enoncé

Exercice 1

Un bref examen de vos fichiers journaux a révélé que la plupart des requêtes effectuées sur la collection salles cible des capacités ainsi que des départements, comme ceci :

```
db.salles.find({"capacite": {$gt: 500}, "adresse.codePostal": /^30/}) 
db.salles.find({"adresse.codePostal": /^30/, "capacite": {$lte: 400}}) 
```

Que proposez-vous comme index qui puisse couvrir ces requêtes ?

Détruisez ensuite l’index créé.



### Rédaction

La majorité des requêtes sont faites sur les départements et la capacité des salles, on peut faire un index sur ces champs pour faire gagner du temps sur les recherches de ces champs.


``` bash

db.salles.createIndex({
	"capacite": 1,
	"adresse.codePostal": 1
},{
	"name": "index_codePostal_capacite"
})

```

![[Pasted image 20230209154403.png]]

Pour supprimer cet index, il suffit de faire : 

```bash

db.salles.dropIndex("index_codePostal_capacite")

```

![[Pasted image 20230209154505.png]]