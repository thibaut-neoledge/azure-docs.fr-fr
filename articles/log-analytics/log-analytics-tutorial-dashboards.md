---
title: "Créer et partager des tableaux de bord de données Log Analytics | Microsoft Docs"
description: "Ce didacticiel vous aide à comprendre comment les tableaux de bord Log Analytics permettent de visualiser l’ensemble de vos recherches dans les journaux enregistrées en proposant une vue unique de votre environnement."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: magoedte
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: b065269a27ad3764399802b4a2bc3076b7349623
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Créer et partager des tableaux de bord de données Log Analytics

Les tableaux de bord Log Analytics vous permettent de visualiser l’ensemble de vos recherches dans les journaux enregistrées. Vous pouvez y trouver les données opérationnelles du service informatique, mettre ces données en corrélation et les partager dans l’organisation.  Ce didacticiel porte sur la création d’une recherche dans les journaux de journal pour un tableau de bord partagé qui sera utilisé par votre équipe de support des opérations informatiques.  Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un tableau de bord partagé dans le portail Azure
> * Visualiser une recherche dans le journal des performances 
> * Ajouter une recherche dans les journaux à un tableau de bord partagé 
> * Personnaliser une vignette dans un tableau de bord partagé

Pour effectuer l’exemple de ce didacticiel, vous devez disposer d’une machine virtuelle [connectée à l’espace de travail Log Analytics](log-analytics-quick-collect-azurevm.md).  
 
## <a name="log-in-to-azure-portal"></a>Se connecter au portail Azure
Connectez-vous au portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Créer un tableau de bord partagé

La première chose que vous voyez après vous être connecté au portail Microsoft Azure, c’est un [tableau de bord](../azure-portal/azure-portal-dashboards.md).<br> ![Tableau de bord du portail Azure](media/log-analytics-tutorial-dashboards/log-analytics-portal-dashboard.png)

Celui-ci vous permet de rassembler les données opérationnelles les plus importantes pour le service informatique dans toutes vos ressources Azure, notamment les données de télémétrie d’Azure Log Analytics.  Avant d’aborder la visualisation d’une recherche dans les journaux, commençons par créer et partager un tableau de bord.  Ces étapes sont nécessaires avant d’effectuer l’exemple de recherche dans les journaux de performances, qui sera restituée sous forme de graphique en courbes. Nous l’ajouterons ensuite au tableau de bord.  

Pour créer un tableau de bord, sélectionnez le bouton **Nouveau tableau de bord** en regard du nom du tableau de bord actuel.<br> ![Créer un tableau de bord dans le portail Azure](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-01.png)

Cette action crée un nouveau tableau de bord privé vide, et vous fait passer en mode de personnalisation. Vous pouvez alors renommer votre tableau de bord et ajouter ou réorganiser les mosaïques. Modifiez le nom du tableau de bord, spécifiez *Sample Dashboard* pour ce didacticiel, puis sélectionnez **Personnalisation terminée**.<br><br> ![Enregistrer le tableau de bord Azure personnalisé](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-02.png)

Par défaut, lorsque vous créez un tableau de bord, celui-ci est privé, ce qui signifie que vous êtes la seule personne à pouvoir le consulter. Pour que les autres utilisateurs puissent le voir, utilisez le bouton **Partager** , disponible avec les autres commandes de tableau de bord.<br> ![Partager un nouveau tableau de bord dans le portail Azure](media/log-analytics-tutorial-dashboards/log-analytics-share-dashboard.png) 

Vous êtes invité à choisir un abonnement et un groupe de ressources dans lesquels votre tableau de bord doit être publié. Pour plus de commodité, l’expérience de publication du portail vous guide vers un modèle dans lequel vous placez les tableaux de bord dans un groupe de ressources appelé **tableaux de bord**.  Vérifiez l’abonnement sélectionné, puis cliquez sur **Publier**.  L’accès aux informations figurant dans le tableau de bord est contrôlé par le [Contrôle d’accès en fonction des ressources Azure](../active-directory/role-based-access-control-configure.md).   

## <a name="visualize-a-log-search"></a>Visualiser une recherche dans les journaux

Vous pouvez créer des requêtes de base sur une seule ligne à partir du portail de recherche dans les journaux, dans le portail Azure. Il est possible d’utiliser le portail de recherche dans les journaux sans lancer un portail externe. Vous pouvez en outre l’utiliser pour exécuter diverses fonctions avec des recherches dans les journaux, y compris la création de règles d’alerte, la création de groupes d’ordinateurs et l’exportation de résultats de la requête. 

Le [portail d’analytique avancée](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal) est un portail dédié qui propose des fonctionnalités avancées non disponibles dans le portail de recherche dans les journaux. Ces fonctionnalités incluent la possibilité de modifier une requête sur plusieurs lignes et d’exécuter de façon sélective du code, des données Intellisense contextuelles et Smart Analytics. Dans le portail d’analytique avancée, vous allez créer une vue des performances sous forme graphique, l’enregistrer pour une recherche ultérieure et l’épingler au tableau de bord partagé créé précédemment.   

Lancez le portail d’analytique avancée à partir d’un lien du portail de recherche dans les journaux.<br> ![Lancer le portail d’analytique avancée](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-01.png)

Dans le portail d’analytique, entrez la requête suivante pour obtenir uniquement les enregistrements d’utilisation des processeurs pour les ordinateurs Windows et Linux, regroupés selon les critères Computer et TimeGenerated et affichés dans un graphique visuel :

```
Perf | where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1m), Computer | render timechart
```

Enregistrez la requête en cliquant sur le bouton **Enregistrer la requête** en haut à droite.<br> ![Enregistrer la requête à partir du portail d’analytique avancée](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-02.png)<br><br> Dans le panneau de configuration **Enregistrer la requête**, indiquez un nom, par exemple *Machines virtuelles Azure - Utilisation des processeurs*, puis cliquez sur **Enregistrer**.  De cette manière, vous pouvez créer une bibliothèque de requêtes courantes dédiées aux recherches ou la modifier sans avoir à la réécrire entièrement.  Enfin, épinglez-la au tableau de bord partagé créé précédemment en cliquant sur le bouton **Épingler le graphique à votre tableau de bord Azure** dans l’angle droit central de la page.  

Maintenant qu’une requête est épinglée au tableau de bord, vous remarquerez qu’elle a un titre générique et qu’elle est suivie d’un commentaire.<br> ![Exemple de tableau de bord Azure](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-01.png)<br><br>  Nous avons tout intérêt à la renommer en lui attribuant un nom explicite et compréhensible des utilisateurs.  Cliquez avec le bouton droit sur la vignette, puis sélectionnez **Modifier la vignette**.  Après avoir personnalisé le titre et le sous-titre de la vignette, cliquez sur **Mettre à jour**.  Une bannière s’affiche pour vous inviter à publier les modifications ou à les ignorer.  Cliquez sur **Publier les modifications**, puis fermez le volet **Modifier la vignette**.  

![Configuration terminée de l’exemple de tableau de bord](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à créer un tableau de bord dans le portail Azure et à lui ajouter une recherche dans les journaux.  Passez au didacticiel suivant pour découvrir les différentes réponses que vous pouvez implémenter en fonction des résultats de la recherche dans les journaux.  

> [!div class="nextstepaction"]
> [Répondre aux événements avec des alertes Log Analytics](log-analytics-tutorial-response.md)
