<properties
	pageTitle="Exécuter l’Assistant Activer la base de données pour Stretch| Microsoft Azure"
	description="Découvrez comment configurer une base de données pour Stretch Database en exécutant l’Assistant Activer la base de données pour Stretch."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglasl"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Exécuter l’Assistant Activer la base de données pour Stretch

Pour configurer une base de données pour Stretch Database, exécutez l’Assistant Activer la base de données pour Stretch. Cette rubrique décrit les informations que vous devez entrer et les choix que vous devez effectuer dans l’Assistant.

Pour en savoir plus sur Stretch Database, consultez [Stretch Database](sql-server-stretch-database-overview.md).

## Lancer l’Assistant

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez la base de données sur laquelle vous souhaitez activer Stretch.

2.  Cliquez avec le bouton droit et sélectionnez **Tâches**, sélectionnez **Stretch**, puis **Activer** pour lancer l’Assistant.

## <a name="Intro"></a>Introduction
Examinez l’objectif de l’Assistant et les conditions préalables.

## <a name="Tables"></a>Sélectionner des tables
Sélectionnez les tables que vous souhaitez activer pour Stretch.

|Colonne|Description|
|----------|---------------|
|(sans titre)|Cochez la case de cette colonne pour activer la table sélectionnée pour Stretch.|
|**Nom**|Spécifie le nom de la colonne dans la table.|
|(sans titre)|Un symbole dans cette colonne indique généralement que vous ne pouvez pas activer la table sélectionnée pour Stretch en raison d’un problème de blocage. C’est peut-être dû au fait que la table utilise un type de données non pris en charge. Placez le pointeur de la souris sur le symbole pour afficher plus d’informations dans une info-bulle. Pour plus d’informations, consultez les [limitations de la surface d’exposition et les problèmes de blocage pour Stretch Database](sql-server-stretch-database-limitations.md).|
|**Étendu**|Indique si la table est déjà activée.|
|**Lignes**|Spécifie le nombre de lignes dans la table.|
|**Taille (Ko)**|Spécifie la taille de la table en Ko.|
|**Migrer**|Dans CTP 3.1 jusqu’à RC0, vous pouvez migrer uniquement une table entière à l’aide de l’Assistant. Si vous souhaitez spécifier un prédicat pour sélectionner des lignes à migrer à partir d’une table qui contient à la fois des données d’historique et des données actuelles, exécutez l’instruction ALTER TABLE pour spécifier un prédicat après avoir quitté l’Assistant. Pour plus d’informations, consultez [Activer Stretch Database pour une table](sql-server-stretch-database-enable-table.md) ou [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|

## <a name="Configure"></a>Configurer le déploiement Azure

1.  Connectez-vous à Microsoft Azure avec un compte Microsoft.

2.  Sélectionnez l’abonnement Azure à utiliser pour Stretch Database.

3.  Sélectionnez une région Azure. Si vous créez un serveur, il est créé dans cette région.

    Pour réduire la latence, choisissez la région Azure où se trouve votre ordinateur SQL Server. Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/).

4.  Spécifiez si vous souhaitez utiliser un serveur existant ou créer un serveur Azure.

    Si l’annuaire Active Directory sur votre serveur SQL Server est fédéré avec Azure Active Directory, vous pouvez éventuellement utiliser un compte de service fédéré pour que SQL Server communique avec le serveur Azure distant. Pour plus d’informations sur la configuration requise pour cette option, consultez [Options SET d’ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).

    -   **Serveur existant**

        1.  Sélectionnez ou entrez le nom du serveur Azure existant.

        2.  Sélectionnez la méthode d’authentification.

            -   Si vous sélectionnez **Authentification SQL Server**, créez une connexion et un mot de passe.

            -   Sélectionnez **Authentification intégrée à Active Directory** pour utiliser un compte de service fédéré pour que SQL Server communique avec le serveur Azure distant.

    -   **Créer un serveur**

        1.  Créez une connexion et un mot de passe pour l’administrateur du serveur.

        2.  Si vous le souhaitez, utilisez un compte de service fédéré pour que SQL Server communique avec le serveur Azure distant.

## <a name="Credentials"></a>Informations d’identification sécurisées
Entrez un mot de passe fort pour créer une clé principale de base de données ou, s’il existe déjà une clé principale de base de données, entrez son mot de passe.

Vous devez avoir une clé principale de base de données pour sécuriser les informations d’identification utilisées par Stretch Database pour se connecter à la base de données distante.

Pour plus d’informations sur la clé principale de base de données, consultez [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) et [Créer une clé principale de base de données](https://msdn.microsoft.com/library/aa337551.aspx). Pour plus d’informations sur les informations d’identification créées par l’Assistant, consultez [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Sélectionner l’adresse IP
Utilisez l’adresse IP publique de votre ordinateur SQL Server, ou entrez une plage d’adresses IP, pour créer une règle de pare-feu sur Azure qui autorise SQL Server à communiquer avec le serveur Azure distant.

## <a name="Summary"></a>Résumé
Examinez les valeurs que vous avez entrées et les options que vous avez sélectionnées dans l’Assistant. Ensuite, sélectionnez **Terminer** pour activer Stretch.

## <a name="Results"></a>Résultats
Passez en revue les résultats.

Sélectionnez éventuellement **Surveiller** pour démarrer la surveillance de l’état de migration des données dans Stretch Database Monitor. Pour plus d’informations, consultez [Monitor and troubleshoot data migration (Stretch Database)](sql-server-stretch-database-monitor.md).

## <a name="KnownIssues"></a>Dépannage de l’Assistant
**Échec de l’Assistant Stretch Database.** Si Stretch Database n’est pas encore activé au niveau du serveur et que vous exécutez l’Assistant sans disposer des autorisations d’administrateur système pour l’activer, l’Assistant échoue. Demandez à l’administrateur système d’activer Stretch Database sur l’instance de serveur local, puis réexécutez l’Assistant. Pour plus d’informations, consultez [Condition préalable : Autorisation pour activer Stretch Database sur le serveur](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## Étapes suivantes
Activer des tables supplémentaires pour Stretch Database. Surveiller la migration des données et gérer des tables et des bases de données Stretch.

-   [Activer Stretch Database pour une table](sql-server-stretch-database-enable-table.md) pour activer des tables supplémentaires.

-   [Surveiller Stretch Database](sql-server-stretch-database-monitor.md) pour connaître l’état de migration des données.

-   [Suspendre et reprendre Stretch Database](sql-server-stretch-database-pause.md)

-   [Gérer et dépanner Stretch Database](sql-server-stretch-database-manage.md)

-   [Sauvegarder et restaurer des bases de données Stretch](sql-server-stretch-database-backup.md)

## Voir aussi
[Activer Stretch Database pour une base de données](sql-server-stretch-database-enable-database.md) [Activer Stretch Database pour une table](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0302_2016-->