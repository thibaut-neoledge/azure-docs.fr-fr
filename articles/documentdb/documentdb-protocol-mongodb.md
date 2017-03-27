---
title: "Qu’est-ce que DocumentDB : API pour MongoDB ? | Microsoft Docs"
description: "En savoir plus sur DocumentDB : API pour MongoDB et comment vous pouvez facilement exécuter des applications MongoDB existantes dans le cloud Azure"
keywords: "qu’est-ce que MongoDB"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: f173fa709f2a7a21042752ba4b5ac936d01fe300
ms.lasthandoff: 03/15/2017


---
# <a name="what-is-documentdb-api-for-mongodb"></a>Qu’est-ce que DocumentDB : API pour MongoDB ?

Les bases de données DocumentDB peuvent désormais être utilisées comme magasins de données pour les applications écrites pour MongoDB. Cela signifie qu’en utilisant les [pilotes](https://docs.mongodb.org/ecosystem/drivers/) existants pour les bases de données MongoDB, votre application écrite pour MongoDB peut désormais communiquer avec DocumentDB et utiliser des bases de données DocumentDB au lieu de bases de données MongoDB. Dans de nombreux cas, vous pouvez passer de MongoDB à DocumentDB en modifiant simplement une chaîne de connexion. Cette fonctionnalité en version préliminaire permet aux clients de générer et exécuter facilement des applications de base de données MongoDB dans le cloud Azure (en tirant parti des bases de données NoSQL évolutives et entièrement gérées de DocumentDB), tout en continuant à exploiter leurs outils et compétences existants pour MongoDB.

## <a name="what-is-the-benefit-of-using-documentdb-api-for-mongodb"></a>Quels sont les avantages liés à l’utilisation de DocumentDB : API pour MongoDB ?
**Aucun serveur de gestion** : DocumentDB est un service entièrement géré, ce qui signifie que vous n’avez pas d’infrastructure ou de machines virtuelles à gérer. DocumentDB est disponible dans plus de 30 [Régions Azure](https://azure.microsoft.com/regions/services/).

**Mise à l’échelle illimitée** : vous pouvez mettre à l’échelle le débit et le stockage de façon indépendante et en toute flexibilité. Vous pouvez ajouter de la capacité afin de pouvoir répondre aisément à des millions de demandes par seconde.

**Qualité professionnelle** : DocumentDB prend en charge plusieurs réplicas locaux pour fournir une disponibilité de 99,99 % et une protection des données en cas de défaillances locales et régionales. DocumentDB offre des [certifications de conformité](https://www.microsoft.com/trustcenter) et des fonctionnalités de sécurité de qualité professionnelle. 

**Compatibilité avec MongoDB** : DocumentDB : API pour MongoDB est conçu pour assurer la compatibilité avec MongoDB. Vous pouvez utiliser votre code, vos applications, vos pilotes et vos outils existants pour travailler avec DocumentDB. 

En savoir plus dans cette vidéo Azure Friday avec Scott Hanselman et Kirill Gavrylyuk, responsable principal de l’ingénierie DocumentDB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/DocumentDB-Database-as-a-Service-for-MongoDB-Developers/player]
> 


## <a name="how-to-get-started"></a>Pour commencer
Créez un compte DocumentDB : API pour MongoDB dans le [portail Azure](https://portal.azure.com), puis permutez la connexion vers votre nouveau compte. 

*Et c’est tout !*

Pour plus des instructions plus détaillées, suivez [Créer un compte](documentdb-create-mongodb-account.md) et [Se connecter à votre compte](documentdb-connect-mongodb-account.md).

## <a name="next-steps"></a>Étapes suivantes

Informations sur DocumentDB : l’API de MongoDB est intégrée dans l’ensemble de la documentation DocumentDB, mais voici quelques points qui vous aideront à démarrer :
* Pour savoir comment obtenir les informations de chaîne de connexion de votre compte, suivez le didacticiel [Se connecter à un compte MongoDB](documentdb-connect-mongodb-account.md).
* Pour savoir comment créer une connexion entre votre base de données DocumentDB et l’application MongoDB dans MongoChef, suivez le didacticiel [Utiliser MongoChef avec DocumentDB](documentdb-mongodb-mongochef.md).
* Suivez le didacticiel [Migration des données vers DocumentDB avec prise en charge du protocole MongoDB](documentdb-mongodb-migrate.md) pour importer vos données dans une API de base de données MongoDB.
* Générez votre première API pour MongoDB à l’aide de [Node.js](documentdb-mongodb-samples.md).
* Générez votre première API pour l’application web MongoDB à l’aide de .[NET](documentdb-mongodb-application.md).
* Établissez une connexion à une API de compte MongoDB à l’aide de [Robomongo](documentdb-mongodb-robomongo.md).
* Découvrez combien d’unités de requête sont utilisées par vos opérations avec la commande [GetLastRequestStatistics et des mesures du portail Azure](documentdb-request-units.md#GetLastRequestStatistics).
* Découvrez comment [configurer des préférences de lecture pour les applications distribuées dans le monde entier](documentdb-distribute-data-globally.md#ReadPreferencesAPIforMongoDB).


