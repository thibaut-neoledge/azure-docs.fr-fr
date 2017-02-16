---
title: Utiliser Robomongo avec un compte Azure DocumentDB prenant en charge le protocole MongoDB | Microsoft Docs
description: "Découvrez comment utiliser Robomongo avec un compte DocumentDB prenant en charge le protocole MongoDB (actuellement disponible en version préliminaire)."
keywords: robomongo
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
ms.date: 11/29/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: a9d78597f708020eaec3fc03d8c8fc2652bddb50
ms.openlocfilehash: 6a7fb7052700bdeed5a3ca43da85304bba4230d8


---
# <a name="use-robomongo-with-a-documentdb-account-with-protocol-support-for-mongodb"></a>Utiliser Robomongo avec un compte DocumentDB prenant en charge le protocole MongoDB
Pour vous connecter à un compte Azure DocumentDB prenant en charge le protocole MongoDB à l’aide de Robomongo, vous devez :

* Télécharger et installer [Robomongo](https://robomongo.org/)
* Posséder un compte DocumentDB avec les informations de la [chaîne de connexion](documentdb-connect-mongodb-account.md) pour la prise en charge du protocole MongoDB

## <a name="connect-using-robomongo"></a>Connexion à l’aide de Robomongo
Pour ajouter votre compte DocumentDB prenant en charge le protocole MongoDB aux connexions Robomongo MongoDB, procédez comme suit.

1. Récupérez votre compte DocumentDB avec les informations de connexion pour la prise en charge du protocole MongoDB à l’aide de [ces instructions](documentdb-connect-mongodb-account.md).

    ![Capture d’écran du panneau Chaîne de connexion](./media/documentdb-mongodb-robomongo/connectionstringblade.png)
2. Exécutez *Robomongo.exe*

3. Cliquez sur le bouton de connexion sous **Fichier** pour gérer vos connexions. Ensuite, cliquez sur **Créer** dans la fenêtre **Connexions MongoDB**, ce qui ouvre la fenêtre **Paramètres de connexion**.

4. Dans la fenêtre **Paramètres de connexion**, choisissez un nom. Ensuite, recherchez **Hôte** et **Port** dans vos informations de connexion à l’étape 1 et entrez-les dans les champs **Adresse** et **Port**, respectivement.

    ![Capture d’écran de gestion des connexions avec Robomongo](./media/documentdb-mongodb-robomongo/manageconnections.png)
5. Dans l’onglet **Authentification**, cliquez sur **Effectuer l’authentification**. Entrez ensuite votre base de données (la valeur par défaut est *Admin*), le **Nom d’utilisateur** et le **Mot de passe**.
Le **Nom d’utilisateur** et le **Mot de passe** figurent tous deux dans vos informations de connexion à l’étape 1.

    ![Capture d’écran de l’onglet authentification de Robomongo](./media/documentdb-mongodb-robomongo/authentication.png)
6. Dans l’onglet **SSL**, cochez **Utiliser le protocole SSL**, puis modifiez la **Méthode d’authentification** sur **Certificat auto-signé**.

    ![Capture d’écran de l’onglet SSL de Robomongo](./media/documentdb-mongodb-robomongo/SSL.png)
7. Enfin, cliquez sur **Test** pour vérifier que vous êtes en mesure de vous connecter, puis sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez des [exemples](documentdb-mongodb-samples.md)de prise en charge du protocole MongoDB dans DocumentDB.



<!--HONumber=Dec16_HO1-->


