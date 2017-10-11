---
title: "Utiliser mongoimport et mongorestore avec l’API Azure Cosmos DB pour MongoDB | Microsoft Docs"
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
ms.date: 06/12/2017
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: 1555f13c3ea88b61be0ea240b51218b83f6f9724
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2017
---
# <a name="azure-cosmos-db-import-mongodb-data"></a>Azure Cosmos DB : importer des données MongoDB 

Pour migrer des données de MongoDB vers un compte Azure Cosmos DB en vue d’une utilisation avec l’API pour MongoDB, vous devez :

* télécharger *mongoimport.exe* ou *mongorestore.exe* depuis le [centre de téléchargement MongoDB](https://www.mongodb.com/download-center) ;
* Obtenir votre [API pour la chaîne de connexion MongoDB](connect-mongodb-account.md).

Si vous importez des données à partir de MongoDB et planifiez de les utiliser avec Azure Cosmos DB, vous devez avoir recours à [l’outil de migration de données](import-data.md) pour importer les données.

Ce didacticiel décrit les tâches suivantes :

> [!div class="checklist"]
> * Récupération de votre chaîne de connexion
> * Importation des données MongoDB à l’aide de mongoimport
> * Importation des données MongoDB à l’aide de mongorestore

## <a name="prerequisites"></a>Composants requis

* Augmentez le débit : la durée de la migration des données dépend de la quantité de débit que vous définissez pour vos collections. Veillez à augmenter le débit pour les migrations de données plus importantes. Une fois que vous avez effectué la migration, diminuez le débit pour réduire les coûts. Pour plus d’informations sur l’augmentation du débit dans le [portail Azure](https://portal.azure.com), consultez les [niveaux de performances et niveaux tarifaires dans Azure Cosmos DB](performance-levels.md).

* Activez SSL : Azure Cosmos DB obéit à des normes et à des exigences strictes en matière de sécurité. Veillez à activer SSL lorsque vous interagissez avec votre compte. Les procédures décrites dans la suite de cet article incluent l’activation du protocole SSL pour mongoimport et mongorestore.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Recherche des informations de votre chaîne de connexion (hôte, port, nom d’utilisateur et mot de passe)

1. Dans le volet de gauche du [portail Azure](https://portal.azure.com), cliquez sur l’entrée **Azure Cosmos DB**.
2. Dans le volet **Abonnements**, sélectionnez le nom de votre compte.
3. Dans le panneau **Chaîne de connexion**, cliquez sur **Chaîne de connexion**.  
Le volet droit contient toutes les informations dont vous avez besoin pour vous connecter à votre compte.

    ![Panneau Chaîne de connexion](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-the-api-for-mongodb-by-using-mongoimport"></a>Importer des données dans l’API pour MongoDB avec mongoimport

Pour importer des données dans votre compte Azure Cosmos DB, utilisez le modèle suivant. Renseignez *l’hôte*, *le nom d’utilisateur*, et *le mot de passe* avec les valeurs qui sont spécifiques à votre compte.  

Modèle :

    mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Exemple :  

    mongoimport.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-the-api-for-mongodb-by-using-mongorestore"></a>Importer des données dans l’API pour MongoDB avec mongorestore

Pour restaurer des données dans votre API pour compte MongoDB, utilisez le modèle suivant afin d’exécuter l’importation. Renseignez *l’hôte*, *le nom d’utilisateur*, et *le mot de passe* avec les valeurs qui sont spécifiques à votre compte.

Modèle :

    mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Exemple :

    mongorestore.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
    
## <a name="guide-for-a-successful-migration"></a>Guide pour une migration réussie

1. Créez au préalable vos collections et mettez-les à l’échelle :
        
    * Par défaut, Azure Cosmos DB approvisionne une nouvelle collection MongoDB avec 1 000 unités de requête (RU). Avant de commencer la migration à l’aide de mongoimport, mongorestore ou mongomirror, créez au préalable l’ensemble des collections à partir du [portail Azure](https://portal.azure.com) ou à partir d’outils et de pilotes MongoDB. Si la taille de votre collection est supérieure à 10 Go, veillez à créer une [collection partitionnée](partition-data.md) avec une clé de partition appropriée.

    * Dans le [portail Azure](https://portal.azure.com), augmentez le débit de vos collections à partir de 1 000 RU pour une collection à partition unique et de 2 500 RU pour une collection partitionnée uniquement pour la migration. Si le débit est plus élevé, vous pouvez éviter la limitation et procéder à une migration plus rapide. Avec une facturation à l’heure dans Azure Cosmos DB, vous pouvez réduire immédiatement le débit à l’issue de la migration afin de réduire les coûts.

2. Calculez les frais approximatifs d’unités de requête pour une écriture de document unique :

    a. Connectez-vous à votre base de données Azure Cosmos DB MongoDB à partir de l’interpréteur de commandes MongoDB. Pour plus d’informations, voir [Connecter une application MongoDB à Azure Cosmos DB](connect-mongodb-account.md).
    
    b. Exécutez un exemple de commande d’insertion en utilisant l’un des exemples de documents de l’interpréteur de commandes MongoDB :
    
        ```db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })```
        
    c. Exécutez ```db.runCommand({getLastRequestStatistics: 1})```. Vous recevez une réponse semblable à celle-ci :
     
        ```
        globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
        {
            "_t": "GetRequestStatisticsResponse",
            "ok": 1,
            "CommandName": "insert",
            "RequestCharge": 10,
            "RequestDurationInMilliSeconds": NumberLong(50)
        }
        ```
        
    d. Prenez note de frais de requête.
    
3. Déterminez la latence de votre machine au service cloud Azure Cosmos DB :
    
    a. Activez la journalisation détaillée à partir de l’interpréteur de commandes MongoDB à l’aide de cette commande : ```setVerboseShell(true)```
    
    b. Exécutez une requête simple dans la base de données : ```db.coll.find().limit(1)```. Vous recevez une réponse semblable à celle-ci :

        ```
        Fetched 1 record(s) in 100(ms)
        ```
        
4. Supprimez le document inséré avant la migration pour vous assurer de l’absence de documents en double. Vous pouvez supprimer des documents à l’aide de cette commande : ```db.coll.remove({})```

5. Calculez les valeurs *batchSize* et *numInsertionWorkers* approximatives :

    * Pour la valeur *batchSize*, divisez le total des unités RU approvisionnées par les unités RU consommées par l’écriture de votre document unique à l’étape 3.
    
    * Si la valeur *batchSize* calculée est inférieure ou égale à 24, utilisez-la comme valeur *batchSize*.
    
    * Si la valeur *batchSize* calculée est supérieure à 24, définissez la valeur *batchSize* sur 24.
    
    * Pour *numInsertionWorkers*, utilisez l’équation suivante : *numInsertionWorkers = (débit approvisionné * latence en secondes) / (taille du lot * RU consommées pour une seule écriture)*.
        
    |Propriété|Valeur|
    |--------|-----|
    |batchSize| 24 |
    |RU approvisionnées | 10000 |
    |Latence | 0,100 s |
    |RU facturée pour 1 écriture de document | 10 unités de requête |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (10 000 RU x 0,1 s) / (24 x 10 RU) = 4,1666*

6. Exécutez la commande de migration finale :

   ```
   mongoimport.exe --host anhoh-mongodb.documents.azure.com:10255 -u anhoh-mongodb -p wzRJCyjtLPNuhm53yTwaefawuiefhbauwebhfuabweifbiauweb2YVdl2ZFNZNv8IU89LqFVm5U0bw== --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez passer au didacticiel suivant et découvrir comment interroger les données MongoDB à l’aide d’Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Comment interroger les données MongoDB ?](../cosmos-db/tutorial-query-mongodb.md)
