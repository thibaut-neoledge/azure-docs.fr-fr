---
title: "Exportation de données Log Analytics vers Power BI | Microsoft Docs"
description: "Power BI est un service Microsoft d’analyse commerciale basé sur le cloud qui fournit de riches fonctions de visualisation et de rapport afin de faciliter l’analyse de différents jeux de données.  Log Analytics peut exporter des données en continu entre le référentiel OMS et Power BI afin de vous permettre de tirer parti de ses visualisations et de ses outils d’analyse.  Cet article décrit comment configurer des requêtes dans Log Analytics pour exécuter automatiquement des exportations vers Power BI à intervalles réguliers."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 98befb16d27387e8f65a27771a2a32c264119d74
ms.contentlocale: fr-fr
ms.lasthandoff: 07/28/2017

---
# <a name="export-log-analytics-data-to-power-bi"></a>Exportation de données Log Analytics vers Power BI

>[!NOTE]
> Une fois votre espace de travail mis à niveau vers le [nouveau langage de requête de Log Analytics](log-analytics-log-search-upgrade.md), le processus d’exportation des données de Log Analytics à Power BI ne fonctionne plus.  Toutes les planifications existantes que vous avez créées avant la mise à niveau sont alors désactivées. 
>
> Après la mise à niveau, Azure Log Analytics utilise la même plateforme qu’Application Insights et vous devez utiliser le même processus vous permettant d’exporter des requêtes de Log Analytics vers Power BI comme [processus d’exportation des requêtes d’Application Insights vers Power BI](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).  Vous pouvez soit exporter la requête à l’aide de la console Analytics comme décrit dans cet article, soit sélectionner le bouton **Power BI** situé dans le coin supérieur de l’écran du portail de recherche dans les journaux.



[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) est un service Microsoft d’analyse commerciale basé sur le cloud qui fournit de riches fonctions de visualisation et de rapport afin de faciliter l’analyse de différents jeux de données.  Log Analytics peut exporter automatiquement les données entre le référentiel OMS et Power BI afin de vous permettre de tirer parti de ses visualisations et de ses outils d’analyse.

Lorsque vous configurez Power BI avec Log Analytics, vous créez des requêtes de journal qui exportent les résultats vers les jeux de données correspondants dans Power BI.  La requête et l’exportation poursuivent automatiquement leur exécution selon une planification que vous avez définie afin de maintenir à jour le jeu de données avec les dernières données collectées par Log Analytics.

![Log Analytics vers Power BI](media/log-analytics-powerbi/overview.png)

## <a name="power-bi-schedules"></a>Planifications Power BI
Une *planification Power BI* inclut une recherche de journal qui exporte un jeu de données du référentiel OMS vers un jeu de données correspondant dans Power BI, ainsi qu’une planification qui définit la fréquence d’exécution de cette recherche afin de maintenir à jour le jeu de données.

Les champs du jeu de données correspondent aux propriétés des enregistrements renvoyés par la recherche de journal.  Si la recherche renvoie des enregistrements de différents types, le jeu de données inclura toutes les propriétés de chacun des types d’enregistrements inclus.  

> [!NOTE]
> Il est recommandé d’utiliser une requête de recherche de journal qui retourne des données brutes, plutôt que d’effectuer une consolidation à l’aide de commandes de type [Mesure](log-analytics-search-reference.md#measure).  Vous pouvez effectuer des agrégations et des calculs dans Power BI à partir des données brutes.
>
>

## <a name="connecting-oms-workspace-to-power-bi"></a>Connexion de l’espace de travail OMS à Power BI
Avant de pouvoir exporter des données de Log Analytics vers Power BI, vous devez connecter à votre espace de travail OMS à votre compte Power BI. Pour cela, procédez comme suit :  

1. Dans la console OMS, cliquez sur la vignette **Paramètres** .
2. Sélectionnez **Comptes**.
3. Dans la section **Informations sur l’espace de travail**, cliquez sur **Se connecter à un compte Power BI**.
4. Entrez les informations d’identification de votre compte Power BI.

## <a name="create-a-power-bi-schedule"></a>Création d’une planification Power BI
Créez une planification Power BI pour chaque jeu de données à l’aide de la procédure suivante.

1. Dans la console OMS, cliquez sur la vignette **Recherche de journal** .
2. Entrez une nouvelle requête ou sélectionnez une recherche enregistrée qui retourne les données que vous souhaitez exporter vers **Power BI**.  
3. Cliquez sur le bouton **Power BI** en haut de la page pour ouvrir la boîte de dialogue **Power BI**.
4. Renseignez les informations du tableau suivant et cliquez sur **Enregistrer**.

| Propriété | Description |
|:--- |:--- |
| Nom |Nom permettant d’identifier la planification dans la liste des planifications Power BI. |
| Recherche enregistrée |Recherche de journal à exécuter.  Vous pouvez sélectionner la requête en cours ou sélectionner une recherche enregistrée dans la zone de liste déroulante. |
| Planification |Fréquence d’exécution de la recherche enregistrée et d’exportation des résultats vers le jeu de données Power BI.  La valeur doit être comprise entre 15 minutes et 24 heures. |
| Nom du jeu de données |Nom du jeu de données dans Power BI.  Ce nom sera créé s’il n’existe pas ; dans le cas contraire, il sera mis à jour. |

## <a name="viewing-and-removing-power-bi-schedules"></a>Affichage et suppression de planifications Power BI
Utilisez la procédure suivante pour afficher la liste des planifications Power BI.

1. Dans la console OMS, cliquez sur la vignette **Paramètres** .
2. Sélectionnez **Power BI**.

Vous obtenez, outre les détails de la planification, le nombre d’exécutions de la planification au cours de la semaine précédente ainsi que l’état de la dernière synchronisation.  Si la synchronisation a rencontré des erreurs, vous pouvez cliquer sur le lien pour exécuter une recherche de journal afin d’obtenir les enregistrements et les détails de l’erreur.

Vous pouvez supprimer une planification en cliquant sur le **X** dans **Supprimer la colonne**.  Pour désactiver une planification, sélectionnez **Désactivé**.  Pour modifier une planification, vous devez la supprimer et la recréer avec les nouveaux paramètres.

![Planifications Power BI](media/log-analytics-powerbi/schedules.png)

## <a name="sample-walkthrough"></a>Exemple de procédure pas à pas
La section suivante explique, au travers d’un exemple, comment création une planification Power BI et utiliser son jeu de données pour créer un rapport simple.  Dans cet exemple, toutes les données de performances d’un ensemble d’ordinateurs sont exportées vers Power BI. Un graphique linéaire est ensuite créé pour afficher l’utilisation du processeur.

### <a name="create-log-search"></a>Création de la recherche de journal
Nous allons commencer par créer une recherche de journal sur les données que vous souhaitez envoyer au jeu de données.  Dans cet exemple, nous allons utiliser une requête qui retourne toutes les données de performances des ordinateurs dont le nom commence par *srv*.  

![Planifications Power BI](media/log-analytics-powerbi/walkthrough-query.png)

### <a name="create-power-bi-search"></a>Création d’une recherche Power BI
Cliquez sur le bouton **Power BI** pour ouvrir la boîte de dialogue Power BI et renseignez les informations requises.  Vous souhaitez exécuter cette recherche une fois par heure et créer un jeu de données nommé *Contoso Perf*.  Puisque vous avez déjà ouvert la recherche qui permet de créer les données souhaitées, conservez la valeur par défaut de l’option *Utiliser la requête de recherche active* pour **Recherche enregistrée**.

![Recherche Power BI](media/log-analytics-powerbi/walkthrough-schedule.png)

### <a name="verify-power-bi-search"></a>Vérification de la recherche Power BI
Pour vérifier que la planification a été correctement créée, affichez la liste des recherches Power BI sous la vignette **Paramètres** dans le tableau de bord OMS.  Attendez quelques minutes et actualisez cette vue jusqu’à ce que vous obteniez confirmation que la synchronisation a été exécutée.

![Recherche Power BI](media/log-analytics-powerbi/walkthrough-schedules.png)

### <a name="verify-the-dataset-in-power-bi"></a>Vérification du jeu de données dans Power BI
Connectez-vous à votre compte à l’adresse [powerbi.microsoft.com](http://powerbi.microsoft.com/) et faites défiler le volet gauche vers le bas jusque **Jeux de données** .  Comme vous pouvez le voir, le jeu de données *Contoso Perf* apparaît, ce qui signifie que l’exportation a réussi.

![Jeu de données Power BI](media/log-analytics-powerbi/walkthrough-datasets.png)

### <a name="create-report-based-on-dataset"></a>Création d’un rapport dérivé du jeu de données
Sélectionnez le jeu de données **Contoso Perf**, puis cliquez sur **Résultats** dans le volet **Champs** pour afficher les champs qui font partie de ce jeu de données.  Pour créer un graphique linéaire illustrant l’utilisation des ressources processeur de chaque ordinateur, effectuez les actions suivantes.

1. Sélectionnez la visualisation Graphique en courbes.
2. Faites glisser **ObjectName** sur **Filtre du niveau de rapport** et cochez la case **Processeur**.
3. Faites glisser **CounterName** sur **Filtre du niveau de rapport** et cochez **% temps processeur**.
4. Faites glisser **CounterValue** sur **Valeurs**.
5. Faites glisser **Ordinateur** sur **Légende**.
6. Faites glisser **TimeGenerated** sur **Axe**.

Comme vous pouvez le voir, le graphique en courbes obtenu s’affiche avec les données de notre jeu de données.

![Graphique en courbes Power BI](media/log-analytics-powerbi/walkthrough-linegraph.png)

### <a name="save-the-report"></a>Enregistrement du rapport
Pour enregistrer le rapport, cliquez sur le bouton Enregistrer en haut de l’écran, puis vérifiez qu’il apparaît désormais dans la section Rapports dans le volet gauche.

![Rapports Power BI](media/log-analytics-powerbi/walkthrough-report.png)

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment les [recherches de journaux](log-analytics-log-searches.md) peuvent vous aider à générer des requêtes pouvant être exportées vers Power BI.
* Découvrez comment utiliser [Power BI](http://powerbi.microsoft.com) pour créer des visualisations basées sur des exportations Log Analytics.

