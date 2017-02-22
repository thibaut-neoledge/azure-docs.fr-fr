---
title: "Gérer Azure Logic Apps à partir de Visual Studio Cloud Explorer | Microsoft Docs"
description: "Gérer Azure Logic Apps à partir de Visual Studio Cloud Explorer."
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
ms.date: 12/19/2016
ms.author: klam
translationtype: Human Translation
ms.sourcegitcommit: a9f786259676a1bc9aa616f2db2935dff45475e2
ms.openlocfilehash: dcb0512b4f9e6c94262a3f39ef59f82fcfbf313e


---
# <a name="manage-logic-apps-with-the-visual-studio-cloud-explorer"></a>Gérer les applications logiques à l’aide de Visual Studio Cloud Explorer
Bien que le [Portail Azure](https://portal.azure.com) vous offre un excellent moyen de concevoir et gérer vos applications logiques, Visual Studio Cloud Explorer vous permet de gérer un grand nombre de vos ressources Azure dans Visual Studio, notamment les applications logiques.  Avec Cloud Explorer, vous pouvez parcourir les applications logiques publiées et effectuer différentes actions, notamment activer, désactiver, modifier et afficher les historiques des exécutions. 

## <a name="installation-steps"></a>Procédure d’installation :
Voici les étapes à suivre pour installer et configurer les outils Visual Studio pour Logic Apps.

### <a name="prerequisites"></a>Composants requis
* [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [Dernier kit de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads/) (2.9.1 ou supérieur)
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* Accès au web lors de l’utilisation du concepteur intégré

### <a name="install-visual-studio-tools-for-logic-apps"></a>Installer les outils Visual Studio pour Logic Apps
Une fois que les composants requis installés, 

1. Ouvrez le menu **Outils** de Visual Studio 2015 et cliquez sur **Extensions et mises à jour**
2. Sélectionnez la catégorie **En ligne** pour effectuer une recherche en ligne
3. Recherchez **Logic Apps** pour afficher les **outils Azure Logic Apps pour Visual Studio**
4. Cliquez sur le bouton **Télécharger** pour télécharger et installer l’extension
5. Redémarrez Visual Studio après l’installation

> [!NOTE]
> Vous pouvez également télécharger l’extension directement à partir de [ce lien](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)
> 
> 

## <a name="browsing-logic-apps"></a>Parcourir les applications logiques
Pour parcourir les applications logiques dans Cloud Explorer, ouvrez Cloud Explorer en sélectionnant Affichage > Cloud Explorer. Vous pouvez les parcourir par groupe de ressources ou par type de ressource.  Si vous les parcourez par type de ressource, sélectionnez un abonnement, développez la section Logic Apps, puis sélectionnez une application logique.  Vous pouvez cliquer avec le bouton droit sur l’une de vos applications logiques ou utiliser le menu Actions en bas de Cloud Explorer pour effectuer l’action souhaitée.

![Parcourir](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-logic-app-with-the-designer"></a>Modifier une application logique avec le concepteur
Pour ouvrir l’application logique actuellement déployée dans le même concepteur que celui proposé par le Portail Azure, cliquez avec le bouton droit sur l’application logique et sélectionnez « Ouvrir avec l’éditeur d’application logique ».  Avec le concepteur, vous pouvez modifier l’application logique, l’enregistrer à nouveau dans le cloud et démarrer une nouvelle exécution à l’aide du bouton « Déclencheur d’exécution ».

![Concepteur](./media/logic-apps-manage-from-vs/designer.png)

## <a name="browse-logic-app-run-history"></a>Parcourir l’historique des exécutions d’une application logique
Pour afficher l’historique des exécutions d’une application logique, cliquez avec le bouton droit sur l’application logique et sélectionnez « Ouvrir l’historique des exécutions ».  Dans cette vue, vous pouvez trier les informations selon l’une des propriétés affichées en sélectionnant l’en-tête de colonne correspondant.  

![Exécutions](media/logic-apps-manage-from-vs/runs.png)

Si vous double-cliquez sur l’une des instances d’exécution, l’historique des exécutions de cette instance s’affiche. Vous pouvez y voir les résultats de l’exécution, y compris les entrées et sorties de chaque étape.

![Historique](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>Étapes suivantes
* Pour vous familiariser avec les applications logiques, effectuez le didacticiel [Créer une application logique](logic-apps-create-a-logic-app.md) .  
* [Afficher des exemples et des scénarios courants](logic-apps-examples-and-scenarios.md)
* [Logic Apps vous permet d’automatiser vos processus métiers](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Apprenez à intégrer vos systèmes avec Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Jan17_HO3-->


