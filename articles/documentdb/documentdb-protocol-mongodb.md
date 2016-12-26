---
title: Prise en charge du protocole DocumentDB pour MongoDB | Microsoft Docs
description: "Prise en charge du protocole DocumentDB pour MongoDB Elle vous permet d’utiliser Azure DocumentDB, un service cloud géré, comme magasin de données pour des applications écrites pour MongoDB."
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
ms.date: 11/23/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 0703fa4f4d3eb9f23aa9b3c0b90267fac9557f13
ms.openlocfilehash: 876fff6a63ccf1a3c10a6852927c60b454436954


---
# <a name="what-is-documentdb-protocol-support-for-mongodb"></a>Prise en charge du protocole DocumentDB pour MongoDB

Les bases de données DocumentDB peuvent désormais être utilisées comme magasins de données pour les applications écrites pour MongoDB. Cela signifie qu’en utilisant les [pilotes](https://docs.mongodb.org/ecosystem/drivers/) existants pour les bases de données MongoDB, votre application écrite pour MongoDB peut désormais communiquer avec DocumentDB et utiliser des bases de données DocumentDB au lieu de bases de données MongoDB. Dans de nombreux cas, vous pouvez passer de MongoDB à DocumentDB en modifiant simplement une chaîne de connexion. Cette fonctionnalité en version préliminaire permet aux clients de générer et exécuter facilement des applications de base de données MongoDB dans le cloud Azure (en tirant parti des bases de données NoSQL évolutives et entièrement gérées de DocumentDB), tout en continuant à exploiter leurs outils et compétences existants pour MongoDB.

## <a name="what-is-the-benefit-of-using-documentdb-protocol-support-for-mongodb"></a>Quels sont les avantages liés à l’utilisation de la prise en charge du protocole DocumentDB pour MongoDB ?
**Aucun serveur de gestion** : DocumentDB est un service entièrement géré, ce qui signifie que vous n’avez pas d’infrastructure ou de machines virtuelles à gérer. DocumentDB est disponible dans plus de 20 [Régions Azure](https://azure.microsoft.com/regions/services/).

**Mise à l’échelle illimitée** : vous pouvez mettre à l’échelle le débit et le stockage de façon indépendante et en toute flexibilité. Vous pouvez ajouter de la capacité afin de pouvoir répondre aisément à des millions de demandes par seconde.

**Qualité professionnelle** : DocumentDB prend en charge plusieurs réplicas locaux pour fournir une disponibilité de 99,99 % et une protection des données en cas de défaillances locales et régionales. DocumentDB offre des [certifications de conformité](https://www.microsoft.com/trustcenter) et des fonctionnalités de sécurité de qualité professionnelle. 

**Compatibilité avec MongoDB** : la prise en charge du protocole DocumentDB pour MongoDB est conçue pour assurer la compatibilité avec MongoDB. Vous pouvez utiliser votre code, vos applications, vos pilotes et vos outils existants pour travailler avec DocumentDB. 

## <a name="how-to-get-started"></a>Pour commencer
Créez un compte DocumentDB avec prise en charge du protocole pour MongoDB dans le [portail Azure](https://portal.azure.com), puis permutez la connexion vers votre nouveau compte. 

*Et c’est tout !*

Pour plus des instructions plus détaillées, suivez [Créer un compte](documentdb-create-mongodb-account.md) et [Se connecter à votre compte](documentdb-connect-mongodb-account.md).

## <a name="next-steps"></a>Étapes suivantes
* Pour créer un compte DocumentDB, suivez le didacticiel [Créer un compte DocumentDB avec prise en charge de protocole pour MongoDB](documentdb-create-mongodb-account.md).
* Pour savoir comment obtenir les informations de chaîne de connexion de votre compte, suivez le didacticiel [Se connecter à un compte DocumentDB avec prise en charge de protocole pour MongoDB](documentdb-connect-mongodb-account.md).
* Pour savoir comment créer une connexion entre votre base de données DocumentDB et l’application MongoDB dans MongoChef, suivez le didacticiel [Utiliser MongoChef avec un compte DocumentDB prenant en charge le protocole MongoDB](documentdb-mongodb-mongochef.md).
* Découvrez des [exemples](documentdb-mongodb-samples.md)de prise en charge du protocole MongoDB dans DocumentDB.




<!--HONumber=Nov16_HO4-->


