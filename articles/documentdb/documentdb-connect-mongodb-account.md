---
title: "Chaîne de connexion MongoDB pour le compte Azure Cosmos DB | Microsoft Docs"
description: "Découvrez comment connecter votre application MongoDB à un compte Azure Cosmos DB à l’aide d’une chaîne de connexion MongoDB."
keywords: "chaîne de connexion mongodb"
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: e36f7375-9329-403b-afd1-4ab49894f75e
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 95fe71c87a3f70174b59cc866d7d399b7e91720c
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---

# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Connecter une application MongoDB à Azure Cosmos DB
Découvrez comment connecter votre application MongoDB à un compte Azure Cosmos DB à l’aide d’une chaîne de connexion MongoDB. En connectant votre application MongoDB à une base de données Azure Cosmos DB, vous pouvez utiliser une base de données Azure Cosmos DB comme magasin de données pour votre application MongoDB. 

Ce didacticiel fournit deux façons de récupérer les informations de la chaîne de connexion :

- [La méthode Démarrage rapide](#QuickstartConnection), à utiliser avec les pilotes .NET, Node.js, MongoDB Shell, Java et Python.
- [La méthode de chaîne de connexion personnalisée](#GetCustomConnection), à utiliser avec d’autres pilotes.

## <a name="prerequisites"></a>Composants requis

- Un compte Azure. Si vous ne possédez pas de compte Azure, vous pouvez créer un [compte Azure gratuit](https://azure.microsoft.com/free/) dès maintenant. 
- Un compte Azure Cosmos DB. Pour obtenir des instructions, consultez [Azure Cosmos DB : Développer une application web API DocumentDB avec .NET et le portail Azure](documentdb-create-mongodb-account.md).

## <a id="QuickstartConnection"></a>Obtenir la chaîne de connexion MongoDB à l’aide de Démarrage rapide
1. Dans un navigateur Internet, connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le panneau **Azure Cosmos DB**, sélectionnez le compte d’API MongoDB. 
3. Dans la barre de **navigation de gauche** du panneau Compte, cliquez sur **Démarrage rapide**. 
4. Choisissez votre plateforme (*Pilote .NET*, *Pilote Node.js*, *Pilote MongoDB*, *Pilote Java*, *Pilote Python*). Si votre pilote ou outil n'est pas répertorié, ne vous inquiétez pas, nous développons en permanence d'autres extraits de code de connexion. Veuillez laisser ci-dessous vos suggestions d'amélioration et consultez la rubrique [Obtenir les informations de chaîne de connexion du compte](#GetCustomConnection) pour apprendre à concevoir votre propre connexion.
5. Copiez et collez l’extrait de code dans votre application MongoDB, et vous voilà prêt à démarrer.

    ![Capture d'écran du panneau Démarrage rapide](./media/documentdb-connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Obtenir la chaîne de connexion MongoDB à personnaliser
1. Dans un navigateur Internet, connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le panneau **Azure Cosmos DB**, sélectionnez le compte d’API MongoDB. 
3. Dans la barre de **navigation de gauche** du panneau Compte, cliquez sur **Chaîne de connexion**. 
4. Le panneau **Informations de chaîne de connexion** s’ouvre ; il contient toutes les informations nécessaires pour vous connecter au compte à l’aide d’un pilote MongoDB, y compris une chaîne de connexion préalablement construite.

    ![Capture d’écran du panneau Chaîne de connexion](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Exigences relatives à la chaîne de connexion
> [!Important]
> Azure Cosmos DB obéit à des normes et à des exigences strictes en matière de sécurité. Les comptes Azure Cosmos DB requièrent une authentification et une communication sécurisée via **SSL**.
>
>

Il est important de noter qu’Azure Cosmos DB prend en charge le format d’URI de chaîne de connexion MongoDB standard, sous réserve que quelques exigences spécifiques soient satisfaites : les comptes Azure Cosmos DB requièrent une authentification et une communication sécurisée via SSL.  Le format de chaîne de connexion est donc :

    mongodb://username:password@host:port/[database]?ssl=true

Les valeurs de cette chaîne sont disponibles dans le panneau Chaîne de connexion illustré ci-dessus.

* Nom d’utilisateur (obligatoire)
  * Un nom de compte Azure Cosmos DB
* Mot de passe (obligatoire)
  * Un mot de passe de compte Azure Cosmos DB
* Hôte (obligatoire)
  * Un nom de domaine complet de compte Azure Cosmos DB
* Port (obligatoire)
  * 10250
* Base de données (facultatif)
  * La base de données par défaut utilisée par la connexion (si aucune base de données n’est fournie, la base de données par défaut est « test »)
* ssl = true (obligatoire)

Par exemple, observez le compte décrit dans les informations de chaîne de connexion ci-dessus.  Exemple de chaîne de connexion valide :

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@anhohmongo.documents.azure.com:10250/mydatabase?ssl=true

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [utiliser MongoChef](documentdb-mongodb-mongochef.md) avec un compte Azure Cosmos DB : API pour MongoDB.
* Explorez les [exemples](documentdb-mongodb-samples.md) d’Azure Cosmos DB : API pour MongoDB.

