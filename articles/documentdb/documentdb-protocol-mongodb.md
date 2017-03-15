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
ms.date: 03/06/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 6c143a18883c99a24264d3174df7ec214d696fb5
ms.lasthandoff: 03/08/2017


---
# <a name="what-is-documentdb-api-for-mongodb"></a>Qu’est-ce que DocumentDB : API pour MongoDB ?

Les bases de données DocumentDB peuvent désormais être utilisées comme magasins de données pour les applications écrites pour MongoDB. Cela signifie qu’en utilisant les [pilotes](https://docs.mongodb.org/ecosystem/drivers/) existants pour les bases de données MongoDB, votre application écrite pour MongoDB peut désormais communiquer avec DocumentDB et utiliser des bases de données DocumentDB au lieu de bases de données MongoDB. Dans de nombreux cas, vous pouvez passer de MongoDB à DocumentDB en modifiant simplement une chaîne de connexion. Cette fonctionnalité en version préliminaire permet aux clients de générer et exécuter facilement des applications de base de données MongoDB dans le cloud Azure (en tirant parti des bases de données NoSQL évolutives et entièrement gérées de DocumentDB), tout en continuant à exploiter leurs outils et compétences existants pour MongoDB.

## <a name="what-is-the-benefit-of-using-documentdb-api-for-mongodb"></a>Quels sont les avantages liés à l’utilisation de DocumentDB : API pour MongoDB ?
**Aucun serveur de gestion** : DocumentDB est un service entièrement géré, ce qui signifie que vous n’avez pas d’infrastructure ou de machines virtuelles à gérer. DocumentDB est disponible dans plus de 20 [Régions Azure](https://azure.microsoft.com/regions/services/).

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
* Pour savoir comment obtenir les informations de chaîne de connexion de votre compte, suivez le didacticiel [Se connecter à un compte MongoDB](documentdb-connect-mongodb-account.md).
* Pour savoir comment créer une connexion entre votre base de données DocumentDB et l’application MongoDB dans MongoChef, suivez le didacticiel [Utiliser MongoChef avec DocumentDB](documentdb-mongodb-mongochef.md).
* Explorez les [exemples](documentdb-mongodb-samples.md) DocumentDB : API pour MongoDB.


