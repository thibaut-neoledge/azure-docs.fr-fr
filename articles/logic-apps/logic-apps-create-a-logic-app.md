---
title: "Créer des flux de travail avec votre première application logique Azure | Microsoft Docs"
description: "Prise en main de la connexion des applications et des services SaaS avec votre première application logique"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: ce1e0cfe7c7f52d521ef3318376af75331323083
ms.openlocfilehash: 178a57624345a4b3e5d73e64ce4ccf81b8b90a88


---
# <a name="create-a-new-logic-app-connecting-saas-services"></a>Créer une application logique connectant les services SaaS
Cette rubrique vous explique comment vous pouvez vous familiariser avec [Azure Logic Apps](logic-apps-what-are-logic-apps.md)en quelques minutes. Nous allons étudier un workflow simple qui vous permet d’envoyer des tweets intéressants à votre adresse e-mail.

Pour activer ce scénario, vous avez besoin des éléments suivants :

* Un abonnement Azure
* un compte Twitter ;
* Une boîte aux lettres Outlook.com ou Office 365 hébergée

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>Créer une application logique pour vous envoyer des tweets par courrier électronique
1. Dans le [tableau de bord du Portail Azure](https://portal.azure.com), sélectionnez **Nouveau**. 
2. Dans la barre de recherche, recherchez « application logique », puis sélectionnez **Application logique**. Vous pouvez également sélectionner **Nouveau**, **Web + mobile**, puis **Application logique**. 
3. Entrez un nom pour votre application logique, sélectionnez un emplacement et un groupe de ressources, puis sélectionnez **Créer**.  Si vous sélectionnez **Épingler au tableau de bord** , l’application logique s’ouvre automatiquement une fois déployée.  
4. Après avoir ouvert votre application logique pour la première fois, vous pouvez sélectionner un modèle à partir duquel démarrer.  Pour l’instant, cliquez sur **Blank Logic App** (Application logique vide) pour créer une application de toutes pièces. 
5. Le premier élément que vous devez créer est le déclencheur.  Il s’agit de l’événement qui démarrera votre application logique.  Recherchez **twitter** dans la zone de recherche de déclencheur, puis sélectionnez cette application.
6. Ensuite, tapez un terme de recherche qui servira de déclencheur.  Les champs **Fréquence** et **Intervalle** déterminent la fréquence à laquelle votre application logique recherchera de nouveaux tweets (et renverra tous les tweets pendant cet intervalle de temps).
    ![Recherche Twitter](media/logic-apps-create-a-logic-app/twittersearch.png)
7. Sélectionnez le bouton **Nouvelle étape**, puis choisissez **Ajouter une action** ou **Ajouter une condition**
8. Lorsque vous sélectionnez **Ajouter une action**, vous pouvez effectuer une recherche parmi les [connecteurs disponibles](../connectors/apis-list.md) pour choisir une action. Par exemple, vous pouvez sélectionner **Outlook.com - Envoyer un message électronique** pour envoyer du courrier à partir d’une adresse outlook.com :  
    ![Actions](media/logic-apps-create-a-logic-app/actions.png)
9. À présent, vous devez renseigner les paramètres du message électronique :  ![Paramètres](media/logic-apps-create-a-logic-app/parameters.png)
10. Pour finir, vous pouvez sélectionner **Enregistrer** pour générer votre application logique.

## <a name="manage-your-logic-app-after-creation"></a>Gérer votre application logique après la création
Votre application logique est maintenant opérationnelle. Elle recherchera à intervalles réguliers des tweets contenant le terme de recherche que vous avez entré. Si elle trouve un tweet correspondant, elle vous enverra un e-mail. Pour finir, vous allez découvrir comment désactiver l'application ou analyser ses performances.

1. Accédez au [Portail Azure](https://portal.azure.com)
2. Cliquez sur **Parcourir** sur le côté gauche de l’écran, et sélectionnez **Applications logiques**.
3. Cliquez sur la nouvelle application logique que vous venez de créer pour afficher l'état actuel et des informations générales.
4. Pour modifier votre nouvelle application logique, cliquez sur **Modifier**.
5. Pour désactiver l’application, cliquez sur **Désactiver** dans la barre de commandes.
6. Visualisez les historiques d’exécution et de déclencheur pour surveiller les moments où votre application logique est en cours d’exécution.  Vous pouvez cliquer sur **Actualiser** pour visualiser les données les plus récentes.

En moins de cinq minutes, vous avez réussi à configurer une application logique simple exécutée dans le cloud. Pour en savoir plus sur l’utilisation des fonctionnalités des applications logiques, consultez [Utiliser les fonctionnalités des applications logiques]. Pour en savoir plus sur les définitions d'application logique, consultez la rubrique [Créer des définitions d'application logique](../logic-apps/logic-apps-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Utiliser les fonctionnalités des applications logiques]: logic-apps-create-a-logic-app.md



<!--HONumber=Jan17_HO4-->


