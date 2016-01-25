<properties
   pageTitle="Performances et mise à l’échelle élastiques avec SQL Data Warehouse | Microsoft Azure"
   description="Description de la flexibilité de SQL Data Warehouse à l’aide des unités DWU afin d’ajouter ou de supprimer des ressources de calcul. Des exemples de code sont fournis."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="nicw;jrj;mausher;barbkess;sonyama"/>

# Performances et mise à l’échelle élastiques avec SQL Data Warehouse
Pour augmenter et faire descendre de manière flexible votre capacité de calcul, il vous suffit de modifier le nombre d’unités DWU (Data Warehouse Units) allouées à votre instance SQL Data Warehouse. Les unités DWU sont un nouveau concept proposé par SQL Data Warehouse pour simplifier et optimiser l’efficacité de votre gestion. Cette rubrique vous présente les unités DWU et vous explique comment les valoriser pour mettre à l’échelle de manière flexible votre capacité de calcul. L’article fournit également quelques recommandations initiales relatives à la définition d’une valeur raisonnable de DWU pour votre environnement.

## Qu’est-ce qu’une unité DWU ?
Dans les coulisses, Microsoft exécute un certain nombre de tests d’évaluation des performances afin de déterminer le volume de matériel et la configuration nécessaires au développement d’une offre compétitive pour nos clients. La montée et la descente en puissance de la capacité de calcul s’effectuent par blocs de 100 unités DWU, mais nos offres ne comprennent pas l’ensemble des multiples de 100 DWU.

## Combien d’unités DWU dois-je utiliser ?
Plutôt que de communiquer des configurations de DWU idéales pour les catégories respectives de clients, penchons-nous sur l’aspect pratique de la question. Les performances de SQL Data Warehouse sont mises à l’échelle de manière linéaire et le passage entre deux capacités données (par exemple de 100 DWU à 2 000 DWU) s’effectue en quelques secondes. Vous pouvez ainsi, en toute souplesse, effectuer des essais et déterminer la meilleure configuration de votre scénario.

1. Si vous disposez d’un entrepôt de données en développement, commencez par sélectionner un nombre réduit d’unités DWU.
2. Surveillez les performances de votre application, en observant notamment le nombre d’unités DWU sélectionné.
3. Déterminez le niveau de performances le mieux adapté aux exigences en modulant la capacité de votre système à l’aide d’une mise à l’échelle linéaire. 
4. Augmentez ou baissez le nombre d’unités DWU sélectionné. Le service répond rapidement et ajuste le volume de ressources de calcul en fonction de la configuration des unités DWU.
5. Continuez à effectuer des ajustements jusqu’à ce que vous atteigniez le niveau de performances requis par vos activités.

Si votre application présente une charge de travail fluctuante, augmentez et réduisez les niveaux de performances suivant les pics et les creux. Par exemple, si une charge de travail atteint habituellement un niveau optimal à la fin du mois, envisagez d’ajouter davantage d’unités DWU durant ces quelques jours, avant de réduire l’échelle une fois la période d’activité intense passée.
 
## Montée et descente en puissance des ressources de calcul
Indépendante du stockage de type cloud, la flexibilité de SQL Data Warehouse vous permet d’augmenter, de réduire ou d’interrompre la capacité de calcul à l’aide d’une mise à l’échelle par glissement des unités d’entrepôt de données (DWU). Ainsi, vous disposez de la latitude nécessaire pour configurer une capacité de calcul optimale pour votre activité.

Pour augmenter votre puissance de calcul, vous pouvez ajouter d’autres DWU au service à l’aide du curseur de montée en puissance parallèle dans le portail Azure Classic. Vous pouvez également ajouter des DWU via les applets de commande T-SQL, API REST ou Powershell. La montée et la descente en puissance annulent l’ensemble des activités en cours et en attente, mais s’exécutent en quelques secondes. Ainsi, vous pouvez reprendre en quelques secondes, avec une capacité de calcul approximativement égale.

Dans le [Portail Azure Classic][], vous pouvez cliquer sur l’icône de « Montée en puissance parallèle » en haut de votre page SQL Data Warehouse, puis utiliser le curseur pour augmenter ou baisser la quantité d’unités DWU appliquée à votre entrepôt de données avant de cliquer sur « Enregistrer ». Si vous souhaitez chanter l’échelle par programmation, le code T-SQL ci-dessous indique comment ajuster l’allocation de DWU pour votre instance SQL Data Warehouse :

```
ALTER DATABASE MySQLDW 
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```
Veuillez noter que cette T-SQL doit être exécuté sur votre serveur logique et non sur l’instance de SQL Data Warehouse elle-même.

Vous pouvez obtenir le même résultat à l’aide de PowerShell, en exécutant le code ci-dessous :

```
Set-AzureSQLDatabase -DatabaseName "MySQLDW" -ServerName "MyServer.database.windows.net" -ServiceObjective "DW1000"
```

## Interruption des ressources de calcul
SQL Data Warehouse est la seule solution qui prend en charge l’interruption et la reprise à la demande des ressources de calcul. Si l’équipe envisage de ne pas utiliser l’instance Data Warehouse pendant une période considérée, comme la nuit, le week-end, durant les jours fériés ou pour toute autre raison, vous pouvez interrompre l’instance pendant ladite période et la configurer la reprise au niveau de la phase précise où vous vous étiez arrêté.

L’action d’interruption renvoie vos ressources de calcul vers le pool de ressources disponible du centre de données et l’action de reprise rassemble les ressources de calcul nécessaires pour le volume défini de DWU et les alloue à votre instance Data Warehouse.

L’interruption et la reprise de votre capacité de calcul s’effectuent via le [portail Azure Classic][], via des API REST ou par le biais de PowerShell. L’interruption annule l’ensemble des activités en cours ou en attente. Le moment venu, vous pouvez relancer vos ressources de calcul en quelques secondes.

Le code ci-dessous indique comment effectuer une pause à l’aide de PowerShell :

```
Suspend-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName
"Server01" –DatabaseName "Database02"
```

Il est également très simple de reprendre le service à l’aide de PowerShell :

```
Resume-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
```

Pour en savoir plus sur l’utilisation de PowerShell, consultez l’article [Introduction aux applets de commande PowerShell][].

> [Azure.Note]Le stockage étant séparé des ressources de calcul, il n’est aucunement affecté par l’interruption.

## Étapes suivantes
Pour consulter une présentation sur les performances, accédez à la [vue d’ensemble sur les performances][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble sur les performances]: sql-data-warehouse-overview-performance.md
[Introduction aux applets de commande PowerShell]: sql-data-warehouse-get-started-powershell-cmdlets.md

<!--MSDN references-->


<!--Other Web references-->

[Portail Azure Classic]: http://portal.azure.com/
[portail Azure Classic]: http://portal.azure.com/

<!---HONumber=AcomDC_0114_2016-->