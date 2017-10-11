---
title: Utiliser MongoChef pour Azure Cosmos DB | Microsoft Docs
description: "Découvrez comment utiliser MongoChef avec un compte Azure Cosmos DB : API pour MongoDB"
keywords: MongoChef
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
ms.date: 05/23/2017
ms.author: anhoh
ms.openlocfilehash: 54c9799bd646b827f602e2ea2f9a15a4fc853f00
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="use-mongochef-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Utiliser MongoChef avec un compte Azure Cosmos DB : API pour MongoDB

Pour vous connecter à un compte Azure Cosmos DB : API pour MongoDB, vous devez :

* Télécharger et installer [MongoChef](http://3t.io/mongochef)
* Disposer des informations de [chaîne de connexion](connect-mongodb-account.md) de votre compte Azure Cosmos DB : API pour MongoDB

## <a name="create-the-connection-in-mongochef"></a>Créer la connexion dans MongoChef
Pour ajouter votre compte Azure Cosmos DB : API pour MongoDB au gestionnaire de connexions MongoChef, procédez comme suit.

1. Récupérez vos informations de connexion Azure Cosmos DB : API pour MongoDB à l’aide des instructions [ici](connect-mongodb-account.md).

    ![Capture d’écran du panneau Chaîne de connexion](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Cliquez sur **Connexion** pour ouvrir le gestionnaire de connexions, puis cliquez sur **Nouvelle connexion**.

    ![Capture d’écran du Gestionnaire de connexions MongoChef](./media/mongodb-mongochef/ConnectionManager.png)
3. Sous l’onglet **Serveur** de la fenêtre **Nouvelle connexion**, entrez l’HÔTE (FQDN) du compte Azure Cosmos DB : API pour MongoDB et le PORT.

    ![Capture d’écran de l’onglet Serveur du Gestionnaire de connexions MongoChef](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Dans la fenêtre **Nouvelle connexion**, sous l’onglet **Authentification**, choisissez le mode d’authentification **Standard (MONGODB-CR ou SCARM-SHA-1)** et entrez les NOM D’UTILISATEUR et MOT DE PASSE.  Acceptez la base de données d’authentification par défaut (admin) ou indiquez votre propre valeur.

    ![Capture d’écran de l’onglet Authentification du Gestionnaire de connexions MongoChef](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Dans la fenêtre **Nouvelle connexion**, sous l’onglet **SSL**, cochez la case **Utiliser le protocole SSL pour se connecter** et sélectionnez **Accepter les certificats SSL auto-signés**.

    ![Capture d’écran de l’onglet SSL du Gestionnaire de connexions MongoChef](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Cliquez sur le bouton **Tester la connexion** pour valider les informations de connexion, cliquez sur **OK** pour revenir à la fenêtre Nouvelle connexion, puis cliquez sur **Enregistrer**.

    ![Capture d’écran de la fenêtre Tester la connexion de MongoChef](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-mongochef-to-create-a-database-collection-and-documents"></a>Utiliser MongoChef pour créer une base de données, une collection et des documents
Pour créer une base de données, une collection et des documents à l’aide de MongoChef, procédez comme suit.

1. Dans le **Gestionnaire de connexions**, sélectionnez la connexion et cliquez sur **Connexion**.

    ![Capture d’écran du Gestionnaire de connexions MongoChef](./media/mongodb-mongochef/ConnectToAccount.png)
2. Cliquez avec le bouton droit sur l’hôte et choisissez **Ajouter une base de données**.  Spécifiez un nom de base de données, puis cliquez sur **OK**.

    ![Capture d’écran de l’option Ajouter une base de données de MongoChef](./media/mongodb-mongochef/AddDatabase1.png)
3. Cliquez avec le bouton droit sur la base de données et sélectionnez **Ajouter une collection**.  Spécifiez un nom de collection et cliquez sur **Créer**.

    ![Capture d’écran de l’option Ajouter une collection de MongoChef](./media/mongodb-mongochef/AddCollection.png)
4. Cliquez sur l’élément de menu **Collection**, puis cliquez sur **Ajouter un document**.

    ![Capture d’écran de l’option Ajouter un document de MongoChef](./media/mongodb-mongochef/AddDocument1.png)
5. Dans la boîte de dialogue Ajouter un document, collez les éléments suivants, puis cliquez sur **Ajouter un document**.

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. Ajoutez un autre document, cette fois avec le contenu suivant.

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. Exécutez un exemple de requête. Par exemple, recherchez des familles portant le nom « Andersen » en retournant les champs parents et état.

    ![Capture d’écran des résultats de requête MongoChef](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Étapes suivantes
* Explorez les [exemples](mongodb-samples.md) d’Azure Cosmos DB : API pour MongoDB.
