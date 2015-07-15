<properties
   pageTitle="Présentation de SQL Data Warehouse | Microsoft Azure"
   description="Base de données distribuée dédiée aux entreprises de Microsoft Azure Cloud prenant en charge le traitement de pétaoctets de données relationnelles et non relationnelles. Il s’agit du premier entrepôt de données cloud prenant en charge l’augmentation, la réduction et la pause en quelques secondes."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/23/2015"
   ms.author="barbkess;JRJ@BigBangData.co.uk;"/>

# En quoi consiste la solution SQL Data Warehouse ?

SQL Data Warehouse est une base de données distribuée dédiée aux entreprises de Microsoft Azure Cloud qui prend en charge le traitement de pétaoctets de données relationnelles et non relationnelles. Il s’agit du premier entrepôt de données cloud prenant en charge l’augmentation, la réduction et la pause en quelques secondes.

SQL Data Warehouse associe le meilleur de la qualité et de la fiabilité de la base de données relationnelle de niveau entreprise au modèle de cloud computing. En savoir plus sur les composantes clés de conception de SQL Data Warehouse.

## Accroître, réduire ou interrompre les ressources de calcul
Avec SQL Data Warehouse, les données sont stockées dans un emplacement de type cloud et les ressources de calcul sont mises à l’échelle indépendamment, ce qui vous permet d’adopter un modèle de tarification évolutif, suivant vos besoins. Vous pouvez désormais déployer, accroître, réduire et même interrompre les ressources de stockage. Tirez pleinement parti du stockage de type cloud et appliquez les ressources de requêtes en fonction des besoins périodiques. Lorsque les ressources sont interrompues, vous payez uniquement le stockage.

> [AZURE.NOTE]Les unités Data Warehouse Units (DWU) sont dédiées à la mise à l’échelle des ressources de calcul dans SQL Data Warehouse.

- Lorsque vous souhaitez accélérer les opérations, il vous suffit d’accroître votre équipement DWU en investissant en conséquence. Lorsque vous n’avez plus besoin d’une telle puissance de calcul, réduisez votre équipement de DWU, ainsi que vos investissements. La puissance de calcul croît proportionnellement au nombre de DWU. En doublant le nombre de DWU, vous doublez les ressources de calcul. 
- Lorsque vous n’avez pas besoin d’exécuter des requêtes, par exemple le soir ou le week-end, interrompez les ressources de calcul afin d’annuler l’ensemble des requêtes en cours et supprimez l’ensemble des unités DWU allouées à votre entrepôt de données. Votre stockage de données demeure intact, mais vous vous épargnez les frais associés aux ressources de calcul. Quand vous devez relancer les requêtes, par exemple le lundi matin, vous pouvez faire repartir vos ressources de calcul. 

## Traitement massivement parallèle et index columnstore afin d’optimiser les performances
SQL Data Warehouse utilise l’architecture de traitement massivement parallèle et la technologie d’index columnstore de SQL Server pour vous offrir des performances exceptionnelles.

> [AZURE.NOTE]Le traitement massivement parallèle est une approche de type « Diviser pour régner » de résolution des problématiques liées aux données. Les données sont réparties et distribuées entre de nombreuses ressources de calcul, qui opèrent toutes en parallèle sur leur portion de données respective.

- Cette configuration est portée en grande partie par la technologie distribuée de requête de Microsoft. SQL Data Warehouse valorise un optimiseur avancé de requête qui détermine comment optimiser les requêtes distribuées en fonction du coût des opérations de requête. La solution est dotée de techniques et d’algorithmes avancés qui déplacent efficacement les données entre les ressources de calcul dans le cadre de l’opération de requête.
- Les index columnstore sont essentiels au maintien de la rapidité des requêtes dirigées sur les entrepôts de données. En s’appuyant sur le stockage en colonnes, les index Columnstore proposent des performances de compression jusqu’à 5 fois supérieures au stockage en ligne, et des performances de requête jusqu’à 10 fois supérieures. Les requêtes dirigées vers l’entrepôt de données sont très efficaces sur les index columnstore, car elles analysent bien souvent l’intégralité de la table ou la partition intégrale d’une table. A contrario, les requêtes OLTP fonctionnent très bien sur les index d’arborescences binaires, car elles ciblent des lignes spécifiques de la table.


## Cloud hybride avec l’expérience SQL-Server de niveau entreprise
SQL Data Warehouse, qui s’appuie sur le moteur éprouvé de base de données relationnelle de SQL Server, propose les fonctionnalités attendues d’un entrepôt de données d’entreprise, notamment des procédures stockées, des fonctions définies par l’utilisateur, un partitionnement des tables, des index et des classements.

> [AZURE.NOTE]Si vous connaissez déjà Transact-SQL, il vous suffit de transposer vos connaissances sur SQL Data Warehouse. Vous êtes un expert ? Un novice ? Dans tous les cas, les exemples de la documentation vous mettent le pied à l’étrier.

- SQL Data Warehouse utilise les technologies Transact-SQL, des index columnstore et PolyBase avec l’architecture de traitement massivement parallèle d’Analytic Platform System afin de développer cette expérience unique, intégrée d’entreposage de données PaaS (Platform-as-a-Service).  

- En profitant de Transact-SQL et de fonctionnalités identiques entre SQL Server, SQL Data Warehouse, la base de données SQL et Analytics Platform System, vous êtes en mesure de développer une solution adaptée à vos besoins. Vous pouvez définir l’emplacement de stockage de vos données, en fonction des exigences de performance, de sécurité et de mise à l’échelle, puis, en fonction des besoins, transférer vos données entre les installations sur site et le cloud.


## Interroger des données relationnelles et non relationnelles
PolyBase vous permet d’interroger des données non relationnelles conservées dans le stockage d’objets Blob Microsoft Azure ou dans Hadoop File System (HDFS) comme des tables standard. Utilisez PolyBase pour interroger des données non relationnelles ou pour importer des données non relationnelles dans SQL Data Warehouse.

> [AZURE.NOTE]PolyBase, simple d’utilisation, vous permet de valoriser vos données de différentes sources en appliquant les commandes SQL que vous connaissez déjà. Il n’est pas nécessaire de vous familiariser avec HiveQL ou d’autres langages pour tirer le meilleur parti de Hadoop.

- PolyBase assure une intégration standard. La solution expose des fonctionnalités identiques à l’ensemble des distributions de Hadoop qu’elle prend en charge. Les données lues par PolyBase peuvent se présenter sous différents formats, notamment les fichiers délimités ou ORC.
- PolyBase accède aux données non relationnelles à l’aide de tables externes. Les définitions de table sont stockées dans SQL Data Warehouse, et les données sont stockées en externe dans Hadoop ou dans le stockage d’objets Blob Microsoft Azure.


## Sécuriser le déploiement d’infrastructure sans aucun coût de maintenance
SQL Data Warehouse se déploie facilement, en quelques secondes. Ce service est une offre entièrement gérée, qui vous permet d’éliminer les efforts consacrés aux correctifs logiciels et à la maintenance. SQL Data Warehouse fournit des sauvegardes intégrées permettant de prendre en charge la restauration en libre-service. Le service sauvegarde automatiquement vos données dans Microsoft Azure Storage à mesure qu’il saisit des instantanés des points de restauration de base de données.


## Étapes suivantes
En savoir plus sur la [charge de travail de l’entrepôt de données]

<!--Image references-->

<!--Article references-->
[charge de travail de l’entrepôt de données] sql-data-warehouse-overview-workload.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO1-->