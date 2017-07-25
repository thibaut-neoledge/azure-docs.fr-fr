---
title: "En quoi consiste Azure SQL Data Warehouse ? | Microsoft Docs"
description: "Base de données distribuée dédiée aux entreprises qui prend en charge le traitement de pétaoctets de données relationnelles et non relationnelles. Il s’agit du premier entrepôt de données cloud prenant en charge l’augmentation, la réduction et la pause en quelques secondes."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: bjhubbard
editor: 
ms.assetid: 4006c201-ec71-4982-b8ba-24bba879d7bb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 2/28/2017
ms.author: jrj;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 575c49f83c8845edcea984459f3907490c62d269
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017


---
# <a name="what-is-azure-sql-data-warehouse"></a>En quoi consiste Azure SQL Data Warehouse ?
Azure SQL Data Warehouse est une base de données relationnelle de mise à l’échelle basée sur le cloud à traitement massivement parallèle (MPP, Massively Parallel Processing) qui prend en charge le traitement de grands volumes de données. 

SQL Data Warehouse :

* Combine notre base de données relationnelle SQL Server avec les capacités de mise à l’échelle du cloud Azure. 
* Découple le stockage du calcul.
* Active l’augmentation, la réduction, la suspension ou la reprise du calcul. 
* S’intègre à l’ensemble de la plateforme Azure.
* Utilise SQL Server Transact-SQL (T-SQL) et les outils.
* Est conforme à différentes exigences juridiques et commerciales de sécurité telles SOC et ISO.

Cet article décrit les principales fonctionnalités de SQL Data Warehouse.

## <a name="massively-parallel-processing-architecture"></a>Architecture Massively Parallel Processing
SQL Data Warehouse est un système de base de données distribuée à traitement massivement parallèle. En arrière-plan, SQL Data Warehouse propage vos données sur de nombreuses unités de traitement et de stockage sans partage. Les données sont stockées dans une couche de stockage Premium redondante en local à partir de laquelle les nœuds de calcul liés de manière dynamique exécutent des requêtes. SQL Data Warehouse adopte une approche « Diviser pour mieux régner » pour l’exécution de charges et de requêtes complexes. Les demandes sont reçues par un nœud de contrôle, optimisées pour la distribution, puis transmises aux nœuds de calcul pour effectuer leur travail en parallèle.

Avec le stockage découplé et le calcul, SQL Data Warehouse peut :

* Agrandir ou réduire la taille du stockage indépendant de calcul.
* Augmenter ou réduire la puissance de calcul sans déplacement de données.
* Suspendre le calcul tout en conservant les données intactes, afin de ne payer que le stockage.
* Reprendre le calcul pendant les heures d’utilisation.

Le diagramme suivant illustre l’architecture plus en détail.

![Architecture de SQL Data Warehouse][1]

**Nœud de contrôle :** le nœud de contrôle gère et optimise les requêtes. Il s’agit du nœud frontal qui interagit avec toutes les applications et les connexions. Dans SQL Data Warehouse, le nœud de contrôle s’appuie sur une base de données SQL ; une fois la connexion établie, vous obtenez une interface et un fonctionnement similaire. Sous la surface, le nœud de contrôle coordonne tous les mouvements de données et calculs nécessaires pour exécuter des requêtes parallèles sur vos données distribuées. Lorsque vous envoyez une requête TSQL à SQL Data Warehouse, le nœud de contrôle la transforme en requêtes distinctes qui s’exécutent sur chaque nœud de calcul en parallèle.

**Nœuds de calcul :** les nœuds de calcul alimentent SQL Data Warehouse. Il s’agit de bases de données SQL qui stockent vos données et traitent votre requête. Lors de l’ajout de données, SQL Data Warehouse distribue les lignes à vos nœuds de calcul. Les nœuds de calcul sont les travaux qui exécutent les requêtes parallèles sur vos données. Après traitement, ils renvoient les résultats au nœud de contrôle. Pour terminer la requête, le nœud de contrôle rassemble les résultats et renvoie le résultat final.

**Stockage :** les données sont stockées dans Azure Blob Storage. Lorsque les nœuds de calcul interagissent avec vos données, la lecture et l’écriture s’effectuent directement vers et depuis le stockage d’objets blob. Dans la mesure où le stockage Azure se développe de manière transparente et à grande échelle, SQL Data Warehouse peut en faire de même. Étant donné que le calcul et le stockage sont indépendants, SQL Data Warehouse peut automatiquement mettre à l’échelle le stockage séparément du calcul de mise à l’échelle et inversement. Azure Blob Storage offre également une tolérance complète aux pannes et rationalise le processus de sauvegarde et de restauration.

**Service de déplacement de données :** DMS déplace les données entre les nœuds. DMS permet aux nœuds de calcul d’accéder aux données dont ils ont besoin pour les jonctions et les agrégations. DMS n’est pas un service Azure. C’est un service Windows qui s’exécute en même temps que la base de données SQL sur tous les nœuds. DMS est un processus en arrière-plan avec lequel il est impossible d’interagir directement. Toutefois, vous pouvez examiner les plans de requête pour savoir à quel moment les opérations DMS se produisent, dans la mesure où le déplacement des données est nécessaire pour exécuter chaque requête en parallèle.

## <a name="optimized-for-data-warehouse-workloads"></a>Optimisé pour les charges de l’entrepôt de données
Cette approche MPP est facilitée par plusieurs optimisations des performances spécifiques à l’entreposage de données, notamment :

* Un optimiseur de requêtes distribuées et un ensemble de statistiques complexes sur toutes les données. À partir des informations relatives à la distribution et à la taille des données, le service est en mesure d’optimiser les requêtes en évaluant le coût des opérations de requête distribuée spécifique.
* Des algorithmes et des techniques avancés intégrés au processus de déplacement de données pour déplacer efficacement les données entre les ressources de calcul dans le cadre de l’opération de requête. Ces opérations de mouvement de données sont intégrées et toutes les optimisations apportées à DMS s’effectuent automatiquement.
* Des index **columnstore** cluster par défaut. En s’appuyant sur le stockage en colonnes, SQL Data Warehouse peut délivrer des performances de compression jusqu’à 5 fois supérieures au stockage en ligne, et des performances de requête jusqu’à 10 fois ou plus supérieures. Les requêtes d’analyse qui doivent analyser un grand nombre de lignes fonctionnent mieux avec les index de stockage de colonnes cluster.


## <a name="predictable-and-scalable-performance-with-data-warehouse-units"></a>Performances prévisibles et évolutives avec des unités DWU (Data Warehouse Units)
SQL Data Warehouse est construit avec des technologies similaires comme SQL Database, ce qui signifie que les utilisateurs peuvent attendre des performances cohérentes et prévisibles pour les requêtes analytiques. Les utilisateurs devraient voir une mise à l’échelle des performances de façon linéaire lorsqu’ils ajoutent ou retirent des nœuds de calcul. L’allocation des ressources dans votre SQL Data Warehouse est exprimée en Data Warehouse Units (DWU). Les DWU sont une mesure des ressources sous-jacentes, telles que le processeur, la mémoire et les E/S, qui sont allouées à votre SQL Data Warehouse. L’augmentation du nombre de DWU augmente les ressources et les performances. Plus précisément, les DWU sont utilisés pour :

* Vous avez la possibilité d’étendre votre entrepôt de données sans vous préoccuper du matériel ou des logiciels sous-jacents.
* Vous pouvez prévoir l’amélioration des performances en termes de DWU avant de changer le calcul de votre entrepôt de données.
* vous permettre de modifier ou de déplacer le matériel et les logiciels sous-jacents de votre instance sans incidence sur les performances de votre charge de travail ;
* Microsoft peut améliorer l’architecture sous-jacente du service sans affecter les performances de votre charge de travail.
* Microsoft peut améliorer rapidement les performances de SQL Data Warehouse d’une manière évolutive et qui impacte uniformément le système.

Les Data Warehouse Units fournissent une mesure de trois indicateurs qui sont fortement corrélés aux performances de la charge de travail d’entreposage de données. Les mesures de charge de travail clés suivantes sont mises à l’échelle de manière linéaire avec les DWU.

**Analyse/agrégation :** requête d’entreposage de données standard qui analyse un grand nombre de lignes avant d’effectuer une agrégation complexe. C’est une opération très gourmande en E/S et en ressources processeur.

**Charge :** capacité à injecter des données dans le service. Les charges sont mieux exécutées avec PolyBase à partir d’Azure Storage Blobs ou d’Azure Data Lake. Cette mesure est conçue pour souligner les aspects réseau et processeur du service.

**Create Table As Select (CTAS) :** CTAS mesure la capacité à créer une copie d’une table. Cela implique la lecture des données sur le système de stockage, leur distribution entre les nœuds de l’appliance et la réécriture dans le système de stockage. Cette opération est très gourmande en ressources processeur, E/S et réseau.

## <a name="built-on-sql-server"></a>Basé sur SQL Server
SQL Data Warehouse s’appuie sur le moteur de base de données relationnelle de SQL Server et inclut les nombreuses fonctionnalités que vous pouvez attendre d’un entrepôt de données d’entreprise. Si vous connaissez déjà T-SQL, il vous suffit de transposer vos connaissances sur SQL Data Warehouse. Que vous ayez atteint un stade avancé ou que vous débutiez, les exemples fournis dans la documentation peuvent aider à prendre un bon départ. En règle générale, vous pouvez considérer la façon dont nous avons construit les éléments de langage de SQL Data Warehouse comme suit :

* SQL Data Warehouse utilise la syntaxe T-SQL pour de nombreuses opérations. Il prend en charge un large éventail de constructions SQL traditionnelles, telles que les procédures stockées, les fonctions définies par l’utilisateur, le partitionnement de tables, les index et les classements.
* SQL Data Warehouse contient également différentes fonctionnalités SQL Server inédites, notamment les index **columnstore** en cluster, l’intégration PolyBase et l’audit de données (complété par l’évaluation des menaces).
* Certains éléments de langage TSQL moins courants pour les charges de travail d’entreposage de données ou plus récemment ajoutés à SQL Server peuvent être indisponibles pour le moment. Pour plus d’informations, consultez la [documentation relative à la migration][Migration documentation].

En profitant de Transact-SQL et de fonctionnalités identiques entre SQL Server, SQL Data Warehouse, la base de données SQL et Analytics Platform System, vous êtes en mesure de développer une solution adaptée à vos besoins. Vous pouvez définir l’emplacement de stockage de vos données, en fonction des exigences de performance, de sécurité et de mise à l’échelle, puis, en fonction des besoins, transférer vos données entre différents systèmes.

## <a name="data-protection"></a>Protection des données
SQL Data Warehouse stocke l’ensemble des données dans le stockage Azure Premium localement redondant. Plusieurs copies synchrones des données sont conservées dans le centre de données local afin de garantir la protection transparente des données contre une défaillance localisée. En outre, SQL Data Warehouse sauvegarde automatiquement vos bases de données actives (réactivées) à intervalles réguliers à l’aide d’instantanés Azure Storage. Pour plus d’informations sur le fonctionnement des processus de sauvegarde et de restauration, voir une [vue d’ensemble des fonctionnalités de sauvegarde et de restauration][Backup and restore overview].

## <a name="integrated-with-microsoft-tools"></a>Intégré avec les outils Microsoft
SQL Data Warehouse s’intègre également à de nombreux outils que les utilisateurs de SQL Server sont susceptibles de connaître. Ces outils incluent :

**Outils SQL Server traditionnels :** SQL Data Warehouse est totalement intégré à SQL Server Analysis Services, Integration Services et Reporting Services.

**Des outils basés sur le cloud :** SQL Data Warehouse peut être intégré à différents services dans Azure, notamment Data Factory, Stream Analytics, Machine Learning et Power BI. Pour une liste plus complète, consultez [Vue d’ensemble des outils intégrés][Integrated tools overview].

**Des outils tiers :** de nombreux fournisseurs d’outils tiers ont certifié l’intégration de leurs outils avec SQL Data Warehouse. Pour obtenir une liste complète, consultez [Partenaires de solutions SQL Data Warehouse][SQL Data Warehouse solution partners].

## <a name="hybrid-data-sources-scenarios"></a>Scénarios de sources de données hybrides
PolyBase vous permet de valoriser vos données de différentes sources en appliquant les commandes T-SQL que vous connaissez. PolyBase vous permet d’interroger des données non relationnelles conservées dans Azure Blob Storage comme des tables standard. Utilisez PolyBase pour interroger des données non relationnelles ou pour importer des données non relationnelles dans SQL Data Warehouse.

* PolyBase accède aux données non relationnelles à l’aide de tables externes. Les définitions de table sont stockées dans SQL Data Warehouse, et vous pouvez y accéder par SQL et par les outils de la même manière que vous accéderiez à des données relationnelles normales.
* PolyBase assure une intégration standard. La solution expose des fonctionnalités identiques à l’ensemble des sources qu’elle prend en charge. Les données lues par PolyBase peuvent se présenter sous différents formats, notamment les fichiers délimités ou ORC.
* PolyBase peut être utilisé pour accéder au Blob Storage qui est également utilisé comme espace de stockage pour un cluster HD Insight. Cela vous permet d’accéder aux mêmes données avec des outils relationnels et non relationnels.

## <a name="sla"></a>Contrat SLA
SQL Data Warehouse offre un contrat de niveau de service pour les produits dans le cadre du Contrat SLA des services en ligne Microsoft. Pour plus d’informations, consultez [SLA pour SQL Data Warehouse][SLA for SQL Data Warehouse]. Pour des informations de contrat SLA concernant tous les autres produits, vous pouvez visiter la page Azure [Contrats de niveau de service] ou téléchargez-les sur la page [Licences en volume][Volume Licensing]. 

## <a name="next-steps"></a>Étapes suivantes
À présent que vous en savez un peu plus sur SQL Data Warehouse, découvrez comment [créer rapidement un SQL Data Warehouse][create a SQL Data Warehouse] et [charger des exemples de données][load sample data]. Si vous n’êtes pas encore familiarisé avec Azure, vous pouvez vous appuyer sur le [Glossaire Azure][Azure glossary] lorsque vous rencontrez de nouveaux termes. Ou bien, consultez ces autres ressources de SQL Data Warehouse.  

* [Témoignages de clients]
* [Blogs]
* [Demandes de fonctionnalités]
* [Vidéos]
* [Blogs de l’équipe de conseil clientèle]
* [Création d’un ticket de support]
* [Forum MSDN]
* [Forum Stack Overflow]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Création d’un ticket de support]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Témoignages de clients]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs de l’équipe de conseil clientèle]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Demandes de fonctionnalités]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Forum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vidéos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Contrats de niveau de service]: https://azure.microsoft.com/en-us/support/legal/sla/

