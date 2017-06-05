---
title: "Créer votre premier flux de travail entre des applications cloud et des services cloud : Azure Logic Apps| Microsoft Docs"
description: "Automatisez les processus d’entreprise dans le cas de scénarios d’intégration des systèmes et des Applications d’Entreprise (IAE) en créant et en exécutant des flux de travail dans Azure Logic Apps."
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
keywords: "flux de travail, applications cloud, services cloud, processus d’entreprise, intégration de systèmes, intégration d’applications d’entreprise, IAE"
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/31/2017
ms.author: LADocs; jehollan; estfan
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 204bf123509729b60b55c306050cef54aa7fecc5
ms.contentlocale: fr-fr
ms.lasthandoff: 05/17/2017

---

# <a name="create-your-first-logic-app-workflow-to-automate-processes-between-cloud-apps-and-cloud-services"></a>Créer votre premier flux de travail d’application logique pour automatiser les processus entre les applications cloud et les services cloud

Vous pouvez automatiser des processus métier plus facilement et rapidement lorsque vous créez et exécutez des flux de travail et ce, sans avoir besoin d’écrire la plus petite ligne de code, grâce à [Azure Logic Apps](logic-apps-what-are-logic-apps.md). Le premier exemple indique comment créer un flux de travail d’application logique de base, qui vérifie la présence éventuelle de nouveau contenu dans un flux RSS, sur un site web. Lorsque de nouveaux éléments s’affichent dans le flux du site web, l’application logique envoie un e-mail depuis un compte Outlook ou Gmail.

Pour créer et exécuter une application logique, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez [commencer par créer gratuitement un compte Azure](https://azure.microsoft.com/free/). Sinon, vous pouvez souscrire à un [abonnement de type paiement à l’utilisation](https://azure.microsoft.com/pricing/purchase-options/).

  Votre abonnement Azure est utilisé pour la facturation relative à l’utilisation des applications logiques. Découvrez le fonctionnement du [contrôle de l’utilisation](../logic-apps/logic-apps-pricing.md) et de la [tarification](https://azure.microsoft.com/pricing/details/logic-apps) pour les applications logiques Azure.

En outre, cet exemple nécessite les éléments suivants :

* Un compte Gmail, Office 365 Outlook ou Outlook.com

    > [!TIP]
    > Si vous disposez d’un [compte Microsoft](https://account.microsoft.com/account) personnel, vous possédez un compte Outlook.com. Sinon, si vous disposez d’un compte Azure professionnel ou scolaire, vous possédez un compte **Office 365 Outlook**.

* Un lien vers le flux RSS d’un site web. Cet exemple utilise le [flux RSS pour les témoignages du site web CNN.com](http://rss.cnn.com/rss/cnn_topstories.rss) : `http://rss.cnn.com/rss/cnn_topstories.rss`

## <a name="add-a-trigger-that-starts-your-workflow"></a>Ajouter un déclencheur qui démarre votre flux de travail

Un [*déclencheur*](./logic-apps-what-are-logic-apps.md#logic-app-concepts) est un événement qui démarre le flux de travail de votre application logique. Il s’agit du premier élément requis par votre application logique.

1. Connectez-vous au [portail Azure](https://portal.azure.com "portail Azure").

2. Dans le menu de gauche, choisissez **Nouveau** > **Intégration d’entreprise** > **Application logique**, comme indiqué ici :

     ![portail Azure, nouveau, intégration d’entreprise, application logique](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

   > [!TIP]
   > Vous pouvez également sélectionner **Nouveau**, puis dans la zone de recherche, tapez `logic app`, puis appuyez sur Entrée. Ensuite, choisissez **Application logique** > **Créer**.

3. Nommez votre application logique et sélectionnez votre abonnement Azure. Créez ou sélectionnez un groupe de ressources Azure, qui permet d’organiser et de gérer des ressources Azure liées. Enfin, sélectionnez l’emplacement du centre de données pour l’hébergement de votre application logique. Lorsque vous êtes prêt, choisissez **Épingler au tableau de bord**, puis **Créer**.

     ![Détails de l’application logique](media/logic-apps-create-a-logic-app/logic-app-settings.png)

   > [!NOTE]
   > Lorsque vous sélectionnez l’option **Épingler au tableau de bord**, votre application logique s’affiche dans le tableau de bord Azure après le déploiement et s’ouvre automatiquement. Si votre application logique ne figure pas dans le tableau de bord, accédez à la mosaïque **Toutes les ressources**, choisissez **En savoir plus**, puis sélectionnez votre application logique. Vous pouvez également cliquer sur **Plus de services** dans le menu de gauche. Sous **Intégration d’entreprise**, choisissez **Applications logiques** et sélectionnez votre application logique.

4. Lorsque vous ouvrez votre application logique pour la première fois, le Concepteur d’application logique affiche les modèles que vous pouvez utiliser pour commencer. Pour l’instant, choisissez **Application logique vide**, afin de développer votre application logique à partir de zéro.

    Le Concepteur d’application logique s’ouvre et affiche les services et *déclencheurs* que vous pouvez utiliser dans votre application logique.

5. Dans la zone de recherche, saisissez `RSS`, puis sélectionnez le déclencheur suivant : **RSS - Lors de la publication d’un élément de flux**. 

    ![Déclencheur RSS](media/logic-apps-create-a-logic-app/rss-trigger.png)

6. Indiquez le lien vers le flux RSS du site web dont vous souhaitez effectuer un suivi. 

     Vous pouvez également modifier la **fréquence** et l’**intervalle**. 
     Ces paramètres déterminent la fréquence à laquelle votre application logique recherche de nouveaux éléments et renvoie tous les éléments détectés pendant cet intervalle de temps.

     Pour les besoins de cet exemple, nous allons rechercher tous les jours les témoignages publiés sur le site web CNN.

     ![Définir un déclencheur avec le flux RSS, la fréquence et l’intervalle](media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

7. Enregistrez votre travail pour commencer (dans la barre de commandes du concepteur, choisissez **Enregistrer**.)

   ![Enregistrer votre application logique](media/logic-apps-create-a-logic-app/save-logic-app.png)

   Lorsque vous l’enregistrez, votre application logique est mise en service. À l’heure actuelle, elle vérifie uniquement la présence de nouveaux éléments dans le flux RSS spécifié. 
   Pour rendre cet exemple plus utile, nous ajoutons une action que votre application logique exécute après l’activation de votre déclencheur.

## <a name="add-an-action-that-responds-to-your-trigger"></a>Ajouter une action de réaction à votre déclencheur

Une [*action*](./logic-apps-what-are-logic-apps.md#logic-app-concepts) est une tâche effectuée par le flux de travail de votre application logique. Après avoir ajouté un déclencheur à votre application logique, vous pouvez ajouter une action permettant d’effectuer des opérations avec les données générées par ce déclencheur. Dans notre exemple, nous ajoutons maintenant une action qui envoie un e-mail lorsque de nouveaux éléments apparaissent dans le flux RSS du site web.

1. Dans le concepteur, sous votre déclencheur, choisissez **Nouvelle étape** > **Ajouter une action**, comme indiqué ici :

   ![Ajouter une action](media/logic-apps-create-a-logic-app/add-new-action.png)

   Le concepteur affiche les [connecteurs disponibles](../connectors/apis-list.md), afin que vous puissiez sélectionner une action à exécuter lorsque votre déclencheur est activé.

2. En fonction de votre compte de messagerie, suivez les étapes relatives à Outlook ou Gmail.

   * Pour envoyer un e-mail à partir de votre compte Outlook, saisissez `outlook` dans la zone de recherche. Sous **Services**, choisissez **Outlook.com** pour les comptes Microsoft personnels, ou **Office 365 Outlook** pour les comptes Azure professionnels ou scolaires. 
   Sous **Actions**, sélectionnez **Envoi d’un courrier électronique**.

       ![Sélectionner l’action Envoi d’un courrier électronique d’Outlook](media/logic-apps-create-a-logic-app/actions.png)

   * Pour envoyer un e-mail à partir de votre compte Gmail, saisissez `gmail` dans la zone de recherche. 
   Sous **Actions**, sélectionnez **Envoyer un e-mail**.

       ![Choisissez Gmail - Envoyer un e-mail.](media/logic-apps-create-a-logic-app/actions-gmail.png)

3. Lorsque vous êtes invité à saisir des informations d’identification, connectez-vous avec le nom d’utilisateur et le mot de passe de votre compte de messagerie. 

4. Fournissez les détails de cette action, comme l’adresse de messagerie de destination, et choisissez les paramètres relatifs aux données à inclure dans l’e-mail, par exemple :

   ![Sélectionner les données à inclure dans l’e-mail](media/logic-apps-create-a-logic-app/rss-action-setup.png)

    Ainsi, si vous avez choisi Outlook, votre application logique peut ressembler à cet exemple :

    ![Application logique terminée](media/logic-apps-create-a-logic-app/save-run-complete-logic-app.png)

5.    Enregistrez vos modifications. (dans la barre de commandes du concepteur, choisissez **Enregistrer**.)

6. Vous pouvez maintenant exécuter votre application logique manuellement, à des fins de test. Dans la barre de commandes du concepteur, choisissez **Exécuter**. Sinon, vous pouvez laisser votre application logique vérifier le flux RSS spécifié selon la planification que vous avez configurée.

   Si votre application logique détecte de nouveaux éléments, l’application logique envoie un e-mail qui inclut vos données sélectionnées. 
   Si aucun nouvel élément n’est détecté, votre application logique ignore l’action qui envoie un e-mail.

7. Pour surveiller et vérifier l’historique de déclencheur et d’exécution de votre application logique, choisissez **Vue d’ensemble** dans le menu de votre application logique.

   ![Surveiller et afficher l’historique de déclencheur et d’exécution de votre application logique](media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > Si vous ne trouvez pas les données que vous attendez, choisissez **Actualiser** dans la barre de commandes.

   Pour en savoir plus sur l’état de votre application logique ou de l’historique de déclencheur et d’exécution correspondant, ou pour diagnostiquer les échecs de votre application logique, voir [Diagnostiquer des échecs d’applications logiques](logic-apps-diagnosing-failures.md).

      > [!NOTE]
      > Votre application logique poursuit son exécution jusqu’à ce que vous la désactiviez. Pour désactiver votre application de façon temporaire, choisissez **Vue d’ensemble** dans le menu de votre application logique. Choisissez **Désactiver** dans la barre de commandes.

Félicitations ! Vous venez de configurer et d’exécuter votre première application logique de base. Vous avez également appris comment créer des flux de travaux qui automatisent les processus et intégrer des services cloud et applications cloud, aisément et sans rédiger la moindre ligne de code.

## <a name="manage-your-logic-app"></a>Gérer votre application logique

Vous pouvez effectuer des tâches telles que la vérification de l’état, la modification, l’affichage de l’historique, la désactivation ou la suppression de votre application logique.

1. Connectez-vous au [portail Azure](https://portal.azure.com "portail Azure").

2. Cliquez sur l’option **Plus de services** dans le menu de gauche. Sous **Intégration d’entreprise**, choisissez **Applications logiques**. Sélectionnez votre application logique. 

   Dans le menu de l’application logique, vous pouvez accéder à ces tâches de gestion de l’application logique :

   |Task|Étapes| 
   |:---|:---| 
   | Afficher l’historique d’exécution et l’état de votre application, ainsi que des informations générales| Choisissez **Vue d’ensemble**.| 
   | Modifier votre application | Choisissez **Concepteur d’application logique**. | 
   | Afficher la définition JSON du flux de travail de votre application | Choisissez **Affichage du code de l’application logique**. | 
   | Afficher les opérations effectuées sur votre application logique | Choisissez **Journal d’activité**. | 
   | Afficher les anciennes versions de votre application logique | Choisissez **Versions**. | 
   | Désactiver votre application temporairement | Choisissez **Vue d’ensemble** et, dans la barre de commandes, sélectionnez **Désactiver**. | 
   | Supprimer votre application | Choisissez **Vue d’ensemble** et, dans la barre de commandes, sélectionnez **Supprimer**. Entrez le nom de votre application logique et choisissez **Supprimer**. | 

## <a name="get-help"></a>Obtenir de l’aide

Pour poser des questions ou y répondre et voir ce que font les autres utilisateurs d’Azure Logic Apps, visitez le [Forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Afin d’améliorer Azure Logic Apps ainsi que les connecteurs, votez pour des idées ou soumettez-en sur le [site de commentaires utilisateur Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

*  [Ajouter des conditions et exécuter des flux de travail](../logic-apps/logic-apps-use-logic-app-features.md)
*     [Configuration d’un flux de travail à l’aide d’un modèle prédéfini pour démarrer rapidement](../logic-apps/logic-apps-use-logic-app-templates.md)
*  [Créer une application logique à l’aide d’un modèle](../logic-apps/logic-apps-arm-provision.md)

