---
title: Solution Azure SQL Analytics dans Log Analytics | Microsoft Docs
description: "La solution Azure SQL Analytics vous permet de gérer vos instances Azure SQL Database."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: banders
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: f5f9aa186480926df1110928983566e05f79efb8
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017


---


# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>Surveiller Azure SQL Database à l’aide d’Azure SQL Analytics (version préliminaire) dans Log Analytics

![Symbole Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-symbol.png)

La solution Azure SQL Analytics d’Azure Log Analytics collecte et affiche d’importantes mesures de performances de SQL Azure. En vous appuyant sur les mesures collectées avec la solution, vous êtes en mesure de créer des règles et des alertes personnalisées de surveillance. Par ailleurs, vous pouvez surveiller les mesures associées à Azure SQL Database et aux pools élastiques au sein de plusieurs abonnements Azure et pools élastiques, et afficher ces données. La solution vous aide également à identifier les problèmes sur chacune des couches de votre pile applicative.  Elle utilise les [données de diagnostic Azure](log-analytics-azure-storage.md) avec les vues Log Analytics pour présenter les données relatives à l’ensemble de vos instances Azure SQL Database et de vos pools élastiques dans un espace de travail Log Analytics unique.

Actuellement, cette solution en version préliminaire prend en charge jusqu’à 150 000 instances Azure SQL Database et 5 000 pools élastiques SQL par espace de travail.

La solution Azure SQL Analytics, comme d’autres solutions disponibles pour Log Analytics, prend en charge la surveillance et la réception des notifications sur l’intégrité de vos ressources Azure, ici d’Azure SQL Database. Microsoft Azure SQL Database est un service évolutif de base de données relationnelle qui offre des fonctionnalités similaires à celles de SQL Server aux applications exécutées dans le cloud Azure. Log Analytics prend en charge la collecte, la mise en correspondance et l’affichage des données structurées et non structurées.

## <a name="connected-sources"></a>Sources connectées

La solution Azure SQL Analytics n’utilise pas d’agent pour établir la connexion avec le service Log Analytics.

Le tableau suivant décrit les sources connectées qui sont prises en charge par cette solution.

| Source connectée | Support | Description |
| --- | --- | --- |
| [Agents Windows](log-analytics-windows-agents.md) | Non | Les agents directs Windows ne sont pas utilisés par la solution. |
| [Agents Linux](log-analytics-linux-agents.md) | Non | Les agents directs Linux ne sont pas utilisés par la solution. |
| [Groupe d’administration SCOM](log-analytics-om-agents.md) | Non | La solution ne valorise aucune connexion directe entre l’agent SCOM et Log Analytics. |
| [Compte Stockage Azure](log-analytics-azure-storage.md) | Non | Log Analytics ne lit pas les données du compte de stockage. |
| [Diagnostics Azure](log-analytics-azure-storage.md) | Oui | Les données de mesure Azure sont transmises à Log Analytics directement par Azure. |

## <a name="prerequisites"></a>Composants requis

- Un abonnement Azure. Si vous n’en avez pas, créez-en un [gratuitement](https://azure.microsoft.com/free/).
- Un espace de travail Log Analytics. Vous pouvez utiliser un élément existant, ou en [créez un nouveau](log-analytics-get-started.md) avant de commencer à utiliser cette solution.
- Activez les diagnostics Azure pour vos instances Azure SQL Database et vos pools élastiques et [configurez-les pour l’envoi de leurs données vers Log Analytics](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/).

## <a name="configuration"></a>Configuration

Exécutez la procédure suivante afin d’ajouter la solution Azure SQL Analytics dans votre espace de travail.

1. Ajoutez la solution Azure SQL Analytics à votre espace de travail depuis la [Place de marché Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview) ou en procédant de la manière décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).
2. Dans le portail Azure, cliquez sur **Nouveau** (le symbole +), puis sur la liste de ressources, et sélectionnez **Surveillance et gestion**.  
    ![Surveillance et gestion](./media/log-analytics-azure-sql/monitoring-management.png)
3. Dans la liste **Surveillance et gestion**, cliquez sur **Afficher tout**.
4. Dans la liste **Recommandé**, cliquez sur **Plus**. Ensuite, dans la nouvelle liste, recherchez **Azure SQL Analytics (version préliminaire)** et sélectionnez cet élément.  
    ![Solution Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. Dans le panneau **Azure SQL Analytics (version préliminaire)**, cliquez sur **Créer**.  
    ![Créer](./media/log-analytics-azure-sql/portal-create.png)
6. Dans le panneau **Créer une nouvelle solution**, sélectionnez l’espace de travail que vous souhaitez ajouter à la solution, puis cliquez sur **Créer**.  
    ![ajouter à l’espace de travail](./media/log-analytics-azure-sql/add-to-workspace.png)


### <a name="to-configure-multiple-azure-subscriptions"></a>Pour configurer plusieurs abonnements Azure

Pour prendre en charge plusieurs abonnements, utilisez le script PowerShell de [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/) (Activer la journalisation des mesures de ressources Azure à l’aide de PowerShell). Fournissez l’ID de ressource d’espace de travail en tant que paramètre lors de l’exécution du script pour transmettre les données des ressources d’un abonnement Azure vers un espace de travail d’un autre abonnement Azure.

**Exemple**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>Utilisation de la solution

Lorsque vous ajoutez la solution à votre espace de travail, la vignette Azure SQL Analytics est également ajoutée ; elle apparaît dans la Vue d’ensemble. La vignette indique le nombre d’instances Azure SQL Database et de pools élastiques Azure SQL auxquels la solution est associée.

![Vignette Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Affichage des données Azure SQL Analytics

Cliquez sur la vignette **Azure SQL Analytics** afin d’ouvrir le tableau de bord Azure SQL Analytics. Le tableau de bord comprend les panneaux définis ci-dessous. Chaque panneau répertorie jusqu’à 15 ressources (abonnement, serveur, pool élastique et base de données). Cliquez sur une des ressources pour ouvrir le tableau de bord correspondant. Les pools élastiques et les bases de données contiennent des graphiques avec des mesures pour une ressource donnée. Cliquez sur un graphique pour ouvrir la boîte de dialogue Recherche dans les journaux.

| Panneau | Description |
|---|---|
| Abonnements | Liste des abonnements avec le nombre de bases de données, de pools et de serveurs connectés. |
| Serveurs | Liste des serveurs avec le nombre de bases de données et de pools connectés. |
| Pools élastiques | Liste des pools élastiques connectés avec les Go et eDTU maximum dans la période observée. |
|Bases de données | Liste des bases de données connectées avec les Go et DTU maximum dans la période observée.|


### <a name="analyze-data-and-create-alerts"></a>Analyser les données et créer des alertes

La solution prend en charge des requêtes pertinentes que vous mettez à profit pour analyser vos données. Si vous faites défiler l’écran vers la droite, le tableau de bord répertorie plusieurs requêtes courantes sur lesquelles vous pouvez cliquer pour effectuer une [recherche dans les journaux](log-analytics-log-searches.md) afin d’obtenir les données Azure SQL.

![requêtes](./media/log-analytics-azure-sql/azure-sql-queries.png)

La solution comprend quelques *requêtes basées sur une alerte*, tel qu’illustré ci-dessus. Vous pouvez utiliser ces dernières pour signaler l’atteinte de seuils spécifiques pour les instances Azure SQL Database et les pools élastiques.

#### <a name="to-configure-an-alert-for-your-workspace"></a>Pour configurer une alerte pour votre espace de travail

1. Accédez au [portail OMS](http://mms.microsoft.com/), puis connectez-vous.
2. Ouvrez l’espace de travail configuré pour la solution.
3. Sur la page Vue d’ensemble, cliquez sur la vignette **Azure SQL Analytics (version préliminaire)**.
4. Faites défiler vers la droite, puis cliquez sur une requête pour amorcer la création d’une alerte.  
![requête d’alerte](./media/log-analytics-azure-sql/alert-query.png)
5. Dans la recherche dans les journaux, cliquez sur **Alerte**.  
![création d’une alerte dans la recherche](./media/log-analytics-azure-sql/create-alert01.png)
6. Sur la page **Ajouter une règle d’alerte**, configurez les propriétés appropriées et les seuils spécifiques, puis cliquez sur **Enregistrer**.  
![ajout d’une règle d’alerte](./media/log-analytics-azure-sql/create-alert02.png)

### <a name="act-on-azure-sql-analytics-data"></a>Utilisation des données Azure SQL Analytics

Par exemple, l’une des requêtes les plus utiles que vous pouvez exécuter implique la comparaison de l’utilisation des unités de débit de base de données (DTU, Database Throughput Unit) sur l’ensemble des pools élastiques Azure SQL de tous vos abonnements Azure. L’utilisation de DTU constitue un moyen de décrire la capacité relative d’un niveau de performance des bases de données et des pools de base, Standard et Premium. Les DTU sont une mesure combinant la quantité d’UC, la mémoire, les lectures et les écritures. À mesure de l’augmentation de la valeur DTU, la puissance fournie par le niveau de performance augmente. Par exemple un niveau de performance avec 5 DTU présente une puissance 5 fois supérieure à un niveau de performance affichant 1 DTU. Un quota de DTU est associé à chaque serveur et pool élastique.

En exécutant la requête suivante dans la recherche des journaux, vous pouvez facilement identifier une sous-utilisation ou une surutilisation de vos pools élastiques SQL Azure.

```
Type=AzureMetrics ResourceId=*"/ELASTICPOOLS/"* MetricName=dtu_consumption_percent | measure avg(Average) by Resource | display LineChart
```

Dans l’exemple suivant, vous pouvez constater qu’un pool élastique présente un taux élevé d’utilisation avoisinant les 100 % de valeur DTU, tandis que les autres affichent un taux très faible. Vous pouvez effectuer d’autres recherches pour résoudre les problèmes associés aux potentielles modifications récentes apportées dans votre environnement, à l’aide des journaux d’activité Azure.

![Résultats de la recherche dans les journaux - Utilisation élevée](./media/log-analytics-azure-sql/log-search-high-util.png)

## <a name="see-also"></a>Voir aussi

- Utilisez les [Recherches de journal](log-analytics-log-searches.md) dans Log Analytics pour afficher les données détaillées sur Azure SQL.
- [Créer un tableau de bord personnalisé](log-analytics-dashboards.md) comportant les données Azure SQL.
- [Create and manage alert rules in Log Analytics with the OMS portal](log-analytics-alerts.md) (Créer et gérer des règles d’alerte dans Log Analytics dans Log Analytics à l’aide du portail OMS).

