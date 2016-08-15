<properties 
	pageTitle="Unités de requête dans DocumentDB | Microsoft Azure" 
	description="Découvrez les besoins en unités de requête dans DocumentDB, et comment les spécifier et les estimer." 
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="mimig" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2016" 
	ms.author="stbaro"/>

#Unités de requête dans DocumentDB
Désormais disponible : DocumentDB [calculatrice d’unités de demande](https://www.documentdb.com/capacityplanner). Pour en savoir plus, consultez [Estimation des besoins de débit](documentdb-request-units.md#estimating-throughput-needs).

![Calculatrice de débit][5]

##Introduction
Cet article fournit une vue d’ensemble des unités de requête dans [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

-	Que sont les unités de requête et les frais de requête ?
-	Comment spécifier la capacité d’unités de requête pour une collection ?
-	Comment estimer les besoins en unités de requête de mon application ?
-	Que se passe-t-il si je dépasse la capacité d’unités de requête pour une collection ?


##Unités de requête et frais de requête
DocumentDB offre des performances élevées et prévisibles en *réservant* des ressources pour répondre aux besoins de débit de votre application. Étant donné que les schémas d’accès et de charge des applications changent au fil du temps, DocumentDB vous permet de facilement augmenter ou diminuer la quantité de débit réservé disponible pour votre application.

Avec DocumentDB, un débit réservé est spécifié en termes de traitement d’unités de requête par seconde. On peut considérer les unités de requête un peu comme une devise de débit : vous *réservez* une quantité d’unités de requête garantie accessible par seconde à votre application. Chaque opération dans DocumentDB (écriture d’un document, exécution d’une requête, mise à jour d’un document) consomme des ressources de processeur, de mémoire et d’E/S. Autrement dit, chaque opération entraîne des *frais de requête*, exprimés en *unités de requête*. Comprendre les facteurs qui ont un impact sur les frais d’unités de requête et cerner les demandes de débit de votre application vous permettent d’exécuter votre application de la manière la plus rentable possible.

##Spécification de la capacité d’unités de requête
Quand vous créez une collection DocumentDB, vous spécifiez le nombre d’unités de requête par seconde à réserver pour la collection. Une fois la collection créée, l’affectation totale des unités de requête spécifiée est réservée à une utilisation par la collection. Chaque collection est garantie comme ayant des caractéristiques de débit dédiées et isolées.

Il est important de noter que DocumentDB fonctionne d’après un modèle de réservation. Autrement dit, vous êtes facturé pour la quantité de débit *réservée* pour la collection, quelle que soit la quantité activement *utilisée*. Toutefois, n’oubliez pas qu’à mesure que les modèles d’utilisation, de données et de charge de votre application évoluent, vous pouvez facilement augmenter ou réduire la quantité d’unités réservées par le biais des Kits de développement logiciel DocumentDB ou à l’aide du [Portail Azure](https://portal.azure.com). Pour plus d’informations sur la façon d’augmenter ou de réduire le débit, consultez [Niveaux de performances dans DocumentDB](documentdb-performance-levels.md).

##Considérations relatives aux unités de requête
Quand vous évaluez le nombre d’unités de requête à réserver pour votre collection DocumentDB, vous devez impérativement tenir compte des variables suivantes :

- **Taille des documents**. Plus la taille du document est grande, plus le nombre d'unités consommées pour lire ou écrire des données augmente.
- **Nombre de propriétés de documents**. En supposant que toutes les propriétés sont indexées par défaut, le nombre d'unités consommées pour écrire un document augmente parallèlement au nombre de propriétés.
- **Cohérence des données**. Quand vous utilisez les niveaux de cohérence des données Strong (Fort) ou Bounded Staleness (En fonction de l'obsolescence), des unités supplémentaires sont consommées pour lire les documents.
- **Propriétés indexées**. Une stratégie d'indexation sur chaque collection détermine quelles propriétés sont indexées par défaut. Vous pouvez réduire la consommation d’unités de requête en limitant le nombre de propriétés indexées ou en activant l’indexation différée.
- **Indexation des documents**. Par défaut, chaque document est automatiquement indexé. Vous consommerez moins d'unités de requête en choisissant de ne pas indexer certains documents.
- **Modèles de requête**. La complexité d’une requête a un impact sur le nombre d’unités de requête consommées pour une opération. Le nombre de prédicats, la nature des prédicats, les projections, le nombre de fonctions définies par l’utilisateur et la taille du jeu de données source ont tous une influence sur le coût des opérations de requête.
- **Utilisation des scripts**. Comme avec les requêtes, les procédures stockées et les déclencheurs consomment plus ou moins d’unités de requête en fonction de la complexité des opérations effectuées. Pendant le développement de votre application, inspectez l'en-tête des frais de requêtes pour mieux comprendre de quelle façon chaque opération consomme la capacité des unités de requête.

##Estimation des besoins de débit
Une unité de requête est une mesure normalisée du coût de traitement de la requête. Une unité de requête représente la capacité de traitement nécessaire pour lire (par le biais d’un élément self link ou id) un seul document JSON de 1 Ko composé de 10 valeurs de propriété uniques (à l’exclusion des propriétés système). Une demande de création (insertion), de remplacement ou de suppression du même document nécessite un plus grand traitement de la part du service et consomme donc plus d’unités de requête.

> [AZURE.NOTE] La ligne de base d’une unité de requête pour un document de 1 Ko correspond à une opération GET simple par l’élément self link ou id du document.

###Utiliser la calculatrice d’unités de demande
Pour aider les clients à affiner leurs estimations de débit, il existe une [calculatrice d’unités de demande](https://www.documentdb.com/capacityplanner) sur le web qui permet d’estimer les besoins d’unité de demande pour les opérations courantes, notamment :

- Créations (écritures) de documents
- Lectures de documents
- Suppressions de documents
- Mises à jour de documents

L’outil inclut également la prise en charge de l’estimation des besoins de stockage de données en fonction des exemples de documents que vous fournissez.

L’utilisation de l’outil est simple :

1. Chargez un ou plusieurs documents JSON représentatifs.

	![Charger les documents vers la calculatrice d’unités de demande][2]

2. Pour estimer les besoins de stockage, entrez le nombre total de documents que vous voulez stocker.

3. Entrez le nombre d’opérations de création, lecture, mise à jour et suppression de documents dont vous avez besoin (par seconde). Pour estimer les frais d’unités de requête des opérations de mise à jour de documents, chargez une copie du document de l’étape 1 comprenant des mises à jour de champs types. Par exemple, si les mises à jour de document comprennent généralement la modification de deux propriétés nommées lastLogi et userVisits, copiez le document, mettez à jour les valeurs de ces deux propriétés, puis chargez le document copié.

	![Entrez les exigences de débit dans la calculatrice d’unités de demande][3]

4. Cliquez sur Calculer et examinez les résultats.

	![Résultats de la calculatrice d’unités de demande][4]

>[AZURE.NOTE]Si vous avez des types de documents qui varient considérablement en termes de taille et de nombre de propriétés indexées, chargez un exemple de chaque *type* de document standard dans l’outil, puis calculez les résultats.

###Utiliser l’en-tête de réponse de frais de requête DocumentDB
Chaque réponse du service DocumentDB inclut un en-tête personnalisé (x-ms-request-charge) qui contient le nombre d’unités de requête consommées pour la requête. Cet en-tête est également accessible par le biais des Kits de développement logiciel (SDK) DocumentDB. Dans le Kit de développement logiciel (SDK) .NET, RequestCharge est une propriété de l’objet ResourceResponse. Pour les requêtes, l’Explorateur de requête DocumentDB dans le portail Azure fournit des informations sur les frais de requête pour les requêtes exécutées.

![Examen des frais d’unités de requête dans l’Explorateur de requête][1]

Ainsi, une méthode permettant d’estimer la quantité de débit réservé requis par votre application consiste à enregistrer les frais d’unité de requête associés à l’exécution des opérations courantes sur un document représentatif utilisé par votre application, puis à évaluer le nombre d’opérations que vous prévoyez d’effectuer chaque seconde. Veillez à mesurer et à inclure également les requêtes courantes et l’utilisation des scripts DocumentDB.

>[AZURE.NOTE]Si vous avez des types de documents qui varient considérablement en termes de taille et de nombre de propriétés indexées, enregistrez les frais d’unités de requête d’opérations applicables associés à chaque *type* de document par défaut.

Par exemple :

1. Enregistrer les frais d’unités de requête de création (insertion) d’un document par défaut.
2. Enregistrer les frais d’unités de requête de lecture d’un document par défaut.
3. Enregistrer les frais d’unités de requête de mise à jour d’un document par défaut.
3. Enregistrer les frais d’unités de requête des requêtes de documents standard courantes.
4. Enregistrer les frais d’unités de requête des scripts personnalisés (procédures stockées, déclencheurs, fonctions définies par l’utilisateur) utilisés par l’application.
5. Calculer les unités de requête nécessaires étant donné l’estimation du nombre d’exécutions d’opérations prévues chaque seconde.

##Exemple d’estimation d’unités de requête
Considérez le document suivant d’environ 1 Ko :

	{
	 "id": "08259",
  	"description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  	"tags": [
    	{
      	"name": "cereals ready-to-eat"
    	},
    	{
      	"name": "kellogg"
    	},
    	{
      	"name": "kellogg's crispix"
    	}
	],
  	"version": 1,
  	"commonName": "Includes USDA Commodity B855",
  	"manufacturerName": "Kellogg, Co.",
  	"isFromSurvey": false,
  	"foodGroup": "Breakfast Cereals",
  	"nutrients": [
    	{
      	"id": "262",
      	"description": "Caffeine",
      	"nutritionValue": 0,
      	"units": "mg"
    	},
    	{
      	"id": "307",
      	"description": "Sodium, Na",
      	"nutritionValue": 611,
      	"units": "mg"
    	},
    	{
      	"id": "309",
      	"description": "Zinc, Zn",
      	"nutritionValue": 5.2,
      	"units": "mg"
    	}
  	],
  	"servings": [
    	{
      	"amount": 1,
      	"description": "cup (1 NLEA serving)",
      	"weightInGrams": 29
    	}
  	]
	}

>[AZURE.NOTE]Les documents étant minimisés dans DocumentDB, la taille du document ci-dessus calculée par le système est légèrement inférieure à 1 Ko.


Le tableau suivant montre les frais d’unités de requête approximatifs pour les opérations courantes sur ce document (les frais d’unités de requête approximatifs partent du principe que le niveau de cohérence du compte a comme valeur « Session » et que tous les documents sont indexés automatiquement) :

Opération|Frais d’unités de requête 
---|---
Créer le document|~15 unités de requête 
Lire le document|~1 unité de requête
Interroger le document par id|~2,5 unités de requête

En outre, ce tableau montre les frais d’unités de requête approximatifs pour les requêtes courantes utilisées dans l’application :

Requête|Frais d’unités de requête|Nombre de documents retournés
---|---|--- 
Sélectionner un aliment par id|~2,5 unités de requête|1 
Sélectionner un aliment par fabricant|~7 unités de requête|7
Sélectionner par groupe d’aliments et trier par poids|~70 unités de requête|100
Sélectionner les 10 premiers aliments dans un groupe d’aliments|~10 unités de requête|10

>[AZURE.NOTE]Les frais d’unités de requête varient en fonction du nombre de documents retournés.

Avec ces informations, nous pouvons estimer les besoins en unités de requête pour cette application étant donné le nombre d’opérations et de requêtes attendues par seconde :

Opération/Requête|Nombre estimé par seconde|Unités de requête nécessaires 
---|---|--- 
Créer le document|10|150 
Lire le document|100|100 
Sélectionner un aliment par fabricant|25|175 
Sélectionner par groupe d’aliments|10|700 
Sélectionner les 10 premiers|15|150 Total|155|1275

Dans ce cas, nous estimons l’exigence de débit moyen à 1275 unités de requête par seconde. Arrondi à la centaine la plus proche, nous devons approvisionner 1 300 unités de requête par seconde pour la collection de cette application.

##<a id="RequestRateTooLarge"></a> Dépassement des limites de débit réservé
Souvenez-vous que la consommation d’unités de requête est évaluée en fonction d’un taux par seconde. Pour les applications qui dépassent le taux d’unités de requête configuré pour une collection, les requêtes pour cette collection sont limitées jusqu’à ce que le taux tombe sous le niveau réservé. En cas de limitation, le serveur met fin à la demande de manière préventive avec RequestRateTooLargeException (code d’état HTTP 429) et il retourne l’en-tête x-ms-retry-after-ms indiquant la durée, en millisecondes, pendant laquelle l’utilisateur doit attendre avant de réessayer.

	HTTP Status 429
	Status Line: RequestRateTooLarge
	x-ms-retry-after-ms :100

Si vous utilisez des requêtes LINQ et le SDK .NET Client, la plupart du temps vous ne devez jamais traiter cette exception, car la version actuelle du SDK .NET Client intercepte implicitement cette réponse, respecte l’en-tête retry-after spécifié par le serveur, et réessaie d’effectuer la demande. La tentative suivante réussira toujours, sauf si plusieurs clients accèdent simultanément à votre compte.

Si vous avez plusieurs clients qui opèrent en même temps au-delà du taux de requêtes, le comportement par défaut peut ne pas suffire, et le client générera dans l’application une exception DocumentClientException avec le code d’état 429. Dans ce cas, vous pourriez traiter le comportement et la logique de nouvelles tentatives dans les routines de gestion d’erreurs de votre application ou accroître le débit réservé pour la collection.

##Étapes suivantes

Pour en savoir plus sur le débit réservé avec les bases de données Azure DocumentDB, explorez ces ressources :
 
- [Tarification de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/)
- [Gestion de la capacité de DocumentDB](documentdb-manage.md)
- [Modélisation des données dans DocumentDB](documentdb-modeling-data.md)
- [Niveaux de performances dans DocumentDB](documentdb-partition-data.md)

Pour en savoir plus sur DocumentDB, consultez la [documentation](https://azure.microsoft.com/documentation/services/documentdb/) Azure DocumentDB.

Pour commencer avec le test des performances et de la mise à l’échelle avec DocumentDB, consultez [Test des performances et de la mise à l’échelle avec Azure DocumentDB](documentdb-performance-testing.md).


[1]: ./media/documentdb-request-units/queryexplorer.png
[2]: ./media/documentdb-request-units/RUEstimatorUpload.png
[3]: ./media/documentdb-request-units/RUEstimatorDocuments.png
[4]: ./media/documentdb-request-units/RUEstimatorResults.png
[5]: ./media/documentdb-request-units/RUCalculator2.png

<!---HONumber=AcomDC_0803_2016-->