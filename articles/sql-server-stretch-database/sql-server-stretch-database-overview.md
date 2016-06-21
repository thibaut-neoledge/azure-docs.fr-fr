<properties
	pageTitle="Vue d’ensemble de Stretch Database | Microsoft Azure"
	description="Apprenez comment Stretch Database migre vos données d’historique en toute sécurité et de manière transparente vers le cloud Microsoft Azure."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# Présentation de Stretch Database

Stretch Database transfère vos données d’historique en toute sécurité et de manière transparente vers le cloud Microsoft Azure.

Si vous souhaitez commencer à utiliser Stretch Database maintenant, consultez [Prise en main en exécutant l’Assistant Activer la base de données pour Stretch](sql-server-stretch-database-wizard.md).

## Quels sont les avantages de Database Stretch ?
Database Stretch offre les avantages suivants :

**Assure la disponibilité des données brutes à faible coût** Étend dynamiquement les données transactionnelles à chaud et brutes de SQL Server vers Microsoft Azure avec la base de données SQL Server Stretch. Contrairement au stockage de données brutes par défaut, vos données sont toujours en ligne et disponibles pour interrogation. Vous pouvez fournir des délais de rétention de données plus longs sans entrer dans des banques contenant de grandes tables comme l’historique des commandes client. Tirez parti du faible coût du stockage Azure plutôt que de passer à la mise à l’échelle de stockage sur site. Le choix du niveau de tarification et la configuration des paramètres ont lieu dans le portail Azure et ce afin de garder le contrôle des prix. Augmentation ou réduction d’échelle, selon le cas. Pour plus d’informations, visitez la [page de tarification SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

**Ne nécessite pas de modification des requêtes ou des applications** Accédez à vos données SQL Server en toute transparence, qu’elles soient en local ou étirées vers le cloud. Vous définissez la stratégie qui détermine l’endroit où les données sont stockées, et SQL Server gère le déplacement des données en arrière-plan. L’ensemble de la table est toujours en ligne et peut être interrogé. En outre, Stretch Database ne nécessite pas de modification des requêtes ou des applications existantes. L’emplacement des données est totalement transparent pour l’application.

**Simplifie la maintenance des données locales** Réduit les besoins en maintenance et en stockage de vos données sur site. Les sauvegardes de la partie cloud de vos données s’exécutent de façon automatique. Les sauvegardes de vos données locales sur site s’exécutent plus vite et se terminent au sein de la fenêtre de maintenance. Vos besoins en stockage local sont considérablement réduits. Le stockage Azure peut être 80 % moins coûteux que l’ajout de disques SSD en local.

**Assure la sécurité des données même lors de la migration** Profitez d’une totale tranquillité d’esprit pendant que vous étirez vos applications les plus importantes en toute sécurité vers le cloud. Le chiffrement intégral SQL Server permet de crypter les données en cours de transfert. La sécurité de niveau de ligne (RLS) et d’autres fonctionnalités de sécurité avancées SQL Server fonctionnent également avec Stretch Database pour protéger vos données.

## Que fait la Stretch Database ?
Une fois Stretch Database activée pour une instance SQL Server, une base de données et au moins une table, elle commence à migrer vos données d’historique Azure en mode silencieux.

-   Si vous stockez des données d’historique dans une table distincte, vous pouvez migrer la totalité de la table.

-   Si votre table contient à la fois des données historiques et des données actuelles, vous pouvez spécifier un prédicat de filtre pour sélectionner les lignes à transférer.

Stretch Database garantit qu’aucune donnée n’est perdue en cas de défaillance pendant la migration. Elle est également dotée d’une logique de nouvelle tentative pour gérer les problèmes de connexion susceptibles de se produire pendant la migration. Une vue de gestion dynamique fournit l’état de la migration.

Vous pouvez suspendre la migration des données pour résoudre les problèmes sur le serveur local ou optimiser la bande passante réseau disponible.

Vous n’avez pas à modifier les requêtes et les applications client existantes. Vous continuez d’avoir un accès transparent aux données locales et distantes, même pendant la migration des données. Il existe un peu de latence pour les requêtes à distance, mais vous êtes confronté à cette latence uniquement lorsque vous interrogez les données d’historique.

![Vue d’ensemble de base de données Stretch][StretchOverviewImage1]

## Stretch Database est-il fait pour vous ?
Si vous êtes concerné par l’une des affirmations suivantes, Stretch Database peut répondre à vos exigences et résoudre vos problèmes.

|Si vous êtes un décideur|Si vous être administrateur de base de données|
|------------------------------|-------------------|
|Je dois conserver des données transactionnelles pendant un certain temps.|Je n’arrive plus à gérer la taille de mes tables.|
|Il arrive que je doive interroger les données d’historique.|Mes utilisateurs disent qu’ils souhaitent accéder à mes données d’historique, mais elles les utilisent rarement.|
|Je dispose d’applications, notamment d’applications anciennes que je ne souhaite pas mettre à jour.|Je dois continuer d’acheter et d’ajouter du stockage.|
|Je veux trouver un moyen de faire des économies sur le stockage.|Je ne peux pas enregistrer ou restaurer des tables aussi grandes au sein du SLA.|

## Quels types de bases de données ou de tables sont des candidats à Stretch Database ?
Stretch Database cible des bases de données transactionnelles contenant de grandes quantités de données historiques, généralement stockées dans quelques tables. Ces tables peuvent contenir plus d’un milliard de lignes.

Si vous utilisez la fonctionnalité de table temporelle de SQL Server 2016, utilisez Stretch Database pour migrer tout ou partie de la table d’historique associée à un stockage économique dans Azure. Pour plus d’informations, consultez [Gérer la rétention de données d’historique dans les tables temporelles de contrôle de version de système](https://msdn.microsoft.com/library/mt637341.aspx).

Utilisez Stretch Database Advisor, fonctionnalité de conseiller de mise à niveau de SQL Server 2016 pour identifier des bases de données et des tables pour Stretch Database. Pour plus d’informations, consultez [Identifier les bases de données et les tables pour Stretch Database](sql-server-stretch-database-identify-databases.md). Pour en savoir plus sur d’éventuels problèmes de blocage, consultez [Limitations for Stretch Database](sql-server-stretch-database-limitations.md) (Limitation concernant Stretch Database).

## <a name="FAQ"></a>Forum Aux Questions (FAQ) sur Stretch Database
**Stretch Database fonctionne-t-il avec <Nom de la fonctionnalité SQL Server> ?**
-   Pour obtenir la liste des fonctionnalités de SQL Server qui rendent une table inéligible pour Stretch, consultez [Limitations for Stretch Database](sql-server-stretch-database-limitations.md) (Limitations concernant Stretch Database).

-   Vous pouvez également télécharger le Conseiller de mise à niveau SQL Server 2016 et exécuter Stretch Database Advisor pour identifier des bases de données et des tables candidates pour Stretch Database. Pour plus d’informations, consultez [Identifier les bases de données et les tables pour Stretch Database](sql-server-stretch-database-identify-databases.md).

**Puis-je cibler une autre instance SQL Server locale pour Stretch Database ?** Non. Stretch Database ne prend pas en charge une autre instance de SQL Server locale comme point de terminaison distant.

**Puis-je désactiver Stretch et déplacer les données migrées vers la table locale ?** Oui. Pour plus d’informations, consultez [Désactiver Stretch Database et rapatrier des données distantes](sql-server-stretch-database-disable.md).

## Termes
**Base de données locale**. Base de données SQL Server locale.

**Point de terminaison distant**. Emplacement dans Microsoft Azure qui contient des données distantes de la base de données.

**Données locales**. Données d’une base de données avec Stretch Database activée qui ne seront pas déplacées vers Azure en fonction de la configuration Stretch Database des tables dans la base de données.

**Données éligibles**. Données d’une base de données avec Stretch Database activée qui n’ont pas encore été déplacées, mais seront déplacées vers Azure conformément à la configuration de Stretch Database des tables dans la base de données.

**Données distantes**. Données contenues dans une base de données avec Stretch Database qui ont été déplacées vers Azure.

## Architecture
Stretch Database s’appuie sur les ressources dans Microsoft Azure pour décharger le stockage de données d’archive et des requêtes.

Lorsque vous activez Stretch Database sur une base de données, il crée une définition de serveur lié sécurisée dans le SQL Server local. Cette définition de serveur lié a pour cible le point de terminaison distant. Lorsque vous activez Stretch Database sur une table dans la base de données, il approvisionne les ressources distantes et commence à migrer les données éligibles si la migration est activée.

Les requêtes sur les tables avec Stretch Database ont été automatiquement activées sur la base de données locale et le point de terminaison distant. Stretch Database s’appuie sur la puissance de traitement d’Azure pour exécuter des requêtes sur les données distantes en réécrivant la requête. Vous pouvez voir cette réécriture comme un opérateur de requête « distante » dans le nouveau plan de requête.

![Architecture de Stretch Database][StretchOverviewImage2]

## Sécurité et autorisations

### Activation et désactivation de Stretch Database pour une instance SQL Server
Pour commencer la configuration de bases de données pour Stretch Database, vous devez d’abord modifier l’option de configuration de niveau d’instance « archive de données distante » à l’aide de sp\_configure. Cette opération exige les privilèges sysadmin ou serveradmin. Si cette option est activée, vous pouvez configurer des bases de données pour Stretch Database, migrer les données et interroger des données sur le point de terminaison distant.

### Activation et désactivation de Stretch Database pour une base de données ou une table
Pour configurer une base de données Stretch Database, vous devez disposer de l’autorisation CONTROL DATABASE. En outre, vous devez fournir les informations d’identification administrateur correspondant au point de terminaison distant.

Pour configurer une table Stretch Database, vous devez disposer du privilège ALTER sur la table et la base de données doit déjà être configurée pour Stretch Database.

### Accès aux données
Seuls les systèmes de processus peuvent accéder à la définition de serveur lié derrière Stretch Database. Les connexions utilisateur ne peuvent pas émettre de requête via la définition du serveur lié pour le point de terminaison distant.

Stretch Database ne modifie pas le modèle d’autorisation d’une base de données existante. Les connexions utilisateur peuvent interroger la base de données dans une table avec Stretch Database via la base de données locale. La base de données locale effectue des vérifications d’autorisation pour toutes les actions lancées par l’utilisateur comme elle le fait pour d’autres objets. Si vous êtes autorisé à accéder à la table alors que Stretch Database est activé, vous avez accès à tout le contenu pour lequel vous disposez d’une autorisation, quel que soit l’emplacement physique des données.

![Modèle d’accès de données de Stretch Database][StretchOverviewImage3]

## Version d’évaluation de Stretch Database
**Version d’évaluation de Stretch Database avec la base de données AdventureWorks.** Pour obtenir la base de données exemple AdventureWorks, téléchargez au moins le fichier de base de données et les exemples et les fichiers script [ici](https://www.microsoft.com/download/details.aspx?id=49502). Après avoir restauré la base de données exemple sur une instance de SQL Server 2016, décompressez le fichier d’exemple et ouvrez le fichier exemple Stretch DB à partir du dossier Stretch DB. Exécutez les scripts de ce fichier pour vérifier l’espace utilisé par vos données avant et après avoir activé Stretch Database, pour suivre la progression de la migration des données, et pour confirmer que vous pouvez continuer d’interroger les données existantes et insérer de nouvelles données pendant et après la migration des données.

## Étape suivante
**Identifiez les bases de données et les tables candidates pour Stretch Database.** Téléchargez le Conseiller de mise à niveau SQL Server 2016 et exécutez le Stretch Database Advisor pour identifier des bases de données et des tables candidates pour Stretch Database. Stretch Database Advisor identifie également les problèmes de blocage. Pour plus d’informations, consultez [Identifier les bases de données et les tables pour Stretch Database](sql-server-stretch-database-identify-databases.md).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png

<!---HONumber=AcomDC_0615_2016-->