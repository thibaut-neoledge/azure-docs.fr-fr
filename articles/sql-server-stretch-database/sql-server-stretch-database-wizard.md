<properties
	pageTitle="Commencez par exécuter l’Assistant Activer la base de données pour Stretch | Microsoft Azure"
	description="Découvrez comment configurer une base de données pour Stretch Database en exécutant l’Assistant Activer la base de données pour Stretch."
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
	ms.topic="hero-article"
	ms.date="05/17/2016"
	ms.author="douglasl"/>

# Commencez par exécuter l’Assistant Activer la base de données pour Stretch

Pour configurer une base de données pour Stretch Database, exécutez l’Assistant Activer la base de données pour Stretch. Cette rubrique décrit les informations que vous devez entrer et les choix que vous devez effectuer dans l’Assistant.

Pour en savoir plus sur Stretch Database, consultez [Stretch Database](sql-server-stretch-database-overview.md).

## Lancer l’Assistant

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez la base de données sur laquelle vous souhaitez activer Stretch.

2.  Cliquez avec le bouton droit et sélectionnez **Tâches**, sélectionnez **Stretch**, puis **Activer** pour lancer l’Assistant.

## <a name="Intro"></a>Introduction
Examinez l’objectif de l’Assistant et les conditions préalables.

![Page d’introduction de l’Assistant Stretch Database][StretchWizardImage1]

## <a name="Tables"></a>Sélectionner des tables
Sélectionnez les tables que vous souhaitez activer pour Stretch.

![Page de sélection des tables de l’Assistant Stretch Database][StretchWizardImage2]

|Colonne|Description|
|----------|---------------|
|(sans titre)|Cochez la case de cette colonne pour activer la table sélectionnée pour Stretch.|
|**Nom**|Spécifie le nom de la colonne dans la table.|
|(sans titre)|Un symbole dans cette colonne indique généralement que vous ne pouvez pas activer la table sélectionnée pour Stretch en raison d’un problème de blocage. C’est peut-être dû au fait que la table utilise un type de données non pris en charge. Placez le pointeur de la souris sur le symbole pour afficher plus d’informations dans une info-bulle. Pour plus d’informations, consultez les [limitations de la surface d’exposition et les problèmes de blocage pour Stretch Database](sql-server-stretch-database-limitations.md).|
|**Étendu**|Indique si la table est déjà activée.|
|**Migrer**|Vous pouvez migrer une table entière (**Entire Table**), ou vous pouvez spécifier un prédicat de filtre basé sur la date dans l’Assistant. Si vous souhaitez utiliser un autre prédicat de filtre pour sélectionner les lignes à migrer, exécutez l'instruction ALTER TABLE afin de spécifier le prédicat de filtre après avoir quitté l'Assistant. Pour plus d’informations sur le prédicat de filtre, consultez [Utiliser un prédicat de filtre pour sélectionner les lignes à migrer (Stretch Database)](sql-server-stretch-database-predicate-function.md). Pour plus d’informations sur l'application du prédicat, consultez [Activer Stretch Database pour une table](sql-server-stretch-database-enable-table.md) ou [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|
|**Lignes**|Spécifie le nombre de lignes dans la table.|
|**Taille (Ko)**|Spécifie la taille de la table en Ko.|

## <a name="Filter"></a>Vous pouvez également fournir un prédicat de filtre basé sur la date

Si vous souhaitez fournir un prédicat de filtre basé sur la date pour sélectionner les lignes à migrer, procédez comme suit à la page **Sélectionner des tables**.

1.  Dans la liste **Sélectionner les tables à étirer**, cliquez sur **Table entière** dans la ligne pour la table. La boîte de dialogue **Sélectionner les lignes à étirer** s'ouvre.

    ![Définition d'un prédicat de filtre basé sur la date][StretchWizardImage2a]

2.  Dans la boîte de dialogue **Sélectionner les lignes à étirer**, choisissez **Choisir les lignes**.

3.  Dans le **champ Nom**, indiquez le nom du prédicat de filtre.

4.  Pour la clause **Où**, sélectionnez une colonne de date dans la table, choisissez un opérateur et spécifiez une valeur de date.

5. Cliquez sur **Vérifier** pour tester le prédicat. Si le prédicat retourne des résultats de la table, autrement dit, s'il existe des lignes à migrer qui remplissent la condition, le test réussit **(Success)**.

6.  Cliquez sur Terminé pour revenir à la page **Sélectionner des tables**.

    ![Page Sélectionner des tables après définition d'un prédicat de filtre][StretchWizardImage2b]

## <a name="Configure"></a>Configuration du déploiement Azure

1.  Connectez-vous à Microsoft Azure avec un compte Microsoft.

    ![Connexion à Azure dans l’Assistant Stretch Database][StretchWizardImage3]

2.  Sélectionnez l’abonnement Azure à utiliser pour Stretch Database.

3.  Sélectionnez une région Azure. Si vous créez un serveur, il est créé dans cette région.

    Pour réduire la latence, choisissez la région Azure où se trouve votre ordinateur SQL Server. Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/).

4.  Spécifiez si vous souhaitez utiliser un serveur existant ou créer un serveur Azure.

    Si l’annuaire Active Directory sur votre serveur SQL Server est fédéré avec Azure Active Directory, vous pouvez éventuellement utiliser un compte de service fédéré pour que SQL Server communique avec le serveur Azure distant. Pour plus d’informations sur la configuration requise pour cette option, consultez [Options SET d’ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).

	-   **Créer un serveur**

        1.  Créez une connexion et un mot de passe pour l’administrateur du serveur.

        2.  Si vous le souhaitez, utilisez un compte de service fédéré pour que SQL Server communique avec le serveur Azure distant.

		![Création d’un serveur Azure dans l’Assistant Stretch Database][StretchWizardImage4]

    -   **Serveur existant**

        1.  Sélectionnez ou entrez le nom du serveur Azure existant.

        2.  Sélectionnez la méthode d’authentification.

            -   Si vous sélectionnez **Authentification SQL Server**, créez une connexion et un mot de passe.

            -   Sélectionnez **Authentification intégrée à Active Directory** pour utiliser un compte de service fédéré pour que SQL Server communique avec le serveur Azure distant.

		![Sélection d’un serveur Azure existant dans l’Assistant Stretch Database][StretchWizardImage5]

## <a name="Credentials"></a>Informations d’identification sécurisées
Entrez un mot de passe fort pour créer une clé principale de base de données ou, s’il existe déjà une clé principale de base de données, entrez son mot de passe.

Vous devez avoir une clé principale de base de données pour sécuriser les informations d’identification utilisées par Stretch Database pour se connecter à la base de données distante.

![Page des informations d’identification sécurisées de l’Assistant Stretch Database][StretchWizardImage6]

Pour plus d’informations sur la clé principale de base de données, consultez [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) et [Création d'une clé principale de base de données](https://msdn.microsoft.com/library/aa337551.aspx). Pour plus d’informations sur les informations d’identification créées par l’Assistant, consultez [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Sélection de l’adresse IP
Utilisez l’adresse IP publique de votre ordinateur SQL Server, ou entrez une plage d’adresses IP, pour créer une règle de pare-feu sur Azure qui autorise SQL Server à communiquer avec le serveur Azure distant.

L’adresse IP, ou les adresses que vous entrez sur cette page, indiquent au serveur Azure d’autoriser les données entrantes, les requêtes et les opérations de gestion initiées par SQL Server à traverser le pare-feu Azure. L’Assistant ne modifie pas les paramètres du pare-feu sur le serveur SQL Server.

![Page de sélection de l’adresse IP de l’Assistant Stretch Database][StretchWizardImage7]

## <a name="Summary"></a>Résumé
Examinez les valeurs que vous avez entrées et les options que vous avez sélectionnées dans l’Assistant. Ensuite, sélectionnez **Terminer** pour activer Stretch.

![Page Résumé de l’Assistant Stretch Database][StretchWizardImage8]

## <a name="Results"></a>Résultats
Passez en revue les résultats.

Sélectionnez éventuellement **Surveiller** pour démarrer la surveillance de l’état de migration des données dans Stretch Database Monitor. Pour plus d’informations, consultez [Surveillance et dépannage de la migration de données (Stretch Database)](sql-server-stretch-database-monitor.md).

## <a name="KnownIssues"></a>Dépannage de l’Assistant
**Échec de l’Assistant Stretch Database.** Si Stretch Database n’est pas encore activé au niveau du serveur et que vous exécutez l’Assistant sans disposer des autorisations d’administrateur système pour l’activer, l’Assistant échoue. Demandez à l’administrateur système d’activer Stretch Database sur l’instance de serveur local, puis réexécutez l’Assistant. Pour plus d’informations, consultez [Condition préalable : Autorisation pour activer Stretch Database sur le serveur](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## Étapes suivantes
Activer des tables supplémentaires pour Stretch Database. Surveiller la migration des données et gérer des tables et des bases de données Stretch.

-   [Activer Stretch Database pour une table](sql-server-stretch-database-enable-table.md) pour activer des tables supplémentaires.

-   [Surveiller Stretch Database](sql-server-stretch-database-monitor.md) pour connaître l’état de migration des données.

-   [Suspendre et reprendre Stretch Database](sql-server-stretch-database-pause.md)

-   [Gérer et dépanner Stretch Database](sql-server-stretch-database-manage.md)

-   [Sauvegarder et restaurer des bases de données Stretch](sql-server-stretch-database-backup.md)

## Voir aussi

[Activer Stretch Database pour une base de données](sql-server-stretch-database-enable-database.md)

[Activer Stretch Database pour une table](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage2a]: ./media/sql-server-stretch-database-wizard/stretchwiz2a.png
[StretchWizardImage2b]: ./media/sql-server-stretch-database-wizard/stretchwiz2b.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png

<!---HONumber=AcomDC_0518_2016-->