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
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 221f1b9f0985bbaf0553f6ca01f0f048b9976315
ms.lasthandoff: 03/28/2017


---
# <a name="create-a-new-logic-app-connecting-saas-services"></a>Créer une application logique connectant les services SaaS
Cette rubrique vous explique comment vous pouvez vous familiariser avec [Azure Logic Apps](logic-apps-what-are-logic-apps.md)en quelques minutes. Nous allons étudier un workflow simple qui vous permet d’envoyer des tweets intéressants à votre adresse e-mail.

Pour activer ce scénario, vous avez besoin des éléments suivants :

* Un abonnement Azure
* un compte Twitter ;
* Un compte Outlook.com ou Office 365 Outlook

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>Créer une application logique pour vous envoyer des tweets par courrier électronique

1. Connectez-vous au [portail Azure](https://portal.azure.com). 

2. Dans le menu de gauche, choisissez **Nouveau** > **Intégration d’entreprise** > **Application logique**.

    Vous pouvez également sélectionner **Nouveau**, puis dans la zone de recherche, tapez `logic app`, puis appuyez sur Entrée. Choisissez **Application logique** > **Créer**.

3. Entrez un nom pour votre application logique, sélectionnez votre abonnement Azure, créez ou sélectionnez un groupe de ressources Azure, sélectionnez un emplacement, puis choisissez **Créer**.

    Si vous sélectionnez **Épingler au tableau de bord**, l’application logique s’ouvre automatiquement après le déploiement.

4. Quand vous ouvrez votre application logique pour la première fois, vous pouvez sélectionner un modèle à partir duquel démarrer.
Pour l’instant, cliquez sur **Blank Logic App** (Application logique vide) pour créer une application de toutes pièces. 

5. Le premier élément que vous devez créer est le déclencheur. Il s’agit de l’événement qui démarre votre application logique. Dans la zone de recherche, recherchez **twitter**, puis sélectionnez **Lors de la publication d’un nouveau tweet**. Connectez-vous avec le nom d’utilisateur et le mot de passe de votre compte Twitter.

6. Ensuite, tapez un terme de recherche pour déclencher votre application logique.

   ![Recherche Twitter](media/logic-apps-create-a-logic-app/twittersearch.png)

    Les champs **Fréquence** et **Intervalle** déterminent la fréquence à laquelle votre application logique recherche de nouveaux tweets et renvoie tous les tweets pendant cet intervalle de temps.

7. Sélectionnez **Nouvelle étape**, puis choisissez **Ajouter une action** ou **Ajouter une condition**.

    Lorsque vous sélectionnez **Ajouter une action**, vous pouvez rechercher les [connecteurs disponibles](../connectors/apis-list.md) pour choisir une action. 

8. Dans la zone de recherche, recherchez **outlook**, puis sélectionnez **Envoyer un message électronique** pour envoyer un e-mail à partir de votre compte Outlook vers une adresse e-mail spécifiée.

   ![Actions](media/logic-apps-create-a-logic-app/actions.png)

9. À présent, vous devez renseigner les paramètres du message électronique : 

   ![Paramètres](media/logic-apps-create-a-logic-app/parameters.png)

10. Pour finir, vous pouvez sélectionner **Enregistrer** pour générer votre application logique.

## <a name="manage-your-logic-app-after-creation"></a>Gérer votre application logique après la création

Votre application logique est maintenant opérationnelle. Elle recherchera à intervalles réguliers des tweets contenant le terme de recherche que vous avez entré. Si elle trouve un tweet correspondant, elle vous enverra un e-mail. Pour finir, vous allez découvrir comment désactiver l'application ou analyser ses performances.

1. Accédez au [portail Azure](https://portal.azure.com).

2. Dans le menu de gauche, cliquez sur **Plus de services**. Sous **Intégration d’entreprise**, choisissez **Applications logiques**. Sélectionnez votre application logique.

    *    Pour afficher l’état de votre application, l’historique d’exécution et des informations générales, choisissez **Vue d’ensemble** dans le menu de l’application logique. Si vous ne trouvez pas les données que vous attendiez, choisissez **Actualiser** dans la barre de commandes.

    *    Pour modifier votre application, dans le menu de l’application logique, choisissez **Concepteur d’application logique**.

    *    Pour désactiver votre application de façon temporaire, choisissez **Vue d’ensemble** dans le menu de l’application logique. Choisissez **Désactiver** dans la barre de commandes.

    *    Pour supprimer votre application, choisissez **Vue d’ensemble** dans le menu de l’application logique. 
    Choisissez **Supprimer** dans la barre de commandes. Entrez le nom de votre application logique et choisissez **Supprimer**.

En moins de cinq minutes, vous avez réussi à configurer une application logique simple exécutée dans le cloud. Pour en savoir plus sur l’utilisation des fonctionnalités des applications logiques, consultez [Utiliser les fonctionnalités des applications logiques]. Pour en savoir plus sur les définitions d'application logique, consultez la rubrique [Créer des définitions d'application logique](../logic-apps/logic-apps-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Utiliser les fonctionnalités des applications logiques]: logic-apps-create-a-logic-app.md
