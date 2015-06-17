<properties 
	pageTitle="Nouveautés de la base de données SQL V12" 
	description="Décrit les dernières fonctionnalités qui ont été ajoutées à la base de données SQL Microsoft Azure version 12, mais uniquement jusqu’au mois de mai 2015." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="genemi"/>


# Nouveautés de la base de données SQL V12


La base de données SQL Microsoft Azure version 12 ([pour laquelle il existe une version préliminaire dans certaines régions](sql-database-v12-whats-new.md#V12AzureSqlDbPreviewGaTable)) offre désormais une compatibilité quasi complète avec le moteur Microsoft SQL Server. Ces améliorations, apportées récemment, permettent de rationaliser la migration des applications SQL Server vers une base de données SQL. Grâce à elles, votre système peut traiter de manière plus robuste des charges de travail de base de données plus volumineuses.


[Inscrivez-vous](https://portal.azure.com) pour obtenir la base de données SQL, afin de [vous familiariser](sql-database-get-started.md) avec cette nouvelle génération sur Microsoft Azure. Tout d’abord, vous avez besoin d’un abonnement à Microsoft Azure. Vous pouvez vous inscrire pour accéder à une [version d’essai gratuit de Microsoft Azure](http://azure.microsoft.com/pricing/free-trial) et obtenir des informations sur la [tarification](http://azure.microsoft.com/pricing/details/sql-database).


Le processus de planification et de mise à niveau de vos bases de données vers la version 12 commence par la [planification et la préparation de la mise à niveau vers la version 12 de la base de données SQL](sql-database-v12-plan-prepare-upgrade.md).


> [AZURE.TIP]Pour en savoir plus sur les nouvelles fonctionnalités de la base de données SQL Microsoft Azure, consultez en priorité la page web « Mises à jour de service », à l’adresse [http://azure.microsoft.com/fr-fr/updates/](http://azure.microsoft.com/updates/). Sur cette page web, accédez à la liste déroulante **Services** et cliquez sur **Base de données SQL**.


## Points clés


- Le **portail Azure en version préliminaire** est [disponible](http://portal.azure.com/) et vous permet de créer des bases de données SQL et des serveurs présentant la version 12 (ou une version antérieure, le cas échéant). Dans le portail Azure en version préliminaire, vous spécifiez votre base de données SQL, puis indiquez le serveur qui doit l’héberger.
 - Depuis avril 2015, l’[ancien portail Microsoft Azure](http://manage.windowsazure.com/) est pris en charge en parallèle.


- Si vous utilisez le portail Azure en version préliminaire, **sélectionnez la version** du serveur de base de données SQL. La valeur par défaut est la version 12, mais vous pouvez choisir une version antérieure du serveur de base de données SQL.


- **Sécurité :** profitez de la nouvelle fonctionnalité de création d’[utilisateurs dans les bases de données](sql-database-v12-whats-new.md#UsersInContainedDatabases). Deux autres fonctionnalités sont proposées : la [sécurité au niveau des lignes](sql-database-v12-whats-new.md#RowLevelSecurity) et le [masquage des données dynamiques](sql-database-v12-whats-new.md#DynamicDataMasking), même si elles sont uniquement disponibles en versions préliminaires, et non en disponibilité générale.


- La **facilité de gestion** des bases de données volumineuses permet la prise en charge de charges de travail plus volumineuses, grâce à des requêtes parallèles (version Premium uniquement), ainsi que le [partitionnement des tables](http://msdn.microsoft.com/library/ms187802.aspx), [l’indexation en ligne](http://msdn.microsoft.com/library/ms188388.aspx), la recréation simplifiée d’index volumineux (grâce à la suppression de la limite de taille, qui s’élevait à 2 Go) et d’autres options de la commande [ALTER DATABASE](http://msdn.microsoft.com/library/bb522682.aspx).


- La **prise en charge des fonctions de programmabilité clés** permet de renforcer la conception des applications, par l’intermédiaire de l’[intégration du CLR](http://msdn.microsoft.com/library/ms189524.aspx), des [fonctions de fenêtre Transact-SQL](http://msdn.microsoft.com/library/ms189461.aspx), des [index XML](http://msdn.microsoft.com/library/bb934097.aspx) et du [suivi des modifications](http://msdn.microsoft.com/library/bb933875.aspx) portant sur les données.


- Bénéficiez de **performances extraordinaires**, grâce à la prise en charge des requêtes d’[index columnstore](http://msdn.microsoft.com/library/gg492153.aspx) en mémoire (niveau Premium uniquement) pour les charges de travail analytiques plus petites et les mini-Data Warehouse.


- La **surveillance et la résolution des problèmes** ont été améliorées, grâce à une visibilité sur plus de 100 nouvelles vues de table dans un ensemble étendu de vues de gestion dynamique ([DMV](http://msdn.microsoft.com/library/ms188754.aspx)).


- **Nouveau niveau de performances S3 dans le niveau Standard :** offre davantage de souplesse, en termes de [tarification](sql-database-upgrade-new-service-tiers.md), entre les niveaux Standard et Premium. Le niveau S3 fournira davantage d’unités de débit de base de données (UDBD) et toutes les fonctionnalités disponibles du niveau Standard.


## Améliorations de la version 12 : gestion étendue des bases de données


| Fonctionnalité | Description |
| :--- | :--- |
| . | ***1er mai 2015 :*** |
| Powershell pour la mise à niveau vers la version 12 à partir d’une version antérieure | De nouvelles applets de commande Powershell sont disponibles pour le démarrage, l’annulation ou le suivi d’une mise à niveau vers la version 12 de la base de données SQL Microsoft Azure (à partir de la version 11 ou d’une autre version antérieure).<br/><br/>Pour en savoir plus sur ces applets de commande, voir :<br/>* [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)<br/>* [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)<br/>* [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx) |
| . | ***Avril 2015 :*** |
| Chiffrement transparent des données | La fonction de chiffrement transparent des données (disponible en version préliminaire) protège le système contre toute activité malveillante, en effectuant un chiffrement et un déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions au repos. Pour utiliser cette fonction, vous n’avez pas besoin d’apporter des modifications à votre application.<br/><br/>Pour en savoir plus, voir :<br/>- [Chiffrement transparent des données avec la base de données SQL Azure](http://msdn.microsoft.com/library/dn948096.aspx) : instructions pas à pas.<br/> - [Chiffrement transparent des données (TDE)](http://msdn.microsoft.com/library/bb934049.aspx) : description générale. |
| Certificats, clés et fonctions de chiffrement de Transact SQL | Vous pouvez désormais créer des certificats, des clés symétriques et des clés asymétriques dans la version 12 de la base de données SQL Microsoft Azure. La plupart des fonctions de chiffrement sont à présent prises en charge.<br/><br/>Pour en savoir plus, voir :<br/> - [CREATE CERTIFICATE (Transact-SQL)](http://msdn.microsoft.com/library/ms187798.aspx)<br/> - [CREATE SYMMETRIC KEY (Transact-SQL)](http://msdn.microsoft.com/library/ms188357.aspx)<br/> - [CREATE ASYMMETRIC KEY (Transact-SQL)](http://msdn.microsoft.com/library/ms174430.aspx)<br/> - [Fonctions de chiffrement (Transact-SQL)](http://msdn.microsoft.com/library/ms173744.aspx) |
| Pool élastique de bases de données SQL | Lorsque vous devez gérer l’évolution de milliers de bases de données, la moindre tâche, même la plus simple, peut devenir épineuse. C’est l’inconvénient d’une croissance rapide de l’activité. Les développeurs SaaS passent de longues heures à écrire une logique complexe pour gérer les modifications apportées aux schémas et d’autres opérations administratives, au fur et à mesure que l’entreprise évolue.<br/><br/>Grâce aux [bases de données élastiques](sql-database-elastic-pool.md), la situation est plus simple à gérer. Prenez un script, envoyez-le sous la forme d’une tâche et laissez la base de données SQL Microsoft Azure se charger du reste ! |
| Recommandations relatives aux niveaux de tarification et aux STA | À présent, lorsque vous faites passer votre base de données d’une édition Web ou Entreprise vers l’un des nouveaux niveaux de service Basic, Standard ou de base, vous pouvez consulter des recommandations sur les niveaux de tarification, directement affichées dans le portail Azure en version préliminaire.<br/><br/>Le service de base de données SQL Microsoft Azure analyse les exigences des bases de données existantes en termes de performances et de fonctionnalités. La fonction [Service Tier Advisor (STA)](sql-database-service-tier-advisor.md) lit les données de performances afin de recommander le niveau de service optimal pour votre base de données. |
| Autorisations des DMV | Auparavant, l’exécution de certaines vues de gestion dynamique (DMV) nécessitait l’autorisation VIEW SERVER STATE. À présent, dans la base de données SQL Microsoft Azure version 12, les DMV peuvent dans de nombreux cas être exécutées par le compte d’administrateur de la base de données SQL, dans les bases de données qui présentent le niveau de service de base ou Standard.<br/><br/>Au niveau Premium, l’accès aux DMV peut être accordé à des utilisateurs supplémentaires, via l’octroi de l’autorisation VIEW DATABASE STATE dans la base de données.<br/><br/>Pour en savoir plus, consultez la section relative aux autorisations d’une [DMV](http://msdn.microsoft.com/library/ms188754.aspx) spécifique. |
| DBCC SQLPERF | La commande [DBCC SQLPERF](http://msdn.microsoft.com/library/ms189768.aspx) est désormais disponible dans la base de données SQL Microsoft Azure version 12. Par contre, les options *reset wait* et *latch statistics* ne sont pas prises en charge par cette base de données. |
| . | ***Décembre 2014 :*** |
| <a name="UsersInContainedDatabases" id="UsersInContainedDatabases"></a>Utilisateurs créés dans les bases de données | Vous pouvez désormais créer des utilisateurs dans vos bases de données sans disposer d’une connexion correspondante dans la base de données principale. On parle alors d’*utilisateurs contenus*. Cela simplifie grandement le déplacement de votre base de données vers un autre serveur. Le code de connexion de vos applications clientes est le même, que vous ayez recours à des utilisateurs contenus dans une base de données ou non.<br/><br/>Cette fonction est un excellent moyen de tirer parti de contrats de niveau de service garantis supérieurs.<br/><br/>D’une manière générale, nous vous invitons à utiliser cette fonction. Toutefois, vous pouvez être confronté à des scénarios spécifiques, qui font de la paire *connexion + user* le meilleur choix pour votre infrastructure, à ce stade.<br/><br/>Pour en savoir plus, voir :<br/> - [Instructions et limitations de la Base de données SQL Azure](http://msdn.microsoft.com/library/azure/ff394108.aspx)<br/> - [Gestion des bases de données et des connexions dans la base de données SQL Microsoft Azure](http://msdn.microsoft.com/library/azure/ee336235.aspx)<br/> - [Bases de données à relation contenant-contenu](http://msdn.microsoft.com/library/azure/ff929071.aspx) |
| Partitionnement de table | Les limites précédemment définies pour le [partitionnement de table](http://msdn.microsoft.com/library/ms190787.aspx) ont été supprimées. |
| Transactions plus volumineuses | Avec la V12, vous n’êtes plus limité à un maximum de 2 Go de modifications de données dans une transaction unique. <br/><br/> Cela présente l’avantage suivant : la recréation d’un index volumineux n’est plus limitée par une taille de transaction maximale de 2 Go. Pour en savoir plus, voir [Limites des ressources de la base de données SQL Azure](http://msdn.microsoft.com/library/azure/dn338081.aspx). |
| Création et recréation d’index en ligne | Avant la version 12, la base de données SQL Microsoft Azure prenait généralement en charge la clause (ONLINE=ON) de l’instruction [ALTER INDEX](http://msdn.microsoft.com/library/ms188388.aspx), mais ce n’était pas le cas pour les index figurant sur une colonne de type BLOB. La version 12 prend désormais en charge la clause (ONLINE=ON), même dans le cas d’index figurant sur des colonnes BLOB.<br/><br/> La fonctionnalité ONLINE permet aux requêtes d’utiliser un index même s’il est en cours de recréation. |
| Prise en charge de CHECKPOINT | Grâce à la version 12, vous pouvez utiliser l’instruction T-SQL [CHECKPOINT](http://msdn.microsoft.com/library/ms188748.aspx) pour votre base de données. |
| Amélioration de l’instruction ALTER TABLE | Permet d’effectuer de nombreuses actions de modification de colonne tandis que la table reste disponible. Pour en savoir plus, voir [ALTER TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms190273.aspx). |
| Amélioration de l’instruction TRUNCATE TABLE | Autorise la troncation de partitions spécifiques. Pour en savoir plus, voir [TRUNCATE TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms177570.aspx). |
| Plus d’options pour ALTER DATABASE | La version 12 prend en charge davantage des options disponibles sur la commande [ALTER DATABASE](http://msdn.microsoft.com/library/ms174269.aspx). <br/><br/> Pour en savoir plus, voir [Guide de référence Transact-SQL dans la base de données SQL Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Plus de commandes DBCC | Plusieurs autres commandes [DBCC](http://msdn.microsoft.com/library/ms188796.aspx) sont désormais disponibles dans la version 12. Pour en savoir plus, voir [Guide de référence Transact-SQL dans la base de données SQL Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |


## Programmation et prise en charge des applications


| Fonctionnalité | Description |
| :--- | :--- |
| . | ***1er mai 2015 :*** |
| Augmentation des tailles d’index | Dans la version 12, jusqu’à 32 colonnes peuvent être combinées en une seule clé d’index composite. La taille maximale autorisée pour les valeurs d’index est de 900 octets pour un index cluster et de 1 700 octets pour un index non cluster.<br/><br/>Pour la version 11 et les versions antérieures à la version 12, les limites se montent à 16 colonnes et une taille de 900 octets |
| . | ***Avril 2015 :*** |
| Version préliminaire de la recherche en texte intégral | La fonction de [recherche en texte intégral (FTS)](http://msdn.microsoft.com/library/ms142571.aspx) vous permet d’interroger des colonnes de caractères en utilisant des mécanismes plus puissants que l’opérateur LIKE. Par exemple :<br/><br/> - FREETEXT : recherche des expressions dont le *sens* correspond à celui de votre expression de recherche, même lorsque le libellé exact ne correspond pas.<br/> - CONTAINS : recherche les valeurs de cellules qui contiennent vos deux termes de recherche, situés *assez près* l’un de l’autre, physiquement.<br/><br/>**REMARQUE : ** cette fonctionnalité est à l’état de version préliminaire. Sa disponibilité générale, à des fins de production, n’a pas encore été annoncée. La portée de la version préliminaire de la fonction FTS est un sous-ensemble de la portée de la fonction FTS que propose Microsoft SQL Server. |
| . | ***Février 2015 :*** |
| <a name="DynamicDataMasking" id="DynamicDataMasking"></a> Version préliminaire du masquage des données dynamiques | Lorsqu’un ensemble de lignes est généré à partir d’une requête, une stratégie de masquage des données établie peut remplacer une partie des données par des caractères « X » afin de protéger les informations sensibles. Une fois l’opération de masquage terminée, l’ensemble de lignes modifié est envoyé au client.<br/><br/>Exemple d’utilisation : vous pouvez appliquer un masquage à l’ensemble des chiffres d’un numéro de carte de crédit, à l’exception des derniers chiffres.<br/><br/>**REMARQUE :** cette fonctionnalité est à l’état de version préliminaire. Sa disponibilité générale, à des fins de production, n’a pas encore été annoncée.<br/><br/>Pour obtenir des informations détaillées, voir [Prise en main du masquage de données dynamiques de base de données SQL](sql-database-dynamic-data-masking-get-started.md) |
| . | ***Janvier 2015 :*** |
| <a name="RowLevelSecurity" id="RowLevelSecurity"></a> Version préliminaire de la fonction de sécurité au niveau des lignes (RLS) | La nouvelle commande [CREATE SECURITY POLICY](http://msdn.microsoft.com/library/dn765135.aspx) de T-SQL vous permet d’implémenter la fonction RLS. Suite à l’utilisation de la fonction RLS, le serveur de bases de données doit ajouter des conditions permettant de filtrer certaines lignes de données avant le renvoi d’un ensemble de lignes au client.<br/><br/>On parle également de « contrôle d’accès affiné » pour qualifier la fonction RLS.<br/><br/>**REMARQUE :** cette fonctionnalité est à l’état de version préliminaire. Sa disponibilité générale, à des fins de production, n’a pas encore été annoncée.<br/><br/>Pour consulter un exemple de code ou en savoir plus, voir [Version préliminaire de la fonction de sécurité au niveau des lignes (RLS)](http://msdn.microsoft.com/library/7221fa4e-ca4a-4d5c-9f93-1b8a4af7b9e8.aspx) |
| . | ***Décembre 2014 :*** |
| Fonctions de fenêtre dans les requêtes Transact-SQL | Les fonctions de fenêtre ANSI sont accessibles via la [clause OVER](http://msdn.microsoft.com/library/ms189461.aspx). <br/><br/> Itzik Ben-Gan a rédigé un excellent [billet de blog](http://sqlmag.com/sql-server-2012/how-use-microsoft-sql-server-2012s-window-functions-part-1), qui explique les fonctions de fenêtre et la clause OVER. |
| Intégration du CLR .NET | Le CLR (common language runtime) du .NET Framework est intégré à la V12. <br/><br/> Seuls les assemblies SAFE entièrement compilés en code binaire sont pris en charge. Pour en savoir plus, voir [Restrictions du modèle de programmation de l’intégration du CLR](http://msdn.microsoft.com/library/ms403273.aspx). <br/><br/> Vous trouverez des informations sur cette fonctionnalité dans les rubriques suivantes : <br/> * [Présentation de l’intégration de CLR dans SQL Server](http://msdn.microsoft.com/library/ms254498.aspx) <br/> * [CREATE ASSEMBLY (Transact-SQL)](http://msdn.microsoft.com/library/ms189524.aspx). |
| Suivi des modifications pour les données | Le suivi des modifications pour les données peut maintenant être configuré au niveau de la base de données ou de la table. <br/><br/> Pour en savoir plus sur le suivi des modifications, voir [À propos du suivi des modifications (SQL Server)](http://msdn.microsoft.com/library/bb933875.aspx). |
| Index XML | La version 12 vous permet d’utiliser les commandes Transact-SQL [CREATE XML INDEX](http://msdn.microsoft.com/library/bb934097.aspx) et [CREATE SELECTIVE XML INDEX](http://msdn.microsoft.com/library/jj670104.aspx). |
| Table en tant que segment | La V12 vous permet de créer une table qui ne comporte aucun index en cluster. <br/><br/> Cette fonctionnalité est particulièrement utile pour la prise en charge de la commande Transact-SQL [SELECT...INTO](http://msdn.microsoft.com/library/ms188029.aspx), qui crée une table à partir d’un résultat de requête. |
| Rôles d’application | Pour le contrôle de la sécurité et des autorisations, la version 12 vous permet d’utiliser les commandes [GRANT](http://msdn.microsoft.com/library/ms187965.aspx) - [DENY](http://msdn.microsoft.com/library/ms188338.aspx) - [REVOKE](http://msdn.microsoft.com/library/ms187728.aspx) sur les [rôles d’application](http://msdn.microsoft.com/library/ms190998.aspx). |


## Meilleures informations sur les clients


| Fonctionnalité | Description |
| :--- | :--- |
| . | ***Décembre 2014 :*** |
| DMV (vues de gestion dynamique) | Plusieurs vues de gestion dynamique sont ajoutées dans la V12. Pour en savoir plus, voir [Guide de référence Transact-SQL dans la base de données SQL Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Suivi des modifications | La V12 prend entièrement en charge le suivi des modifications. <br/><br/> Pour en savoir plus sur cette fonction, voir [Activer et désactiver le suivi des modifications (SQL Server)](http://msdn.microsoft.com/library/bb964713.aspx). |
| Index Columnstore | Un index columnstore améliore les performances système des entrepôts de données lorsqu’une colonne indexée contient des répétitions de la même valeur. L’[index columnstore compresse](http://msdn.microsoft.com/library/gg492088.aspx) les valeurs dupliquées, afin de réduire le nombre de lignes physiques auxquelles le système doit accéder pendant les requêtes. |


## Améliorations des performances au niveau du service Premium


Ces améliorations de performances s’appliquent aux niveaux P2 et P3 du niveau de service Premium.


| Fonctionnalité | Description |
| :--- | :--- |
| . | ***Décembre 2014 :*** |
| Traitement en parallèle des requêtes | La V12 offre un degré plus élevé de parallélisme pour les requêtes qui peuvent en bénéficier. |
| Latence d’E/S plus courte | La V12 a une latence considérablement plus courte pour les opérations d’entrée/sortie. |
| Augmentation des IOPS | La V12 peut traiter une plus grande quantité d’opérations E/S par seconde (IOPS). |
| Taux d’enregistrement | La V12 peut enregistrer davantage de modifications de données par seconde. |


## La V12 sera utilisée par défaut par l’API dès le 1er août 2015  


| Fonctionnalité | Description |
| :--- | :--- |
| . | ***Août 2015 :*** |
| Créer un serveur à l’aide de REST ou de PowerShell | Lorsque vous créez un serveur sans spécifier de version de serveur, la version par défaut n’est plus la version 11, mais la version 12.<br/><br/>Cela correspond au [portail Azure en version préliminaire](http://portal.azure.com). |
| Créer une base de données via Transact-SQL, [REST](http://msdn.microsoft.com/library/azure/gg715283.aspx) ou [PowerShell](http://msdn.microsoft.com/library/azure/dn806332.aspx) | Sur les serveurs version 11, lorsque vous créez une base de données sans spécifier une édition ou un objectif de service, l’objectif de service par défaut n’est plus « Web et Business », mais [S0](http://azure.microsoft.com/pricing/details/sql-database/). Cela correspond aux serveurs de version 12 dans le portail Azure en version préliminaire. |


## Résumé des améliorations


La V12 offre à la base de données SQL une compatibilité quasi totale des fonctionnalités avec notre produit SQL Server. La V12 se concentre sur les fonctionnalités les plus demandées et sur la prise en charge de la programmabilité. Le développement s’en trouve plus efficace et plus agréable. Vous pouvez désormais déplacer plus facilement vos applications de base de données SQL dans le cloud.


Par ailleurs, le niveau Premium de la V12 apporte des améliorations de performances considérables. Pour certaines applications, les requêtes s’exécutent beaucoup plus rapidement. Les applications avec d’importantes charges de travail bénéficient d’un débit fiable.


## <a name="V12AzureSqlDbPreviewGaTable"></a>État de disponibilité générale de la version 12 (par région)


> [AZURE.NOTE]
> [Pricing](http://azure.microsoft.com/pricing/details/sql-database/) a fait l’objet d’une réduction pendant la période de version préliminaire. La tarification normale pour toutes les régions doit être appliquée dès le mardi 31 mars 2015.


| Région Azure | État de lancement actuel<br/>pour la version 12 | Date du passage<br/>vers l’état de disponibilité générale |
| :--- | :--- | :--- |
| Sud du centre des États-Unis | Disponibilité générale | 9 février 2015 |
| Centre des États-Unis | Disponibilité générale | 9 février 2015 |
| Nord du centre des États-Unis | Disponibilité générale | 9 février 2015 |
| Ouest des États-Unis | Disponibilité générale | 9 février 2015 |
| Est des États-Unis | Disponibilité générale | 9 février 2015 |
| Est des États-Unis 2 | Disponibilité générale | 9 février 2015 |
| Asie de l’Est | Disponibilité générale | 24 février 2015 |
| Asie du Sud-Est | Disponibilité générale | 24 février 2015 |
| Ouest du Japon | Disponibilité générale | 24 février 2015 |
| Est du Japon | Disponibilité générale | 24 février 2015 |
| Europe du Nord | Disponibilité générale | 29 janvier 2015 |
| Europe de l’Ouest | Disponibilité générale | 29 janvier 2015 |
| Sud du Brésil | Disponibilité générale | 21 avril 2015 |
| Est de l’Australie | VERSION PRÉLIMINAIRE | Deuxième trimestre 2015, estimation |
| Sud-est de l’Australie | VERSION PRÉLIMINAIRE | Deuxième trimestre 2015, estimation |


Pour toute région ayant atteint la disponibilité générale, tous les nouveaux abonnements et les bases de données suivantes utilisent l’architecture de service V12 et ont par conséquent accès aux fonctionnalités plus récentes. Cet article répertorie les nouvelles fonctionnalités de la V12.


Pour toute région disposant de la version préliminaire et non de la version en disponibilité générale, vous devez [activer l’option permettant d’utiliser des bases de données de version 12](sql-database-v12-sign-up.md).


En disponibilité générale, si vous disposez de serveurs et de bases de données présentant une version inférieure à la version 12, vous pouvez décider d’effectuer la mise à niveau (transition) de vos bases de données vers une architecture de service version 12. Vous pourrez alors utiliser les nouvelles fonctionnalités en production. Les bases de données de version 12 doivent présenter le [niveau de tarification de base](sql-database-upgrade-new-service-tiers.md), Standard ou Premium.


## Comment procéder


Pour savoir comment mettre à niveau vos bases de données de la version 11 de la base de données SQL Microsoft Azure vers la version 12, voir [Planifier et préparer la mise à niveau vers la base de données SQL Microsoft Azure version 12](sql-database-v12-plan-prepare-upgrade.md).


Les numéros de version comme «V12 » font référence à la valeur renvoyée par l’instruction Transact-SQL suivante :<br/> **SELECT @@version;**


- 11.0.9228.18 *(V11)*
- 12.0.2000.8 *(ou légèrement supérieure, version 12)*


Pour accéder aux dernières informations de tarification sur la version 12, voir [Base de données SQL - Tarification](http://azure.microsoft.com/pricing/details/sql-database/).


## Précautions à prendre lors d’une mise à niveau vers la version 12


Vous devez tenir compte de limitations importantes lors d’une mise à niveau de la version 11 vers la version 12 et ce, pendant et après l’opération. Pour en savoir plus, accédez à ce lien vers une [valeur médiane](sql-database-v12-plan-prepare-upgrade.md#limitations) dans la section *Planifier et préparer la mise à niveau vers la version 12 de la base de données SQL*.


## Autres sources d’informations à jour


La section *Nouveautés* portant sur la base de données SQL Microsoft Azure version 12 ne sera bientôt plus mise à jour (à compter du 30 avril 2015, probablement). Les informations relatives aux nouvelles fonctionnalités et annonces seront migrées vers le lien suivant :


- [Annonces](http://azure.microsoft.com/updates/?service=sql-database) relatives à la base de données SQL Microsoft Azure sur notre page web **Mises à jour de service**.
 - Vous devez peut-être cliquer sur l’icône **RSS** de cette page web après avoir sélectionné *Base de données SQL* dans la liste déroulante Services.
- Suivez-nous sur Twitter : **@SQLTechCenter**.


[V12 general availability (GA) status per region]: #V12AzureSqlDbPreviewGaTable

<!---HONumber=58--> 