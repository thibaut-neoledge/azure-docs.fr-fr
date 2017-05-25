---
title: "Gestion d’applications logiques dans Visual Studio - Azure Logic Apps | Microsoft Docs"
description: "Gestion d’applications logiques et d’autres ressources à l’aide de Visual Studio Cloud Explorer"
author: klam
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 12/19/2016
ms.author: LADocs; klam
ms.translationtype: Human Translation
ms.sourcegitcommit: 2fafb3c127183c4903ed7ee0da5811988db9116c
ms.openlocfilehash: 9a542f83eb35b159de04f0395cd850376da498de
ms.contentlocale: fr-fr
ms.lasthandoff: 03/02/2017

---

# <a name="manage-your-logic-apps-with-visual-studio-cloud-explorer"></a>Gestion de vos applications logiques à l’aide de Visual Studio Cloud Explorer

Bien que le [Portail Azure](https://portal.azure.com/) vous offre un excellent moyen de concevoir et gérer Azure Logic Apps, vous pouvez gérer un grand nombre de vos ressources Azure, notamment des applications logiques, dans Visual Studio lorsque vous utilisez Visual Studio Cloud Explorer. Vous pouvez parcourir les applications logiques publiées et effectuer différentes tâches, notamment activer et désactive vos applications logiques ou modifier et afficher les historiques des exécutions. 

## <a name="installation-steps"></a>Procédure d’installation :

Pour installer et configurer les outils Visual Studio pour Azure Logic Apps, suivez ces étapes.

### <a name="prerequisites"></a>Composants requis

* [Visual Studio 2015 ou Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [Dernier kit de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads/) (2.9.1 ou supérieur)
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* Accès au web lors de l’utilisation du concepteur intégré

### <a name="install-visual-studio-tools-for-logic-apps"></a>Installer les outils Visual Studio pour Logic Apps

Une fois les composants requis installés :

1. Ouvrez Visual Studio. Dans le menu **Outils**, sélectionnez **Extensions et mises à jour**.
2. Développez la catégorie **En ligne** pour effectuer une recherche en ligne.
3. Parcourez les résultats ou recherchez **Logic Apps** jusqu’à ce que vous trouviez **Outils Azure Logic Apps pour Visual Studio**.
4. Pour télécharger et installer l’extension, cliquez sur **Télécharger**.
5. Redémarrez Visual Studio après l’installation.

> [!NOTE]
> Vous pouvez également télécharger des outils Azure Logic Apps pour Visual Studio directement à partir de la [Place de marché Visual Studio](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9).

## <a name="browse-for-logic-apps-in-cloud-explorer"></a>Recherche d’applications logiques dans Cloud Explorer

1.    Pour ouvrir Cloud Explorer, sur le menu **Affichage**, choisissez **Cloud Explorer**.
2.    Recherchez votre application logique, par groupe de ressources ou par type de ressource. 

    Si vous les parcourez par type de ressource, sélectionnez un abonnement Azure, développez la section Logic Apps, puis sélectionnez une application logique. 
    Vous pouvez cliquer avec le bouton droit sur une application logique, choisir le menu **Actions** en bas du Cloud Explorer.

    ![Accès à votre application logique](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-your-logic-app-with-logic-app-designer"></a>Modifier votre application logique avec le Concepteur d’application logique

Pour ouvrir une application logique actuellement déployée dans le même concepteur que celui utilisé dans le Portail Azure, cliquez avec le bouton droit sur l’application logique et sélectionnez **Ouvrir avec l’éditeur d’application logique**. 

Dans le concepteur, vous pouvez modifier votre application logique, enregistrer vos mises à jour dans le cloud et démarrer une nouvelle exécution en choisissant **Déclencheur d'exécution**.

![Concepteur d’application logique](./media/logic-apps-manage-from-vs/designer.png)

## <a name="browse-your-logic-app-run-history"></a>Parcourir votre historique des exécutions d’une application logique

Pour afficher l’historique des exécutions de votre application logique, cliquez avec le bouton droit sur votre application logique et sélectionnez **Ouvrir l’historique des exécutions**. Pour réorganiser votre historique des exécutions en fonction de l’une des propriétés affichées, sélectionnez l’en-tête de colonne.

![Historique d’exécution](media/logic-apps-manage-from-vs/runs.png)

Pour afficher l’historique des exécutions pour une instance et voir les résultats de l’exécution, notamment les entrées et sorties de chaque étape, double-cliquez sur l’une des instances d’exécution.

![Exécuter les résultats de l’historique, les entrées et les sorties d’étapes](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>Étapes suivantes

*    Pour vous familiariser avec Azure Logic Apps, consultez la rubrique [Créez votre première application logique](logic-apps-create-a-logic-app.md)
* [Concevoir, créer et déployer des applications logiques dans Visual Studio](logic-apps-deploy-from-vs.md)
* [Afficher des exemples et des scénarios courants](logic-apps-examples-and-scenarios.md)
* [Découvrez comment automatiser vos processus métiers avec Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694)
* [Apprenez à intégrer vos systèmes avec Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)

