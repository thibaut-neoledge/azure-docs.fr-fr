---
title: "Chaîne de connexion MongoDB pour le compte DocumentDB | Microsoft Docs"
description: "Découvrez comment connecter votre application MongoDB à un compte Azure DocumentDB à l’aide d’une chaîne de connexion MongoDB."
keywords: "chaîne de connexion mongodb"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: e36f7375-9329-403b-afd1-4ab49894f75e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 91474f61ab724d3fd7a70c51dcd097fade8953dd
ms.openlocfilehash: 3789079412932d56be1c9697fc23c56de5afba6f


---

# <a name="connect-a-mongodb-app-to-an-documentdb-account-using-a-mongodb-connection-string"></a>Connecter une application MongoDB à un compte DocumentDB à l’aide d’une chaîne de connexion MongoDB
Découvrez comment connecter votre application MongoDB à un compte Azure DocumentDB à l’aide d’une chaîne de connexion MongoDB. En connectant votre application MongoDB à une base de données Azure DocumentDB, vous pouvez utiliser une base de données DocumentDB comme magasin de données pour votre application MongoDB. 

Ce didacticiel fournit deux façons de récupérer les informations de la chaîne de connexion :

- [La méthode Démarrage rapide](#QuickStartConnection), à utiliser avec les pilotes .NET, Node.js, MongoDB Shell, Java et Python.
- [La méthode de chaîne de connexion personnalisée](#GetCustomConnection), à utiliser avec d’autres pilotes.

## <a name="prerequisites"></a>Composants requis

- Un compte Azure. Si vous ne possédez pas de compte Azure, vous pouvez créer un [compte Azure gratuit](https://azure.microsoft.com/free/) dès maintenant. 
- Un compte DocumentDB. Pour obtenir des instructions, consultez [Créer un compte DocumentDB à utiliser avec les applications MongoDB](documentdb-create-mongodb-account.md).

## <a name="a-idquickstartconnectionaget-the-mongodb-connection-string-using-the-quick-start"></a><a id="QuickStartConnection"></a>Obtenir la chaîne de connexion MongoDB à l’aide de Démarrage rapide
1. Dans un navigateur Internet, connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le panneau **NoSQL (DocumentDB)**, sélectionnez le compte DocumentDB avec le protocole de prise en charge pour MongoDB. 
3. Dans la barre de **navigation de gauche** du panneau Compte, cliquez sur **Démarrage rapide**. 
4. Choisissez votre plateforme (*Pilote .NET*, *Pilote Node.js*, *Pilote MongoDB*, *Pilote Java*, *Pilote Python*). Si votre pilote ou outil n'est pas répertorié, ne vous inquiétez pas, nous développons en permanence d'autres extraits de code de connexion. Veuillez laisser ci-dessous vos suggestions d'amélioration et consultez la rubrique [Obtenir les informations de chaîne de connexion du compte](#GetCustomConnection) pour apprendre à concevoir votre propre connexion.
5. Copiez et collez l’extrait de code dans votre application MongoDB, et vous voilà prêt à démarrer.

    ![Capture d'écran du panneau Démarrage rapide](./media/documentdb-connect-mongodb-account/QuickStartBlade.png)

## <a name="a-idgetcustomconnectiona-get-the-mongodb-connection-string-to-customize"></a><a id="GetCustomConnection"></a> Obtenir la chaîne de connexion MongoDB à personnaliser
1. Dans un navigateur Internet, connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le panneau **NoSQL (DocumentDB)**, sélectionnez le compte DocumentDB avec le protocole de prise en charge pour MongoDB. 
3. Dans la barre de **navigation de gauche** du panneau Compte, cliquez sur **Chaîne de connexion**. 
4. Le panneau **Informations de chaîne de connexion** s’ouvre ; il contient toutes les informations nécessaires pour vous connecter au compte à l’aide d’un pilote MongoDB, y compris une chaîne de connexion préalablement construite.

    ![Capture d’écran du panneau Chaîne de connexion](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Exigences relatives à la chaîne de connexion
> [!Important]
> DocumentDB obéit à des normes et des exigences strictes en matière de sécurité. Les comptes DocumentDB requièrent une authentification et une communication sécurisée via **SSL**.
>
>

Il est important de noter que DocumentDB prend en charge le format d’URI de chaîne de connexion MongoDB standard, sous réserve que quelques exigences spécifiques soient satisfaites : les comptes DocumentDB requièrent une authentification et une communication sécurisée via SSL.  Le format de chaîne de connexion est donc :

    mongodb://username:password@host:port/[database]?ssl=true

Les valeurs de cette chaîne sont disponibles dans le panneau Chaîne de connexion illustré ci-dessus.

* Nom d’utilisateur (obligatoire)
  * Nom du compte DocumentDB
* Mot de passe (obligatoire)
  * Mot de passe du compte DocumentDB
* Hôte (obligatoire)
  * Nom de domaine complet du compte DocumentDB
* Port (obligatoire)
  * 10250
* Base de données (facultatif)
  * La base de données par défaut utilisée par la connexion (si aucune base de données n’est fournie, la base de données par défaut est « test »)
* ssl = true (obligatoire)

Par exemple, observez le compte décrit dans les informations de chaîne de connexion ci-dessus.  Exemple de chaîne de connexion valide :

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@anhohmongo.documents.azure.com:10250/mydatabase?ssl=true

## <a name="next-steps"></a>Étapes suivantes
* Apprenez à [utiliser MongoChef](documentdb-mongodb-mongochef.md) avec un compte DocumentDB prenant en charge le protocole MongoDB.
* Découvrez des [exemples](documentdb-mongodb-samples.md)de prise en charge du protocole MongoDB dans DocumentDB.



<!--HONumber=Dec16_HO2-->


