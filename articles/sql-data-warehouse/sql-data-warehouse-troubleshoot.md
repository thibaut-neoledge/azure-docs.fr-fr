<properties
   pageTitle="Résolution des problèmes d’Azure SQL Data Warehouse | Microsoft Azure"
   description="Résolution des problèmes d’Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="sonyama;barbkess"/>

# Résolution des problèmes d’Azure SQL Data Warehouse

Cette rubrique répertorie les questions les plus courantes relatives à la résolution des problèmes que nous posent nos clients.

## Connexion

| Problème | Résolution : |
| :----------------------------------| :---------------------------------------------- |
| Échec de connexion pour l’utilisateur 'NT AUTHORITY\\ANONYMOUS LOGON'. (Microsoft SQL Server, erreur : 18456) | Cette erreur se produit lorsqu’un utilisateur AAD tente de se connecter à la base de données master alors qu’elle ne contient pas d’utilisateur. Pour corriger ce problème, spécifiez le SQL Data Warehouse auquel vous souhaitez vous connecter ou ajoutez l’utilisateur à la base de données master. Consultez l’article [Présentation de la sécurité][] pour plus de détails.|
|Le serveur principal « MyUserName » n’est pas en mesure d’accéder à la base de données « master » dans le contexte de sécurité actuel. La base de données utilisateur par défaut ne peut pas être ouverte. La connexion a échoué. Échec de la connexion pour l'utilisateur 'MyUserName'. (Microsoft SQL Server, erreur : 916) | Cette erreur se produit lorsqu’un utilisateur AAD tente de se connecter à la base de données master alors qu’elle ne contient pas d’utilisateur. Pour corriger ce problème, spécifiez le SQL Data Warehouse auquel vous souhaitez vous connecter ou ajoutez l’utilisateur à la base de données master. Consultez l’article [Présentation de la sécurité][] pour plus de détails.|
| Erreur CTAIP | Cette erreur peut se produire lorsqu’un identifiant de connexion a été créé sur la base de données principale du serveur SQL, mais pas sur la base de données SQL Data Warehouse. Si vous rencontrez cette erreur, consultez l’article [Vue d’ensemble de la sécurité][]. Cet article explique comment créer un identifiant et un utilisateur sur la base de données master, puis comment créer un utilisateur dans la base de données SQL Data Warehouse.|
| Bloqué par le pare-feu |Les bases de données SQL Azure sont protégées par des pare-feu au niveau du serveur et de la base de données pour s’assurer que seules les adresses IP connues ont accès à une base de données. Les pare-feu sont sécurisés par défaut, ce qui signifie que vous devez activer explicitement une adresse IP ou une plage d’adresses avant de vous connecter. Pour configurer votre pare-feu pour l’accès, suivez les étapes décrites dans la section de [configuration de l’accès au pare-feu du serveur pour l’adresse IP de votre client][] des [instructions d’approvisionnement][].|
| Connexion impossible avec l’outil ou le pilote | SQL Data Warehouse recommande l’utilisation de [SSMS][], [SSDT for Visual Studio 2015][] or [sqlcmd][] pour interroger vos données. Pour plus d’informations sur les pilotes et la connexion à SQL Data Warehouse, consultez les articles [Pilotes pour Azure SQL Data Warehouse][] et [Connexion à Azure SQL Data Warehouse][].|


## Outils

| Problème | Résolution : |
| :----------------------------------| :---------------------------------------------- |
| Des utilisateurs Azure Active Directory sont manquants dans l’explorateur d’objets Visual Studio | Il s'agit d'un problème connu. Pour résoudre ce problème, affichez les utilisateurs dans [sys.database\_principals][]. Consultez la page [Authentification sur Azure SQL Data Warehouse][] pour en savoir plus sur l’utilisation d’Azure Active Directory avec SQL Data Warehouse.|

## Performances

| Problème | Résolution : |
| :----------------------------------| :---------------------------------------------- |
| Résolution des problèmes de performances des requêtes | Si vous tentez de dépanner une requête spécifique, commencez avec l’article relatif à la [surveillance de vos requêtes][].|
| Des performances des requêtes et des plans médiocres sont souvent le résultat de statistiques manquantes | La cause la plus courante de la médiocrité des performances est le manque de statistiques concernant vos tables. Consultez la page [Maintaining Table Statistics][Statistics] (Maintenance des statistiques de table) pour plus d’informations sur la façon de créer des statistiques et les raisons pour lesquelles celles-ci sont essentielles à vos performances.|
| Concurrence faible / requêtes en file d’attente | Comprendre la [gestion des charges de travail][] est important pour comprendre comment équilibrer l’allocation de mémoire avec l’accès concurrentiel.|
| Comment mettre en œuvre les meilleures pratiques | L’article [Meilleures pratiques relatives à SQL Data Warehouse][] est le point de départ idéal pour apprendre à améliorer les performances des requêtes.|
| Comment améliorer les performances avec la mise à l’échelle | Pour améliorer les performances, il suffit parfois simplement d’ajouter davantage de puissance de calcul à vos requêtes en procédant à une [mise à l’échelle de votre base de données SQL Data Warehouse][].|
| Performances de requêtes médiocres en raison de la qualité médiocre de l’index | Parfois, les requêtes peuvent ralentir en raison de la [qualité médiocre des index columnstore][]. Consultez cet article pour plus d’informations et pour découvrir comment [Reconstruire des index pour améliorer la qualité de segment][].|

## Gestion de systèmes

| Problème | Résolution : |
| :----------------------------------| :---------------------------------------------- |
| Msg 40847 : Impossible d’effectuer l’opération car le serveur risque de dépasser le quota d’unité de transaction de base de données autorisé de 45 000. | Réduisez la [DWU][] de la base de données que vous essayez de créer ou [demandez une augmentation du quota][].|
| Examen de l’utilisation de l’espace | Consultez la section relative aux [tailles des tables][] pour comprendre l’utilisation de l’espace de votre système.|
| Aide concernant la gestion des tables | Consultez l’article [Table overview][Overview] (Vue d’ensemble des tables) pour obtenir de l’aide pour la gestion de vos tables. Cet article inclut également des liens vers des rubriques plus détaillées, notamment [Types de données de table][Data types], [Distribution d’une table][Distribute], [Indexation d’une table][Index], [Partitionnement d’une table][Partition], [Maintenance des statistiques de table][Statistics] et [Tables temporaires][Temporary].|

## Polybase

| Problème | Résolution : |
| :----------------------------------| :---------------------------------------------- |
| Erreur UTF-8 | Actuellement, PolyBase prend uniquement en charge le chargement des fichiers de données encodés en UTF-8. Consultez [Contournement de la nécessité du codage UTF-8 de PolyBase][] pour obtenir des conseils sur la façon de contourner cette limitation.|
| Échec du chargement en raison des grandes lignes | Actuellement, la prise en charge des grandes lignes n’est pas disponible pour Polybase. Cela signifie que si votre table contient VARCHAR(MAX), NVARCHAR(MAX) ou VARBINARY(MAX), les tables externes ne peuvent pas être utilisés pour charger vos données. Actuellement, les charges pour les grandes lignes sont uniquement prises en charge via Azure Data Factory (avec BCP), Azure Stream Analytics, SSIS, BCP ou la classe .NET SQLBulkCopy. La prise en charge de PolyBase pour les grandes lignes sera ajoutée dans une version ultérieure.|
| Échec de chargement BCP d’une table avec le type de données MAX | Il existe un problème connu qui nécessite que VARCHAR(MAX), NVARCHAR(MAX) ou VARBINARY(MAX) soient placés à la fin de la table dans certains scénarios. Essayez de déplacer vos colonnes MAX à la fin de la table.|

## Différences par rapport à la base de données SQL

| Problème | Résolution : |
| :----------------------------------| :---------------------------------------------- |
| Fonctionnalités de base de données SQL non prises en charge | Voir [Fonctionnalités de tables non prises en charge][].|
| Types de données de base de données SQL non pris en charge | Voir [Types de données non pris en charge][].|
| Limitations DELETE et UPDATE | Consultez les rubriques [Solutions de contournement UPDATE][], [Solutions de contournement DELETE][] et [Utilisation de CTAS pour contourner les syntaxes UPDATE et DELETE non prises en charge][]. |
| Instruction MERGE non prise en charge | Consultez la rubrique [Solutions de contournement MERGE][].|
| Limitations des procédures stockées | Consultez [Limitations des procédures stockées][] pour comprendre certaines limitations des procédures stockées.|
| Les fonctions définies par l’utilisateur ne prennent pas en charge les instructions SELECT | Il s’agit d’une limitation actuelle de nos fonctions définies par l’utilisateur. Consultez [CREATE FUNCTION][] pour connaître la syntaxe que nous prenons en charge. |

## Étapes suivantes

Si les ressources ci-dessus ne vous ont pas permis de trouver une solution à votre problème, voici d’autres ressources que vous pouvez consulter.

- [Blogs]
- [Demandes de fonctionnalités]
- [Vidéos]
- [Blogs de l’équipe CAT (Customer Advisory Team)]
- [Création d’un ticket de support]
- [Forum MSDN]
- [Forum Stack Overflow]
- [Twitter]

<!--Image references-->

<!--Article references-->
[Présentation de la sécurité]: ./sql-data-warehouse-overview-manage-security.md
[Vue d’ensemble de la sécurité]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT for Visual Studio 2015]: ./sql-data-warehouse-install-visual-studio.md
[Pilotes pour Azure SQL Data Warehouse]: ./sql-data-warehouse-connection-strings.md
[Connexion à Azure SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Création d’un ticket de support]: ./sql-data-warehouse-get-started-create-support-ticket.md
[mise à l’échelle de votre base de données SQL Data Warehouse]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[demandez une augmentation du quota]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[surveillance de vos requêtes]: ./sql-data-warehouse-manage-monitor.md
[instructions d’approvisionnement]: ./sql-data-warehouse-get-started-provision.md
[configuration de l’accès au pare-feu du serveur pour l’adresse IP de votre client]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[Meilleures pratiques relatives à SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[tailles des tables]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[Fonctionnalités de tables non prises en charge]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Types de données non pris en charge]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[qualité médiocre des index columnstore]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Reconstruire des index pour améliorer la qualité de segment]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[gestion des charges de travail]: ./sql-data-warehouse-develop-concurrency.md
[Utilisation de CTAS pour contourner les syntaxes UPDATE et DELETE non prises en charge]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[Solutions de contournement UPDATE]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[Solutions de contournement DELETE]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[Solutions de contournement MERGE]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Limitations des procédures stockées]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentification sur Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md
[Contournement de la nécessité du codage UTF-8 de PolyBase]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[sys.database\_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CREATE FUNCTION]: https://msdn.microsoft.com/library/mt203952.aspx
[sqlcmd]: https://azure.microsoft.com/fr-FR/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs de l’équipe CAT (Customer Advisory Team)]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Demandes de fonctionnalités]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Forum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vidéos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

<!---HONumber=AcomDC_0907_2016-->