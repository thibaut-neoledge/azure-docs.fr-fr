---
title: "Créer votre premier flux de travail automatisé entre des systèmes et des services cloud : Azure Logic Apps| Microsoft Docs"
description: "Automatisez les processus d’entreprise et les flux de travail dans le cas de scénarios d’intégration des systèmes et des Applications d’Entreprise (IAE) en créant et en exécutant des applications logiques"
author: ecfan
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
ms.date: 09/18/2017
ms.author: LADocs; estfan
ms.openlocfilehash: d62255ba6e3d5bdfbd792a47f3a92d4c88876742
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-logic-app-for-automating-workflows-and-processes-through-the-azure-portal"></a>Créez votre première application logique pour automatiser les flux de travail et les processus via le portail Azure

Sans écrire de code, vous pouvez intégrer des systèmes et des services en générant et en exécutant des flux de travail automatisés avec [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md). Pour vous montrer à quel point il est facile d’automatiser des tâches avec un flux de travail, ce didacticiel crée une application logique de base qui cherche du nouveau contenu dans le flux RSS d’un site web et envoie un e-mail pour chaque nouvel élément trouvé. 

![Vue d’ensemble - Exemple d’une première application logique](./media/logic-apps-create-a-logic-app/logic-app-overview.png)

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créez une application logique vide.
> * Ajoutez un déclencheur pour démarrer l’application logique lorsqu’un élément du flux RSS est publié.
> * Ajoutez une action pour envoyer un e-mail avec les informations relatives à l’élément du flux RSS.
> * Exécutez et testez votre application logique.

## <a name="prerequisites"></a>Composants requis

* Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez [commencer par créer gratuitement un compte Azure](https://azure.microsoft.com/free/). Sinon, vous pouvez souscrire à un [abonnement de type paiement à l’utilisation](https://azure.microsoft.com/pricing/purchase-options/).

* Un compte de messagerie sur [n’importe quel fournisseur de messagerie pris en charge par Azure Logic Apps](../connectors/apis-list.md) pour envoyer les notifications. Vous pouvez par exemple utiliser Outlook 365 Office, Outlook.com, Gmail ou n’importe quel fournisseur pris en charge. Ce didacticiel utilise Outlook Office 365.

  > [!TIP]
  > Si vous disposez d’un [compte Microsoft](https://account.microsoft.com/account) personnel, vous possédez un compte Outlook.com. Sinon, si vous disposez d’un compte Azure professionnel ou scolaire, vous possédez un compte Outlook 365 Office.

* Un lien vers le flux RSS d’un site web. Cet exemple utilise le [flux RSS pour les témoignages du site web CNN.com](http://rss.cnn.com/rss/cnn_topstories.rss) : `http://rss.cnn.com/rss/cnn_topstories.rss`

## <a name="1-create-a-blank-logic-app"></a>1. Créer une application logique vide 

1. Connectez-vous au [portail Azure](https://portal.azure.com "portail Azure").

2. Dans le menu principal d’Azure, choisissez **Nouveau** > **Intégration d’entreprise** > **Application logique**.

   ![portail Azure, nouveau, intégration d’entreprise, application logique](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. Créez votre application logique avec les paramètres spécifiés dans le tableau.

   ![Spécifier les détails de l’application logique](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | Paramètre | Valeur suggérée | Description | 
   | ------- | --------------- | ----------- | 
   | **Name** | *nom-de-votre-application-logique* | Donnez un nom unique à l’application logique. | 
   | **Abonnement** | *votre-abonnement-Azure* | Sélectionnez l’abonnement Azure à utiliser. | 
   | **Groupe de ressources** | *votre-groupe-de-ressources-Azure* | Créez ou sélectionnez un groupe de ressources Azure, qui permet d’organiser et de gérer des ressources Azure liées. | 
   | **Emplacement** | *votre-région-Azure* | Sélectionnez la région du centre de données où déployer votre application logique. | 
   |||| 

4. Lorsque vous êtes prêt, sélectionnez **Épingler au tableau de bord**, puis **Créer**.

   Vous venez de créer une ressource Azure pour votre application logique. 
   Une fois qu’Azure a déployé votre application logique, le Concepteur d’applications logiques vous propose des modèles courants pour faciliter vos premiers pas.

   > [!NOTE] 
   > Lorsque vous sélectionnez l’option **Épingler au tableau de bord**, votre application logique s’affiche dans le tableau de bord Azure après le déploiement et s’ouvre automatiquement dans le Concepteur d’applications logiques. Sinon, vous pouvez manuellement rechercher et ouvrir votre application logique.

5. Sous **Modèles**, choisissez **Application logique vide**, afin de développer votre application logique à partir de zéro.

   ![Choisir le modèle d’application logique](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   Le Concepteur d’applications logiques affiche à présent les [*connecteurs*](../connectors/apis-list.md) disponibles et leurs [*déclencheurs*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts), que vous utilisez pour démarrer le flux de travail de votre application logique.

   ![Déclencheurs d’application logique](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="2-add-a-trigger-for-starting-the-workflow"></a>2. Ajouter un déclencheur pour démarrer le flux de travail

Chaque application logique doit démarrer avec un [*déclencheur*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts). Le déclencheur s’active lorsqu’un événement spécifique survient ou lorsque de nouvelles données remplissent les conditions que vous avez définies. Le moteur Logic Apps crée ensuite une instance d’application logique pour exécuter votre flux de travail. À chaque activation du déclencheur, le moteur crée une instance différente qui exécute le flux de travail de votre application logique.

1. Dans la zone de recherche, entrez « rss » comme filtre. Sélectionnez le déclencheur suivant : **RSS - Lors de la publication d’un élément de flux** 

   ![Sélectionnez le déclencheur : « RSS - Lors de la publication d’un élément de flux »](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. Indiquez le lien vers le flux RSS du site web dont vous souhaitez effectuer un suivi. Par exemple, `http://rss.cnn.com/rss/cnn_topstories.rss`. Définissez l’intervalle et la fréquence pour la récurrence. Dans cet exemple, définissez ces propriétés de sorte qu’elles vérifient le flux tous les jours. 

   ![Définir un déclencheur avec le flux RSS, la fréquence et l’intervalle](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

3. Enregistrez votre travail pour commencer Dans la barre d’outils du concepteur, choisissez **Enregistrer**.
Pour réduire et masquer les informations du déclencheur, cliquez sur la barre de titre.

   ![Enregistrer votre application logique](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   Votre application logique est désormais en ligne, mais elle ne fait rien de plus que vérifier les nouveaux éléments du flux RSS tant que vous n’avez pas ajouté d’actions au flux de travail. 

## <a name="3-add-an-action-that-responds-to-the-trigger"></a>3. Ajouter une action de réaction à votre déclencheur

Ajoutez à présent une [*action*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts), une tâche réalisée par le flux de travail de votre application logique. Dans cet exemple, ajoutez une action qui envoie un e-mail lorsqu’un nouvel élément apparaît dans le flux RSS.

1. Dans le Concepteur d’application logique, sous le déclencheur, sélectionnez **+ Nouvelle étape** > **Ajouter une action**.

   ![Ajouter une action](./media/logic-apps-create-a-logic-app/add-new-action.png)

   Le concepteur affiche les [connecteurs disponibles](../connectors/apis-list.md), afin que vous puissiez sélectionner une action à exécuter lorsque votre déclencheur est activé.

   ![Sélectionnez-en une depuis la liste des actions](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. Dans la zone de recherche, entrez «  envoyer e-mail » comme filtre. Selon votre fournisseur de messagerie, recherchez et sélectionnez le connecteur correspondant. Puis sélectionnez l’action « Envoyer un courrier électronique » pour votre connecteur. Par exemple : 

   * Pour un compte professionnel ou scolaire Azure, sélectionnez le connecteur Outlook Office 365. 
   * Pour des comptes personnels Microsoft, sélectionnez le connecteur Outlook.com. 
   * Pour des comptes Gmail, sélectionnez le connecteur Gmail. 

   Nous continuerons d’utiliser le connecteur Outlook Office 365. 
   Si vous utilisez un autre fournisseur, les étapes restent les mêmes, mais votre interface utilisateur peut s’afficher différemment. 

   ![Sélectionnez l’action : « Office 365 Outlook - Envoyer un e-mail »](./media/logic-apps-create-a-logic-app/actions.png)

3. Lorsque vous êtes invité à saisir des informations d’identification, connectez-vous avec le nom d’utilisateur et le mot de passe de votre compte de messagerie. 

4. Fournissez les informations spécifiées dans le tableau et sélectionnez les champs à inclure dans l’e-mail.

   | À | Étapes | 
   | -- | ----- | 
   | Sélectionnez les champs disponibles pour votre flux de travail. | Cliquez sur une zone d’édition afin d’ouvrir la liste **Contenu dynamique**, ou choisissez **Ajouter du contenu dynamique**. | 
   | Consultez les autres champs disponibles. | Dans la liste **Contenu dynamique**, sélectionnez **Voir plus** pour chaque section.  | 
   | Ajoutez des lignes vides dans une zone d’édition. | Appuyez sur Maj + Entrée. | 
   | Fermez la liste **Contenu dynamique**. | Sélectionnez de nouveau **Ajouter du contenu dynamique**. | 
   ||| 

   ![Sélectionner les données à inclure dans l’e-mail](./media/logic-apps-create-a-logic-app/rss-action-setup.png)

   | Paramètre | Valeur suggérée | Description | 
   | ------- | --------------- | ----------- | 
   | **To** | *adresse-e-mail-du-destinataire* | Entrez l’adresse e-mail du destinataire. À des fins de test, vous pouvez utiliser votre propre adresse e-mail. | 
   | **Objet** | Nouveau sujet CNN : **Titre du flux** | Entrez le contenu de l’objet de l’e-mail. <p>Dans le cadre de ce didacticiel, entrez le texte suggéré puis sélectionnez le champ **Titre du flux** du déclencheur, qui affiche le titre de l’élément du flux. | 
   | **Corps** | Titre : **Titre du flux** <p>Date de publication : **Lien principal du flux** <p>Lien : **Lien du flux principal** | Entrez le contenu du corps de l’e-mail. <p>Dans le cadre de ce didacticiel, entrez le texte suggéré et sélectionnez les champs du déclencheur suivants : <p>- **Titre du flux**, qui affiche à nouveau le titre de l’élément du flux </br>- **Flux publié sur**, qui affiche la date et l’heure de publication de l’élément </br>- **Lien du flux principal**, qui indique l’URL de l’élément du flux | 
   |||| 

   > [!NOTE] 
   > Si vous sélectionnez un champ qui contient un tableau, le concepteur ajoute automatiquement une boucle For each autour de l’action qui référence ce tableau. De cette façon, votre application logique effectue cette action sur chaque élément du tableau.

5. Lorsque vous avez terminé, enregistrez les modifications. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

   ![Application logique terminée](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

   Pour tester maintenant votre application logique, passez à la section suivante.

## <a name="4-run-and-test-your-workflow"></a>4. Exécuter et tester votre flux de travail

1. Pour exécuter manuellement votre application logique en vue d’un test, sélectionnez **Exécuter** dans la barre d’outils du concepteur. Sinon, vous pouvez laisser votre application logique vérifier le flux RSS spécifié selon la planification que vous avez configurée.

   ![Exécuter une application logique](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

   Si votre application logique détecte de nouveaux éléments, l’application logique envoie un e-mail qui inclut vos données sélectionnées. Par exemple :

   ![E-mail envoyé en raison d’un nouvel élément dans le flux RSS](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

   Si votre application logique ne détecte aucun nouvel élément, elle n’envoie pas d’e-mail et attend le prochain intervalle de vérification. 

2. Pour consulter l’historique des exécutions et des déclenchements de votre application logique, choisissez **Vue d’ensemble** dans le menu de votre application logique.
Pour afficher plus de détails sur une exécution, sélectionnez la ligne correspondante.

   ![Surveiller et afficher l’historique de déclencheur et d’exécution de votre application logique](./media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > Si vous ne trouvez pas les données que vous attendez, choisissez **Actualiser** dans la barre d’outils.

   Que l’exécution ait échoué ou non, les détails d’exécution indiqueront quelles étapes ont échoué et lesquelles ont réussi. 

   ![Affichage des détails relatifs à une exécution d’application logique](./media/logic-apps-create-a-logic-app/logic-app-run-details.png)

   Pour en savoir plus sur l’état de votre application logique ou de l’historique de déclencheur et d’exécution correspondant, ou pour diagnostiquer les échecs de votre application logique, voir [Diagnostiquer des échecs d’applications logiques](../logic-apps/logic-apps-diagnosing-failures.md).

3. Pour afficher les entrées et les sorties de chaque étape, développez celle que vous souhaitez vérifier. Ces informations peuvent vous aider à diagnostiquer et déboguer les problèmes de votre application logique. Par exemple :

   ![Affichage des détails de l’étape](./media/logic-apps-create-a-logic-app/logic-app-run-details-expanded.png)

   Pour plus d’informations, voir [Surveiller votre application logique](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Félicitations ! Vous venez de créer et d’exécuter votre première application logique de base. Cet exemple montre comment créer des flux de travaux qui automatisent les processus et intégrer des services cloud et applications cloud, aisément et sans rédiger la moindre ligne de code.

> [!NOTE]
> Votre application logique poursuit son exécution jusqu’à ce que vous la désactiviez. Pour désactiver temporairement votre application, passez à la section suivante.

## <a name="clean-up-resources"></a>Supprimer des ressources

Ce didacticiel utilise des ressources et effectue des actions qui peuvent entraîner des frais sur votre abonnement Azure. Lorsque vous aurez terminé de suivre ce didacticiel et d’effectuer les tests, veillez à désactiver ou à supprimer les ressources pour lesquelles vous ne souhaitez pas être facturé.

Vous pouvez empêcher l’exécution de votre application logique et envoyer un e-mail sans supprimer l’application. Dans le menu de votre application logique, **Vue d’ensemble**. Dans la barre d’outils, choisissez **Désactiver**.

![Désactiver votre application logique](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

## <a name="faq"></a>Forum Aux Questions

**Q :** Que puis-je faire d’autre avec mon application logique ? </br>
**R :** Il existe d’autres tâches que vous pouvez effectuer. Vous pouvez par exemple modifier, afficher la définition JSON, consulter le journal d’activité ou supprimer votre application logique.

Pour trouver d’autres tâches de gestion d’application logique, consultez ces commandes dans le menu de l’application logique :

| Task | Étapes | 
| ---- | ----- | 
| Afficher l’historique d’exécution et du déclencheur et les informations générales | Choisissez **Vue d’ensemble**. | 
| Modifier votre application | Choisissez **Concepteur d’application logique**. | 
| Afficher la définition JSON du flux de travail de votre application | Choisissez **Affichage du code de l’application logique**. | 
| Afficher les opérations effectuées sur votre application logique | Choisissez **Journal d’activité**. | 
| Afficher les anciennes versions de votre application logique | Choisissez **Versions**. | 
| Désactiver votre application temporairement | Choisissez **Vue d’ensemble** et, dans la barre d’outils, sélectionnez **Désactiver**. | 
| Supprimer votre application | Choisissez **Vue d’ensemble** et, dans la barre d’outils, sélectionnez **Supprimer**. Entrez le nom de votre application logique et choisissez **Supprimer**. | 
||| 

## <a name="get-support"></a>Obtenir de l'aide

* Pour toute question au sujet d’Azure Logic Apps, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Envie d’aider à améliorer Azure Logic Apps et ses connecteurs ? Votez ou faites-nous part de vos idées sur le [site Azure Logic Apps User Voice](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* [Créer votre application logique avec Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md)
* [Ajouter des conditions et exécuter des flux de travail](../logic-apps/logic-apps-use-logic-app-features.md)
*   [Configuration d’un flux de travail à l’aide d’un modèle prédéfini pour démarrer rapidement](../logic-apps/logic-apps-use-logic-app-templates.md)
* [Créer une application logique à l’aide d’un modèle](../logic-apps/logic-apps-arm-provision.md)
* [Contrôle d’utilisation de Logic Apps](../logic-apps/logic-apps-pricing.md) 
* [Tarification de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps)
