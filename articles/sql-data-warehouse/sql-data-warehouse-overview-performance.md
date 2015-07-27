<properties
   pageTitle="Vue d’ensemble des performances et de la mise à l’échelle | Microsoft Azure"
   description="Introduction aux fonctionnalités de performances et de mise à l’échelle de SQL Data Warehouse."
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
   ms.date="06/24/2015"
   ms.author="barbkess;JRJ@BigBangData.co.uk;mausher;nicw"/>

# Vue d’ensemble des performances et de la mise à l’échelle
En plaçant votre solution Data Warehouse dans le cloud, vous éliminez les problématiques matérielles de niveau inférieur. L’époque où vous deviez identifier les types de processeurs, le volume de mémoire et le type de stockage nécessaires à la satisfaction des besoins en performances de votre entrepôt de données est désormais révolue. Aujourd’hui, SQL Data Warehouse vous pose cette question : À quelle vitesse souhaitez-vous traiter vos données ?

SQL Data Warehouse est une plateforme de base de données distribuée, de type cloud qui est conçue pour offrir de hauts niveaux de performances à l’échelle dans un contexte où vous êtes pleinement maître des ressources utilisées pour la résolution de vos requêtes. En ajustant simplement le nombre d’unités allouées à votre entrepôt de données, vous pouvez mettre en toute flexibilité à l’échelle la taille de vos ressources en quelques secondes. En vous dotant de SQL Data Warehouse, plateforme distribuée et évolutive, vous disposez d’un entrepôt de données qui traite efficacement d’importants volumes de données et conservez toute latitude sur le coût de votre solution.

*Les informations suivantes s’appliquent à la version préliminaire de Microsoft Azure SQL Data Warehouse. Ces informations seront continuellement mises à jour durant la phase préliminaire, à mesure que la disponibilité générale est adoptée dans le service.*

Les objectifs que nous avons définis pour SQL Data Warehouse sont les suivants : - Performances prévisibles et évolutivité linéaire pour l’ensemble des opérations de l’entrepôt, appuyées par un contrat de niveau de service - Réduction de l’intervalle entre le chargement et l’analyse des données relationnelles et non relationnelles

Nous poursuivrons sans relâche ces objectifs durant la phase préliminaire, ceci pour les atteindre préalablement à la disponibilité générale.

>[AZURE.NOTE]Les sections ci-dessous décrivent la version préliminaire publique du service Microsoft Azure SQL Data Warehouse. Ces informations seront continuellement mises à jour durant la phase préliminaire, à mesure que la haute disponibilité est adoptée dans le service.

## Protection des données
SQL Data Warehouse stocke l’ensemble des données dans Microsoft Azure Storage à l’aide d’objets Blob géo-redondants. Trois copies synchrones des données sont conservées dans la région locale Microsoft Azure afin de garantir la protection transparente des données en cas de défaillance localisée (comme un dysfonctionnement de disque de stockage). Par ailleurs, trois autres copies asynchrones sont conservées dans une région Microsoft Azure distante, ceci pour garantir la protection des données en cas de défaillance régionale (récupération d’urgence). Les régions locales et distantes sont associées, ce qui permet de maintenir des latences de synchronisation acceptables (par exemple entre les côtes Est et Ouest des États-Unis).

## Restauration de base de données
SQL Data Warehouse sauvegarde l’ensemble des données toutes les 4 heures à l’aide d’instantanés Microsoft Azure Storage. Ces instantanés sont conservés gratuitement pendant 7 jours. Ce système permet de restaurer les données suivant 42 points différents dans le temps au cours des 7 derniers jours, jusqu’à la prise de l’instantané. Durant la phase préliminaire, la capacité de définir des valeurs de rétention différentes sera introduite. Les données peuvent être restaurées à partir d’un instantané à l’aide de PowerShell ou d’API REST. Les instantanés sont copiés de manière asynchrone vers une région distante Microsoft Azure afin d’optimiser la récupération en cas de défaillance régionale (récupération d’urgence).

## Fiabilité
SQL Data Warehouse est un système distribué comportant de multiples composants, qui augmentent en nombre parallèlement à l’augmentation de la quantité d’unités DWU. SQL Data Warehouse détecte et limite automatiquement les défaillances des ressources de calcul. Cependant, une opération (comme un chargement de données ou une requête) peut être mise en échec en raison de défaillances de calcul individuelles. Durant la version préliminaire, nous allons apporter des améliorations continues de la fiabilité des requêtes afin d’augmenter la probabilité de réussite des opérations perturbées par des défaillances.

Selon les données de télémétrie recueillies, la fiabilité de SQL Data Warehouse est estimée à 98 % avec des charges de travail standard d’entrepôts de données. Cela signifie que, en moyenne, 2 requêtes sur 100 sont mises en échec en raison d’erreurs système. Il ne s’agit pas d’un contrat de niveau de service associé à la phase préliminaire, plutôt d’un indicateur de la fiabilité attendue des requêtes exécutées. Notez que la probabilité de défaillance des requêtes augmente en parallèle de la durée d’exécution (les requêtes d’une durée supérieure à 2 heures sont davantage susceptibles d’être mises en échec que les requêtes d’une durée inférieure à 10 minutes). Durant la phase préliminaire, nous apporterons des améliorations continues afin de garantir le maintien d’un niveau identique de fiabilité pour l’ensemble des opérations, indépendamment de leur durée d’exécution. Nous modifierons les prévisions de fiabilité au fur et à mesure des améliorations, avec pour objectif de fournir un contrat de niveau de service complet avec la disponibilité générale.

Durant la phase préliminaire, SQL Data Warehouse peut présenter jusqu’à 5 événements de maintenance par mois, dédiés à l’installation des correctifs critiques. Chacun des événements peut entraîner des défaillances de requêtes pendant un intervalle maximal de 2 heures, suivant la quantité d’unités DWU utilisées par l’instance SQL Data Warehouse. Nous ferons notre possible pour prévenir nos clients 48 heures à l’avance, et ainsi leur donner les moyens de s’y préparer convenablement.

## Performances et évolutivité
SQL Data Warehouse introduit des unités DWU (Data Warehouse Unit) en tant qu’abstraction des ressources de calcul (processeurs, mémoire, E/S de stockage) agrégées sur différents nœuds. En vous dotant d’un nombre plus conséquent d’unités DWU, vous augmentez les ressources de calcul agrégées d’une instance SQL Data Warehouse. SQL Data Warehouse distribue les opérations (comme le chargement de données ou les requêtes) sur l’ensemble de l’infrastructure de calcul de l’instance afin d’augmenter ou de réduire les performances des chargements ou des requêtes à mesure des montées et descentes en puissance du système.

Un entrepôt de données présente 2 mesures essentielles des performances : - **Taux de charge** : nombre d’enregistrements pouvant être chargés par seconde dans l’entrepôt de données. Nous mesurons spécifiquement le nombre d’enregistrements pouvant être importés, via PolyBase, à partir des objets Blob Microsoft Azure Storage dans une table présentant un index Column-Store cluster. - **Taux d’analyse **: nombre d’enregistrements pouvant être séquentiellement récupérés par seconde de l’entrepôt de données. Cette mesure évalue précisément le nombre d’enregistrements renvoyés par une requête d’une table présentant un index Column-Store cluster.

Durant la phase préliminaire, nous apporterons des améliorations continues afin d’augmenter ces taux et de garantir la prévisibilité de leur mise à l’échelle.

## Concepts clés de performances

Consultez les articles suivants afin de mieux comprendre certains concepts supplémentaires clés de performance et de mise à l’échelle :

- [performances et mise à l’échelle][]
- [modèle concurrentiel][]
- [tables de conception][]
- [associer une clé de distribution par hachage à votre table][]
- [statistiques pour améliorer les performances][]

## Étapes suivantes
Consultez la [vue d’ensemble sur le développement][] afin de bénéficier de recommandations sur le développement d’une solution SQL Data Warehouse.

<!--Image references-->

<!--Article references-->

[performances et mise à l’échelle]: sql-data-warehouse-performance-scale.md
[modèle concurrentiel]: sql-data-warehouse-develop-concurrency.md
[tables de conception]: sql-data-warehouse-develop-table-design.md
[associer une clé de distribution par hachage à votre table]: sql-data-warehouse-develop-hash-distribution-key
[statistiques pour améliorer les performances]: sql-data-warehouse-develop-statistics.md
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other web references-->

<!---HONumber=July15_HO3-->