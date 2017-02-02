---
title: "Migration de données vers un compte Azure DocumentDB prenant en charge le protocole MongoDB | Microsoft Docs"
description: "Découvrez comment utiliser mongoimport et mongorestore pour importer des données dans un compte DocumentDB prenant en charge le protocole MongoDB (actuellement disponible en version préliminaire)."
keywords: migrate
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 2dc98400ed4eaa2263d73f0718f5eaee260d48f5
ms.openlocfilehash: 80665bc84948696b05b04536a066ebebb5a81e89


---
# <a name="migrate-data-to-documentdb-with-protocol-support-for-mongodb"></a>Migration de données vers DocumentDB avec prise en charge du protocole MongoDB
Pour migrer des données vers un compte Azure DocumentDB prenant en charge le protocole MongoDB, vous devez

* télécharger *mongoimport.exe* ou *mongorestore.exe* depuis le [centre de téléchargement MongoDB](https://www.mongodb.com/download-center) ;
* obtenir votre [prise en charge de DocumentDB pour la chaîne de connexion MongoDB](documentdb-connect-mongodb-account.md).

## <a name="before-you-begin"></a>Avant de commencer

* Augmentez le débit : la durée de la migration des données dépend de la quantité de débit que vous définissez pour vos collections. Veillez à augmenter le débit pour les migrations de données plus importantes. Une fois que vous avez effectué la migration, diminuez le débit pour réduire les coûts. Pour plus d’informations sur l’augmentation du débit dans le [portail Azure](https://portal.azure.com), consultez [Niveaux de performances et niveaux tarifaires dans DocumentDB](documentdb-performance-levels.md).

* Activez SSL : DocumentDB obéit à des normes et à des exigences strictes en matière de sécurité. Veillez à activer SSL lorsque vous interagissez avec votre compte. Les procédures décrites dans le reste de cet article incluent l’activation de SSL pour *mongoimport* et *mongorestore*.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Recherche des informations de votre chaîne de connexion (hôte, port, nom d’utilisateur et mot de passe)

1. Dans le volet de gauche du [portail Azure](https://portal.azure.com), cliquez sur l’entrée NoSQL **(DocumentDB)**.
2. Dans le volet **Abonnements**, sélectionnez le nom de votre compte.
3. Dans le panneau **Chaîne de connexion**, cliquez sur **Chaîne de connexion**.  
Le volet droit contient toutes les informations dont vous avez besoin pour vous connecter à votre compte.

    ![Le panneau « Chaîne de connexion »](./media/documentdb-mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-documentdb-with-protocol-support-for-mongodb-with-mongoimport"></a>Importation de données vers DocumentDB avec prise en charge du protocole MongoDB avec mongoimport

Pour importer des données dans votre compte DocumentDB, utilisez le modèle suivant pour exécuter l’importation. Renseignez *l’hôte*, *le nom d’utilisateur*, et *le mot de passe* avec les valeurs qui sont spécifiques à votre compte.  

Modèle :

    mongoimport.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Exemple :  

    mongoimport.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-documentdb-with-protocol-support-for-mongodb-with-mongorestore"></a>Importation de données vers DocumentDB avec prise en charge du protocole MongoDB avec mongorestore

Pour restaurer des données dans votre compte DocumentDB, utilisez le modèle suivant pour exécuter l’importation. Renseignez *l’hôte*, *le nom d’utilisateur*, et *le mot de passe* avec les valeurs spécifiques à votre compte.

Modèle :

    mongorestore.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Exemple :

    mongorestore.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus, découvrez des [exemples de prise en charge du protocole MongoDB dans DocumentDB](documentdb-mongodb-samples.md).



<!--HONumber=Dec16_HO3-->


