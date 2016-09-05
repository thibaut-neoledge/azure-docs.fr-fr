<properties 
	pageTitle="Prise en charge du protocole DocumentDB pour MongoDB | Microsoft Azure" 
	description="Découvrez la prise en charge du protocole DocumentDB pour MongoDB, désormais disponible en version préliminaire publique." 
	keywords="mongodb"
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/23/2016" 
	ms.author="stbaro"/>

# Prise en charge du protocole DocumentDB pour MongoDB

## Présentation d'Azure DocumentDB
Azure DocumentDB est un service de base de données NoSQL entièrement géré, conçu pour garantir des performances rapides et prévisibles, tout en offrant une haute disponibilité, une mise à l’échelle automatique et une grande facilité de développement. Avec son modèle de données flexible, sa faible latence et ses puissantes fonctionnalités de requête, il est idéal pour le web, les appareils mobiles, les jeux et l’IoT, et convient également à de nombreuses autres applications nécessitant une mise à l’échelle transparente. Pour en savoir plus, consultez la [présentation de DocumentDB](documentdb-introduction.md).

## Prise en charge du protocole DocumentDB pour MongoDB
Les bases de données DocumentDB peuvent désormais être utilisées comme magasins de données pour les applications écrites pour MongoDB. Grâce aux [pilotes](https://docs.mongodb.org/ecosystem/drivers/) existants pour MongoDB, les applications peuvent communiquer facilement et en toute transparence avec DocumentDB. Il suffit souvent de modifier une chaîne de connexion. Cette fonctionnalité en version préliminaire permet aux clients de générer et exécuter facilement des applications dans le cloud Azure (en tirant parti des bases de données NoSQL évolutives et entièrement gérées de DocumentDB) tout en continuant à exploiter leurs outils et compétences existants pour MongoDB.

Grâce à la prise en charge du protocole DocumentDB pour MongoDB, les principales fonctions d’API MongoDB peuvent créer, lire, mettre à jour et supprimer (CRUD, Create, Read, Update, Delete) des données et interroger la base de données. Les fonctionnalités actuellement implémentées ont été hiérarchisées en fonction des besoins des plateformes, infrastructures et outils courants et des clients MongoDB de grande envergure évaluant Azure pour leur plateforme cloud.
  

## Étapes suivantes


- Découvrez comment [créer](documentdb-create-mongodb-account.md) un compte DocumentDB prenant en charge le protocole MongoDB.
- Découvrez comment vous [connecter](documentdb-connect-mongodb-account.md) à un compte DocumentDB prenant en charge le protocole MongoDB.
- Apprenez à [utiliser MongoChef](documentdb-mongodb-mongochef.md) avec un compte DocumentDB prenant en charge le protocole MongoDB.
- Découvrez des [exemples](documentdb-mongodb-samples.md) de prise en charge du protocole MongoDB dans DocumentDB.

 

<!---HONumber=AcomDC_0824_2016-->