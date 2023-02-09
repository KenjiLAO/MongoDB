Bonus expliquez ce qui se passe plus bas :

```bash 

db.achats.aggregate([{
	$addFields: {
		"total_achats": { $sum: "$achats" },
		"total_reduc": { $sum: "$reductions" }}}, {
	$addFields: {
		"total_final": { $subtract: [ "$total_achats","$total_reduc"]}}}, {
	$project: {
		"_id": 0,
		"nom": 1,
		"prenom": 1,
		"Total payé": { $divide:[
			{$subtract: [
				{$multiply: [
					'$total_final', 100]},
					{$mod: [{$multiply: ['$total_final', 100]}, 1]}]}, 100]}}}])

```

Le premier $addField :
- On fait la somme de tous les champs "achats" et on stocke la valeur dans un nouveau champ "total_achats"
- De meme pour reduc

Le 2ème $addFields :

- On soustrait "total_achats" et "total_reduc" et on stocke la valeur dans un nouveau champ "total_final"

$project :
- On va afficher le nom, le prénom, et le total payé sans afficher l'id
	$multiply va permettre d'avoir la valeur finale au centième près (avoir les centimes)
	$mod permet d'avoir un reste égal à 0 si et seulement si "total_final" est un entier et non Décimal
		Pour être exacte, MongoDB supporte les BigDecimal pour toutes nouvelles valeurs entrées, ici on ne spécifie pas le type de "total_final" et "total_payé" donc il les met par défault à BigDecimal
		$mod ["total_final", 1] permet d'enlever les potentielles virgules des décimales pour rester en Integer
	Le $subtract enlève à "total_final" le reste de $mod 
	$divide va remettre le total en € avec les centièmes

Ce problème est aussi présent en javascript.
