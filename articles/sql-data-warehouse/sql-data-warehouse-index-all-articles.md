<properties
	pageTitle="Toutes les rubriques pour le service SQL Data Warehouse | Microsoft Azure"
	description="Tableau de toutes les rubriques du service Azure nommé SQL Data Warehouse, qui existent sur http://azure.microsoft.com/documentation/articles/, titre et description."
	services="sql-data-warehouse"
	documentationCenter=""
	authors="barbkess"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-data-warehouse"
	ms.workload="sql-data-warehouse"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2016"
	ms.author="barbkess"/>


# Toutes les rubriques pour le service SQL Data Warehouse

Cette rubrique répertorie toutes les rubriques qui s'appliquent directement au service **SQL Data Warehouse**. Pour trouver les rubriques qui vous intéressent, vous pouvez rechercher des mots clés sur cette page web à l'aide de la combinaison de touches **Ctrl+F**.



## Articles mis à jour

Cette section répertorie les articles qui ont été mis à jour récemment et dont la mise à jour a été volumineuse ou importante. Pour chaque article mis à jour, un extrait de code approximatif du texte markdown ajouté s’affiche. Les articles ont été mis à jour dans la plage de dates allant du **2016-07-26** au **2016-08-21**.

| &nbsp; | Article | Texte mis à jour, extrait de code |
| --: | :-- | :-- |
| 1 | [Gestion de la concurrence et des charges de travail dans SQL Data Warehouse](sql-data-warehouse-develop-concurrency.md) | ** Requêtes qui honorent les limites de concurrence** La plupart des requêtes sont régies par des classes de ressource. Ces requêtes doivent figurer à la fois dans le seuil des requêtes de concurrence et dans le seuil des emplacements de concurrence. Un utilisateur final ne peut pas choisir d'exclure une requête du modèle d'emplacement de concurrence. Pour rappel, les instructions suivantes **honorent** les classes de ressources : / INSERT-SELECT / UPDATE / DELETE / SELECT (lors de l’interrogation de tables utilisateur) / ALTER INDEX REBUILD / ALTER INDEX REORGANIZE / ALTER TABLE REBUILD / CREATE INDEX / CREATE CLUSTERED COLUMNSTORE INDEX / CREATE TABLE AS SELECT (CTAS) / Chargement de données / Opérations de déplacement de données conduites par le service de déplacement de données (DMS) ** Exceptions de requête aux limites de concurrence** |
| 2 | [Détails relatifs à la migration vers Premium Storage](sql-data-warehouse-migrate-to-premium-storage.md) | Avec l’évolution de Premium Storage, nous avons également augmenté le nombre de fichiers blob de base de données dans l’architecture sous-jacente de votre entrepôt de données. Si vous rencontrez des problèmes de performances, nous vous recommandons de reconstruire vos index columnstore en cluster grâce au script ci-dessous. Cette opération forcera le déplacement de certaines de vos données existantes vers les objets blob supplémentaires. Dans le cas contraire, les données seront naturellement redistribuées au fil du temps, quand vous chargerez d’autres données dans les tables de votre entrepôt de données. **Conditions préalables :** 1. Data Warehouse doit s’exécuter avec au moins 1 000 DWU ou plus (voir Mise à l’échelle de la puissance de calcul) 2. L’utilisateur qui exécute le script doit avoir le rôle mediumrc ou un rôle supérieur 1. Pour ajouter un utilisateur à ce rôle, exécutez la commande suivante : 1. ````EXEC sp_addrolemember 'xlargerc', 'MyUser'```` ````sql /------------------------------------------------------------------------------ /- Step 1: Create Table to control Index Rebuild /- Run as user in mediumrc or higher /------------------------------------------------------------ |





## Prise en main

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 3 | [Authentification sur Azure SQL Data Warehouse](sql-data-warehouse-authentication.md) | Authentification Azure Active Directory (AAD) et SQL Server sur Azure SQL Data Warehouse. |
| 4 | [Meilleures pratiques pour Azure SQL Data Warehouse](sql-data-warehouse-best-practices.md) | Recommandations et meilleures pratiques que vous devez connaître pour développer des solutions pour Azure SQL Data Warehouse. Elles vous aideront à réussir. |
| 5 | [Pilotes pour Azure SQL Data Warehouse](sql-data-warehouse-connection-strings.md) | Chaînes de connexion et pilotes pour SQL Data Warehouse |
| 6 | [Connexion à Azure SQL Data Warehouse](sql-data-warehouse-connect-overview.md) | Vue d’ensemble de la connexion à Azure SQL Data Warehouse |
| 7 | [Analyse des données avec Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md) | Utilisez Azure Machine Learning pour générer un modèle Machine Learning prédictif basé sur les données stockées dans Azure SQL Data Warehouse. |
| 8 | [Interroger Azure SQL Data Warehouse (sqlcmd)](sql-data-warehouse-get-started-connect-sqlcmd.md) | Interrogation d’Azure SQL Data Warehouse à l’aide de l’utilitaire de ligne de commande sqlcmd. |
| 9 | [Créer une base de données SQL Data Warehouse à l’aide de Transact-SQL (TSQL).](sql-data-warehouse-get-started-create-database-tsql.md) | Découvrez comment créer un entrepôt Azure SQL Data Warehouse avec TSQL |
| 10 | [Création d’un ticket de support pour SQL Data Warehouse](sql-data-warehouse-get-started-create-support-ticket.md) | Création d'un ticket de support dans Azure SQL Data Warehouse. |
| 11 | [Téléchargement de données avec Azure Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md) | Apprenez à charger des données avec Azure Data Factory |
| 12 | [Télécharger des données avec PolyBase dans SQL Data Warehouse](sql-data-warehouse-get-started-load-with-polybase.md) | Découvrez PolyBase et apprenez comment utiliser cette solution avec les scénarios d’entreposage de données. |
| 13\. | [Créer un serveur logique](sql-data-warehouse-get-started-new-server.md) | Découvrez comment créer une base de données Azure SQL Data Warehouse dans le portail Azure |
| 14 | [Créer un Azure SQL Data Warehouse](sql-data-warehouse-get-started-provision.md) | Découvrez comment créer un entrepôt de données Azure SQL Data Warehouse dans le portail Azure |
| 15 | [Création de SQL Data Warehouse à l’aide de PowerShell](sql-data-warehouse-get-started-provision-powershell.md) | Création de SQL Data Warehouse à l’aide de PowerShell |
| 16 | [Visualiser des données avec Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) | Visualiser les données SQL Data Warehouse avec Power BI |
| 17 | [Interroger Azure SQL Data Warehouse (sqlcmd) (Visual Studio)](sql-data-warehouse-query-visual-studio.md) | Interrogez SQL Data Warehouse avec Visual Studio. |



## Développement

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 18 | [Optimisation des transactions pour SQL Data Warehouse](sql-data-warehouse-develop-best-practices-transactions.md) | Meilleures pratiques sur l’écriture de mises à jour efficaces de transactions dans Azure SQL Data Warehouse |
| 19 | [Gestion de la concurrence et des charges de travail dans SQL Data Warehouse](sql-data-warehouse-develop-concurrency.md) | Décrit la gestion de la concurrence et des charges de travail dans Azure SQL Data Warehouse pour le développement de solutions. |
| 20 | [Instruction Create Table As Select (CTAS) dans SQL Data Warehouse](sql-data-warehouse-develop-ctas.md) | Conseils de codage à l’aide de l’instruction Create Table As Select (CTAS) dans Azure SQL Data Warehouse pour le développement de solutions. |
| 21 | [Code SQL dynamique dans SQL Data Warehouse](sql-data-warehouse-develop-dynamic-sql.md) | Conseils relatifs à l’utilisation de code SQL dynamique dans Azure SQL Data Warehouse pour le développement de solutions. |
| 22 | [Options de regroupement dans SQL Data Warehouse](sql-data-warehouse-develop-group-by-options.md) | Conseils relatifs à l’implémentation d’options de regroupement dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions. |
| 23 | [Utiliser des libellés pour instrumenter des requêtes dans SQL Data Warehouse](sql-data-warehouse-develop-label.md) | Conseils relatifs à l’utilisation de libellés pour instrumenter des requêtes dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions. |
| 24 | [Boucles dans SQL Data Warehouse](sql-data-warehouse-develop-loops.md) | Conseils relatifs à l’utilisation de boucles Transact-SQL et au remplacement de curseurs dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions. |
| 25 | [Procédures stockées dans SQL Data Warehouse](sql-data-warehouse-develop-stored-procedures.md) | Conseils relatifs à l’implémentation de procédures stockées dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions. |
| 26 | [Transactions dans SQL Data Warehouse](sql-data-warehouse-develop-transactions.md) | Conseils relatifs à l’implémentation de transactions dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions. |
| 27 | [Schémas définis par l’utilisateur dans SQL Data Warehouse](sql-data-warehouse-develop-user-defined-schemas.md) | Conseils relatifs à l’utilisation de schémas Transact-SQL dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions. |
| 28 | [Affecter des variables dans SQL Data Warehouse](sql-data-warehouse-develop-variable-assignment.md) | Conseils relatifs à l’affectation de variables Transact-SQL dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions. |
| 29 | [Vues proposées par SQL Data Warehouse](sql-data-warehouse-develop-views.md) | Conseils relatifs à l’utilisation de vues Transact-SQL dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions. |
| 30 | [Choix de conception et techniques de codage pour SQL Data Warehouse](sql-data-warehouse-overview-develop.md) | Concepts de développement, choix de conception, recommandations et des techniques de codage pour SQL Data Warehouse. |



## Gérer

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 31 | [Gestion de la puissance de calcul dans Azure SQL Data Warehouse (Vue d’ensemble)](sql-data-warehouse-manage-compute-overview.md) | Capacités de montée en puissance des performances dans Azure SQL Data Warehouse. Montez en puissance en ajustant le nombre d’unités DWU ou suspendez et reprenez des ressources de calcul pour réduire les coûts. |
| 32 | [Gestion de la puissance de calcul dans Azure SQL Data Warehouse (portail Azure)](sql-data-warehouse-manage-compute-portal.md) | Tâches du portail Azure permettant de gérer la puissance de calcul. Mettez à l’échelle les ressources de calcul en ajustant les unités DWU. Ou suspendez et reprenez des ressources de calcul pour réduire les coûts. |
| 33 | [Gestion de la puissance de calcul dans Azure SQL Data Warehouse (PowerShell)](sql-data-warehouse-manage-compute-powershell.md) | Tâches PowerShell permettant de gérer la puissance de calcul. Mettez à l’échelle les ressources de calcul en ajustant les unités DWU. Ou suspendez et reprenez des ressources de calcul pour réduire les coûts. |
| 34 | [Gestion de la puissance de calcul dans Azure SQL Data Warehouse (REST)](sql-data-warehouse-manage-compute-rest-api.md) | Tâches PowerShell permettant de gérer la puissance de calcul. Mettez à l’échelle les ressources de calcul en ajustant les unités DWU. Ou suspendez et reprenez des ressources de calcul pour réduire les coûts. |
| 35 | [Gestion de la puissance de calcul dans Azure SQL Data Warehouse (T-SQL)](sql-data-warehouse-manage-compute-tsql.md) | Tâches Transact-SQL (T-SQL) permettant une montée en puissance des performances en ajustant les unités DWU. Réalisez des économies en réduisant vos ressources pendant les heures creuses. |
| 36 | [Surveiller votre charge de travail à l'aide de vues de gestion dynamique](sql-data-warehouse-manage-monitor.md) | Comment surveiller votre charge de travail à l'aide de vues de gestion dynamique |
| 37 | [Gestion de bases de données dans Azure SQL Data Warehouse](sql-data-warehouse-overview-manage.md) | Vue d’ensemble de la gestion des bases de données SQL Data Warehouse. Inclut des outils de gestion, des unités DWU et une montée en puissance des performances, une résolution des problèmes de performances des requêtes, la mise en œuvre de stratégies de sécurité adaptées et la restauration d’une base de données en cas d’altération des données ou de panne au niveau régional. |
| 38 | [Surveillance des requêtes d’utilisateur dans Azure SQL Data Warehouse](sql-data-warehouse-overview-manage-user-queries.md) | Vue d’ensemble des considérations, meilleures pratiques et tâches de surveillance des requêtes d’utilisation dans Azure SQL Data Warehouse. |
| 39 | [Restauration d’un Azure SQL Data Warehouse (Vue d’ensemble)](sql-data-warehouse-restore-database-overview.md) | Vue d’ensemble des options de restauration de base de données pour la récupération d’une base de données dans Azure SQL Data Warehouse. |
| 40 | [Restauration d’un Azure SQL Data Warehouse (Portail)](sql-data-warehouse-restore-database-portal.md) | Tâches du portail Azure permettant de restaurer un Azure SQL Data Warehouse. |
| 41 | [Restauration d’un Azure SQL Data Warehouse (PowerShell)](sql-data-warehouse-restore-database-powershell.md) | Tâches PowerShell permettant de restaurer un Azure SQL Data Warehouse. |
| 42 | [Restauration d’un Azure SQL Data Warehouse (API REST)](sql-data-warehouse-restore-database-rest-api.md) | Tâches d’API REST permettant de restaurer un Azure SQL Data Warehouse. |



## Tables et index

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 43 | [Types de données pour tables dans SQL Data Warehouse](sql-data-warehouse-tables-data-types.md) | Prise en main des types de données pour les tables Azure SQL Data Warehouse. |
| 44 | [Distribution de tables dans SQL Data Warehouse](sql-data-warehouse-tables-distribute.md) | Prise en main de la distribution de tables dans Azure SQL Data Warehouse. |
| 45 | [Indexation de tables dans SQL Data Warehouse](sql-data-warehouse-tables-index.md) | Prise en main de l’indexation de tables dans Azure SQL Data Warehouse. |
| 46 | [Vue d’ensemble des tables dans SQL Data Warehouse](sql-data-warehouse-tables-overview.md) | Prise en main des tables Azure SQL Data Warehouse. |
| 47 | [Partitionnement de tables dans SQL Data Warehouse](sql-data-warehouse-tables-partition.md) | Prise en main du partitionnement de tables dans Azure SQL Data Warehouse. |
| 48 | [Gestion des statistiques sur les tables dans SQL Data Warehouse](sql-data-warehouse-tables-statistics.md) | Prise en main des statistiques sur les tables dans Azure SQL Data Warehouse. |
| 49 | [Tables temporaires dans SQL Data Warehouse](sql-data-warehouse-tables-temporary.md) | Prise en main des tables temporaires dans Azure SQL Data Warehouse. |



## Integrate

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 50 | [Utiliser Azure Data Factory avec SQL Data Warehouse](sql-data-warehouse-integrate-azure-data-factory.md) | Conseils sur l’utilisation de Microsoft Azure Data Factory avec Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions. |
| 51 | [Utilisation de Microsoft Azure Machine Learning avec SQL Data Warehouse](sql-data-warehouse-integrate-azure-machine-learning.md) | Didacticiel sur l’utilisation de Microsoft Azure Machine Learning avec Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions. |
| 52 | [Utiliser Azure Stream Analytics avec SQL Data Warehouse](sql-data-warehouse-integrate-azure-stream-analytics.md) | Conseils sur l’utilisation d’Azure Stream Analytics avec Azure SQL Data Warehouse pour le développement de solutions. |
| 53 | [Utiliser Power BI avec SQL Data Warehouse](sql-data-warehouse-integrate-power-bi.md) | Conseils relatifs à l’utilisation de Power BI avec Azure SQL Data Warehouse pour le développement de solutions. |
| 54 | [Tirer parti d’autres services avec SQL Data Warehouse](sql-data-warehouse-overview-integrate.md) | Outils et partenaires proposant des solutions s’intégrant avec SQL Data Warehouse. |



## charger

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 55 | [Charger les données conservées dans un stockage d’objets blob Azure dans Azure SQL Data Warehouse. (Azure Data Factory)](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md) | Apprenez à charger des données avec Azure Data Factory |
| 56 | [Charger les données de stockage d’objets blob Azure dans SQL Data Warehouse (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) | Apprenez à utiliser PolyBase pour charger des données de stockage d’objets blob Azure dans SQL Data Warehouse. Chargez quelques tables à partir de données publiques dans le schéma d’entrepôt de données Contoso Retail. |
| 57 | [Charger des données à partir de SQL Server dans Azure SQL Data Warehouse (AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) | Utilise BCP pour exporter des données à partir de SQL Server vers des fichiers plats, AZCopy pour importer des données dans le stockage d’objets blob Azure et PolyBase pour recevoir les données dans Azure SQL Data Warehouse. |
| 58 | [Charger des données à partir de SQL Server dans Azure SQL Data Warehouse (fichiers plats)](sql-data-warehouse-load-from-sql-server-with-bcp.md) | Pour les données de taille réduite, utilise l’utilitaire de ligne de commande bcp pour exporter les données dans des fichiers plats à partir de SQL Server, puis les importer directement dans Azure SQL Data Warehouse. |
| 59 | [Charger des données à partir de SQL Server dans Azure SQL Data Warehouse (SSIS)](sql-data-warehouse-load-from-sql-server-with-integration-services.md) | Vous montre comment créer un package SQL Server Integration Services (SSIS) pour déplacer des données d’un large éventail de sources de données vers SQL Data Warehouse. |
| 60 | [Télécharger des données avec PolyBase dans SQL Data Warehouse](sql-data-warehouse-load-from-sql-server-with-polybase.md) | Utilise BCP pour exporter des données à partir de SQL Server vers des fichiers plats, AZCopy pour importer des données dans le stockage d’objets blob Azure et PolyBase pour recevoir les données dans Azure SQL Data Warehouse. |
| 61 | [Guide d'utilisation de PolyBase dans SQL Data Warehouse](sql-data-warehouse-load-polybase-guide.md) | Instructions et recommandations relatives à l'utilisation de PolyBase dans les scénarios SQL Data Warehouse. |
| 62 | [Charger des exemples de données dans SQL Data Warehouse](sql-data-warehouse-load-sample-databases.md) | Charger des exemples de données dans SQL Data Warehouse |
| 63 | [Charger des données avec bcp](sql-data-warehouse-load-with-bcp.md) | Découvrez bcp et apprenez comment utiliser cette solution avec les scénarios d’entreposage de données. |
| 64 | [Chargement de données dans Azure SQL Data Warehouse](sql-data-warehouse-overview-load.md) | Découvrez les scénarios courants de chargement des données dans SQL Data Warehouse. Ceux-ci incluent l’utilisation de PolyBase, d’Azure Blob Storage et de fichiers plats ainsi que l’envoi de disques. Vous pouvez également utiliser des outils tiers. |



## Migrer

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 65 | [Migration de votre code SQL vers SQL Data Warehouse](sql-data-warehouse-migrate-code.md) | Conseils relatifs à la migration de votre code SQL vers Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions. |
| 66 | [Migration de vos données](sql-data-warehouse-migrate-data.md) | Conseils relatifs à la migration de vos données vers Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions. |
| 67 | [Utilitaire de migration de l’entrepôt de données (version préliminaire)](sql-data-warehouse-migrate-migration-utility.md) | Migrer vers SQL Data Warehouse. |
| 68 | [Migration de votre schéma vers SQL Data Warehouse](sql-data-warehouse-migrate-schema.md) | Conseils relatifs à la migration de votre schéma vers Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions. |
| 69 | [Migration de votre solution vers SQL Data Warehouse](sql-data-warehouse-overview-migrate.md) | Recommandations relatives à la migration de votre solution vers la plateforme Microsoft Azure SQL Data Warehouse |



## Partenaires

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 70 | [Partenaires décisionnels de SQL Data Warehouse](sql-data-warehouse-partner-business-intelligence.md) | Répertorie les partenaires tiers en matière d’aide à la décision proposant des solutions qui prennent en charge SQL Data Warehouse. |
| 71 | [Partenaires de SQL Data Warehouse en matière d’intégration de données](sql-data-warehouse-partner-data-integration.md) | Répertorie les partenaires tiers proposant des solutions d’intégration de données prenant en charge SQL Data Warehouse. |
| 72 | [Partenaires de SQL Data Warehouse en matière de gestion de données](sql-data-warehouse-partner-data-management.md) | Répertorie les partenaires de gestion des données tiers proposant des solutions qui prennent en charge SQL Data Warehouse. |



## Référence

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 73 | [Rubriques de référence pour SQL Data Warehouse](sql-data-warehouse-overview-reference.md) | Liens vers du contenu de référence pour SQL Data Warehouse. |
| 74 | [Applets de commande PowerShell et API REST pour SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md) | Recherchez les principaux applets de commande PowerShell pour Azure SQL Data Warehouse, y compris comment suspendre et reprendre une base de données. |
| 75 | [Éléments de langage](sql-data-warehouse-reference-tsql-language-elements.md) | Liste de liens vers du contenu de référence pour les éléments de langage Transact-SQL utilisés pour SQL Data Warehouse. |
| 76 | [Rubriques Transact-SQL](sql-data-warehouse-reference-tsql-statements.md) | Liens vers du contenu de référence pour les rubriques Transact-SQL utilisées par SQL Data Warehouse. |
| 77 | [Vues système](sql-data-warehouse-reference-tsql-system-views.md) | Des liens vers le contenu des vues système pour SQL Data Warehouse |



## Sécurité

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 78 | [SQL Data Warehouse : prise en charge des clients de niveau inférieur pour l’audit et le masquage de données dynamiques](sql-data-warehouse-auditing-downlevel-clients.md) | En savoir plus sur la prise en charge des clients de niveau inférieur de SQL Data Warehouse pour l’audit des données |
| 79 | [Audit dans Azure SQL Data Warehouse](sql-data-warehouse-auditing-overview.md) | Prise en main de l’audit dans Azure SQL Data Warehouse |
| 80 | [Mise en route avec le chiffrement transparent des données (TDE) dans SQL Data Warehouse](sql-data-warehouse-encryption-tde.md) | Mise en route avec le chiffrement transparent des données (TDE) dans SQL Data Warehouse |
| 81 | [Prise en main du chiffrement transparent des données (TDE)](sql-data-warehouse-encryption-tde-tsql.md) | Mise en route avec TSQL et le chiffrement transparent des données (TDE) pour SQL Data Warehouse |
| 82 | [Sécuriser une base de données dans SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md) | Conseils relatifs à la sécurisation d’une base de données dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions. |



## Divers

| &nbsp; | Intitulé | Description |
| --: | :-- | :-- |
| 83 | [Installer Visual Studio 2015 et SSDT pour SQL Data Warehouse](sql-data-warehouse-install-visual-studio.md) | Installer les outils de développement Visual Studio et SSDT pour Azure SQL Data Warehouse |
| 84 | [Détails relatifs à la migration vers Premium Storage](sql-data-warehouse-migrate-to-premium-storage.md) | Instructions de migration d’un entrepôt SQL Data Warehouse existant vers le stockage Premium |
| 85 | [Prise en main de la détection de menaces](sql-data-warehouse-security-threat-detection.md) | Prise en main de la détection de menaces |
| 86 | [Limites de la capacité de SQL Data Warehouse](sql-data-warehouse-service-capacity-limits.md) | Valeurs maximales pour les connexions, les bases de données, les tables et les requêtes pour SQL Data Warehouse. |
| 87 | [Résolution des problèmes d’Azure SQL Data Warehouse](sql-data-warehouse-troubleshoot.md) | Résolution des problèmes d’Azure SQL Data Warehouse. |

<!---HONumber=AcomDC_0824_2016-->