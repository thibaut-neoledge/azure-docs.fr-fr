<properties
   pageTitle="Attentes de SQL Data Warehouse preview | Microsoft Azure"
   description="Résumé des fonctionnalités de la version préliminaire publique et de nos objectifs pour la disponibilité générale de SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lvargas"
   manager="tonypet"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="lvargas;twounder;barbkess"/>

# Attentes de SQL Data Warehouse preview

Cet article décrit les fonctionnalités de SQL Data Warehouse preview et nos objectifs pour le service de disponibilité générale. Nous mettrons régulièrement à jour ces informations au fur et à mesure que nous améliorerons les fonctionnalités de la version préliminaire publique.

Nos objectifs pour SQL Data Warehouse :

- Performances prévisibles et évolutivité linéaire jusqu’à plusieurs pétaoctets de données.
- Fiabilité élevée pour toutes les opérations d’entrepôt de données, soutenue par un contrat de niveau de service (SLA).

Nous tendrons en permanence vers ces objectifs avant la promotion de SQL Data Warehouse en disponibilité générale.

## Performances prévisibles et évolutives

Azure SQL Data Warehouse présente les unités DWU (Data Warehouse Units) comme un moyen de mesurer les ressources informatiques (processeurs, mémoire, stockage E/S) disponibles pour l’entrepôt de données. L’augmentation du nombre de DWU augmente les ressources. À mesure que le nombre de DWU augmente, SQL Data Warehouse exécute des opérations en parallèle (chargement de données ou requête, par exemple) sur un plus grand nombre de ressources distribuées. Cela réduit la latence et améliore les performances.

Tout entrepôt de données possède 2 mesures fondamentales des performances :

- Taux de charge. Nombre d’enregistrements qui peuvent être chargés dans l’entrepôt de données par seconde. Nous évaluons spécifiquement le nombre d’enregistrements qui peuvent être importés, via PolyBase, à partir du stockage des objets blob Azure vers une table avec un index de stockage des colonnes en cluster. 
- Taux d’analyse. Nombre d’enregistrements qui peuvent être séquentiellement extraits de l’entrepôt de données par seconde. Nous mesurons précisément le nombre d’enregistrements renvoyés par une requête sur un index de stockage des colonnes en cluster.


Nous mesurons certaines améliorations importantes des performances et partagerons bientôt les taux attendus. Durant la phase préliminaire, nous apporterons des améliorations continues (par exemple, l’augmentation de la compression et la mise en cache) afin d’augmenter ces taux et de garantir leur mise à l’échelle de façon prévisible.


## Fiabilité élevée soutenue par un contrat SLA

### Protection des données 

SQL Data Warehouse stocke l’ensemble des données dans Microsoft Azure Storage à l’aide d’objets blob géo-redondants. Trois copies synchrones des données sont conservées dans la région locale Microsoft Azure afin de garantir la protection transparente des données en cas de défaillance localisée (comme un dysfonctionnement de disque de stockage). Par ailleurs, trois autres copies asynchrones sont conservées dans une région Microsoft Azure distante, ceci pour garantir la protection des données en cas de défaillance régionale (récupération d’urgence). Les régions locales et distantes sont associées, ce qui permet de maintenir des latences de synchronisation acceptables (par exemple entre les côtes Est et Ouest des États-Unis).


### Sauvegardes

SQL Data Warehouse d’azure sauvegarde toutes les données au moins toutes les 8 heures à l’aide des captures instantanées d’Azure Storage. Ces instantanés sont conservés pendant 7 jours. Ce système permet de restaurer les données suivant 21 points différents dans le temps au cours des 7 derniers jours, jusqu’au moment de la prise du dernier instantané. Les données peuvent être restaurées à partir d’un instantané à l’aide de PowerShell ou des API REST.

Les instantanés sont copiés de manière asynchrone vers une région distante Microsoft Azure afin d’optimiser la récupération en cas de défaillance régionale (récupération d’urgence).


### Achèvement de requête 

SQL Data Warehouse stocke les données sur un ou plusieurs nœuds de calcul qui, chacun, contient certaines des données utilisateur et contrôle l’exécution des requêtes sur ces données. Dans le cadre de l’architecture de traitement parallèle massif, les requêtes s’exécutent en parallèle sur les nœuds de calcul. SQL Data Warehouse détecte automatiquement les défaillances de nœud de calcul et les atténue. Toutefois, lors de la phase préliminaire, une opération (chargement de données ou requête, par exemple) peut échouer en raison d’échecs de nœud individuel. Lors de la phase préliminaire, nous apportons des améliorations continues pour terminer avec succès les opérations en dépit des défaillances de nœud.

Selon les données de télémétrie, nous estimons que la fiabilité actuelle d’Azure SQL Data Warehouse est de 98 %. Cela signifie que, en moyenne, 2 requêtes sur 100 peuvent échouer en raison d’erreurs système. Ce n’est pas un contrat SLA. La probabilité d’échec d’une requête augmente avec sa durée d’exécution. Par exemple, une requête qui dure plus de 2 heures a une probabilité nettement plus élevée d’échouer qu’une requête qui dure moins de 10 minutes. Durant la phase préliminaire, nous améliorons la fiabilité afin de garantir le même niveau de fiabilité pour l’ensemble des opérations, indépendamment de leur durée d’exécution. Nous mettrons à jour la fiabilité escomptée au fur et à mesure que nous publions les améliorations. Dans le cadre de la disponibilité générale, la fiabilité bénéficie d’un contrat SLA.

### Disponibilité du service

Azure SQL Data Warehouse peut présenter jusqu’à 4 événements de maintenance par mois, dédiés à l’installation des correctifs critiques. Chaque événement peut entraîner un échec des requêtes jusqu’à 2 heures au plus. Le temps dépend du nombre de DWU alloué au service. Nous nous efforcerons de notifier ces événements 48 heures à l’avance.


## Étapes suivantes

[Prise en main][] avec la version préliminaire publique.

<!--Image references-->

<!--Article references-->
[Prise en main]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=Oct15_HO3-->