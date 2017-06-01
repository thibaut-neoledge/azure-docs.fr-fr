---
title: "Utiliser mongoimport et mongorestore avec l’API Azure Cosmos DB pour MongoDB | Microsoft Docs"
description: "Découvrez comment utiliser mongoimport et mongorestore pour importer des données dans un compte API pour MongoDB"
keywords: mongoimport, mongorestore
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: ab4fb39f8784a2d6aa956056a30649aab1d08487
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017


---

# <a name="azure-cosmos-db-how-to-import-mongodb-data"></a>Azure Cosmos DB : comment importer des données MongoDB ? 

Pour migrer des données de MongoDB vers un compte Azure Cosmos DB en vue d’une utilisation avec l’API MongoDB, vous devez :

* télécharger *mongoimport.exe* ou *mongorestore.exe* depuis le [centre de téléchargement MongoDB](https://www.mongodb.com/download-center) ;
* Obtenir votre [API pour la chaîne de connexion MongoDB](connect-mongodb-account.md).

Si vous importez des données à partir de MongoDB et souhaitez les utiliser avec l’API DocumentDB, vous devez avoir recours à l’outil de migration de données pour importer les données. Pour plus d’informations, consultez [Outil de migration de données](import-data.md).

Ce didacticiel décrit les tâches suivantes :

> [!div class="checklist"]
> * Récupération de votre chaîne de connexion
> * Importation des données MongoDB à l’aide de mongoimport
> * Importation des données MongoDB à l’aide de mongorestore

## <a name="prerequisites"></a>Composants requis

* Augmentez le débit : la durée de la migration des données dépend de la quantité de débit que vous définissez pour vos collections. Veillez à augmenter le débit pour les migrations de données plus importantes. Une fois que vous avez effectué la migration, diminuez le débit pour réduire les coûts. Pour plus d’informations sur l’augmentation du débit dans le [portail Azure](https://portal.azure.com), consultez les [niveaux de performances et niveaux tarifaires dans Azure Cosmos DB](performance-levels.md).

* Activez SSL : Azure Cosmos DB obéit à des normes et à des exigences strictes en matière de sécurité. Veillez à activer SSL lorsque vous interagissez avec votre compte. Les procédures décrites dans le reste de cet article incluent l’activation de SSL pour *mongoimport* et *mongorestore*.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Recherche des informations de votre chaîne de connexion (hôte, port, nom d’utilisateur et mot de passe)

1. Dans le volet de gauche du [portail Azure](https://portal.azure.com), cliquez sur l’entrée **Azure Cosmos DB**.
2. Dans le volet **Abonnements**, sélectionnez le nom de votre compte.
3. Dans le panneau **Chaîne de connexion**, cliquez sur **Chaîne de connexion**.  
Le volet droit contient toutes les informations dont vous avez besoin pour vous connecter à votre compte.

    ![Le panneau « Chaîne de connexion »](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-api-for-mongodb-with-mongoimport"></a>Importer des données dans l’API pour MongoDB avec mongoimport

Pour importer des données dans votre compte Azure Cosmos DB, utilisez le modèle suivant afin d’exécuter l’importation. Renseignez *l’hôte*, *le nom d’utilisateur*, et *le mot de passe* avec les valeurs qui sont spécifiques à votre compte.  

Modèle :

    mongoimport.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Exemple :  

    mongoimport.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-api-for-mongodb-with-mongorestore"></a>Importer des données dans l’API pour MongoDB avec mongorestore

Pour restaurer des données dans votre API pour compte MongoDB, utilisez le modèle suivant afin d’exécuter l’importation. Renseignez *l’hôte*, *le nom d’utilisateur*, et *le mot de passe* avec les valeurs spécifiques à votre compte.

Modèle :

    mongorestore.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Exemple :

    mongorestore.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez effectué les tâches suivantes :

> [!div class="checklist"]
> * Récupération de votre chaîne de connexion
> * Importation des données MongoDB à l’aide de mongoimport
> * Importation des données MongoDB à l’aide de mongorestore

Vous pouvez maintenant passer à l’étape suivante du didacticiel et découvrir comment interroger les données MongoDB à l’aide d’Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Comment interroger les données MongoDB ?](../cosmos-db/tutorial-query-mongodb.md)

