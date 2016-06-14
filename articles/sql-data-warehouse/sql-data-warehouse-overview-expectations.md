<properties
   pageTitle="Attentes de SQL Data Warehouse preview | Microsoft Azure"
   description="Résumé des fonctionnalités de la version préliminaire publique et de nos objectifs pour la disponibilité générale de SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/05/2016"
   ms.author="nicw;barbkess;sonyama"/>


# Attentes de SQL Data Warehouse preview

Cet article décrit les fonctionnalités de SQL Data Warehouse preview et nos objectifs pour le service de disponibilité générale. Nous mettrons régulièrement à jour ces informations au fur et à mesure que nous améliorerons les fonctionnalités de la version préliminaire publique.

Nos objectifs pour SQL Data Warehouse :

- Performances prévisibles et évolutivité linéaire jusqu’à plusieurs pétaoctets de données
- Fiabilité élevée pour toutes les opérations effectuées dans l'entrepôt de données
- Intervalle raccourci entre le chargement des données et les analyses des données sur des données relationnelles et non relationnelles

Nous tendrons en permanence vers ces objectifs dans la version préliminaire de SQL Data Warehouse.

## Performances prévisibles et évolutives

Azure SQL Data Warehouse présente les unités DWU (Data Warehouse Units) comme un moyen de mesurer les ressources informatiques (processeurs, mémoire, stockage E/S) disponibles pour l’entrepôt de données. L’augmentation du nombre de DWU augmente les ressources. À mesure que le nombre de DWU augmente, SQL Data Warehouse exécute des opérations en parallèle (chargement de données ou requête, par exemple) sur un plus grand nombre de ressources distribuées. Cela réduit la latence et améliore les performances.

Tout entrepôt de données possède 2 mesures fondamentales des performances :

- Taux de charge. Nombre d’enregistrements qui peuvent être chargés dans l’entrepôt de données par seconde. Nous évaluons spécifiquement le nombre d’enregistrements qui peuvent être importés, via PolyBase, à partir du stockage des objets blob Azure vers une table avec un index de stockage des colonnes en cluster.
- Taux d’analyse. Nombre d’enregistrements qui peuvent être séquentiellement extraits de l’entrepôt de données par seconde. Nous mesurons précisément le nombre d’enregistrements renvoyés par une requête sur un index de stockage des colonnes en cluster.

Nous mesurons certaines améliorations importantes des performances et partagerons bientôt les taux attendus. Durant la phase préliminaire, nous apporterons des améliorations continues (par exemple, l’augmentation de la compression et la mise en cache) afin d’augmenter ces taux et de garantir leur mise à l’échelle de façon prévisible.

## Protection des données

SQL Data Warehouse stocke l’ensemble des données dans Azure Storage à l’aide du stockage localement redondant. Plusieurs copies synchrones des données sont conservées dans le centre de données local afin de garantir la protection transparente des données en cas de défaillance localisée.

## Sauvegardes

SQL Data Warehouse d’azure sauvegarde toutes les données au moins toutes les 8 heures à l’aide des captures instantanées d’Azure Storage. Ces instantanés sont conservés pendant 7 jours. Ce système permet de restaurer les données suivant 21 points différents dans le temps au cours des 7 derniers jours, jusqu’au moment de la prise du dernier instantané. Les données peuvent être restaurées à partir d’un instantané à l’aide de PowerShell ou des API REST.

## Fiabilité des requêtes

SQL Data Warehouse repose sur une architecture MPP (Massively Parallel Processing). SQL Data Warehouse détecte automatiquement les défaillances de nœud de calcul et de contrôle, et les migre. Toutefois, une opération (par exemple, un chargement de données ou une requête) peut échouer suite à une défaillance ou une migration de nœud. Lors de la phase préliminaire, nous apportons des améliorations continues pour terminer avec succès les opérations en dépit des défaillances de nœud.


## Mises à niveau et temps d'arrêt

SQL Data Warehouse sera mis à niveau périodiquement afin d’ajouter de nouvelles fonctionnalités et installer des correctifs critiques. Ces mises à niveau peuvent entraîner des perturbations et ne sont pas effectuées selon un calendrier prévisible pour l’instant. Si vous trouvez que ce processus provoque trop de perturbations, nous vous encourageons à [créer un ticket de support][]. Nous pourrons ainsi vous aider à contourner ce processus.


## Étapes suivantes

[Prise en main][] avec la version préliminaire publique.

<!--Image references-->

<!--Article references-->
[créer un ticket de support]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Prise en main]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0608_2016-->