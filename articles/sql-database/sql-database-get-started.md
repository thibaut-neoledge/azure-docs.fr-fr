---
title: "Démarrage rapide : Votre première solution Azure SQL Database | Microsoft Docs"
description: "Découvrez comment créer un serveur logique SQL Database, une règle de pare-feu de niveau serveur et des bases de données à l’aide du portail Azure. Vous apprendrez également à utiliser SQL Server Management Studio avec SQL Azure Database."
keywords: "didacticiel sur la base de données sql, créer une base de données sql"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: single databases
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/04/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6453cca9f876e6c363fbed463263c0f9684a3e70
ms.openlocfilehash: b838974de06ecbc751254064e2310df51c450086


---
# <a name="quick-start-tutorial-your-first-azure-sql-database"></a>Didacticiel de démarrage rapide : Votre première solution Azure SQL Database

Ce didacticiel de démarrage rapide vous apprendra plusieurs choses :

* [Création d’un serveur logique](sql-database-get-started.md#create-a-new-logical-sql-server) 
* [Affichage des propriétés de serveur logique](sql-database-get-started.md#view-the-logical-server-properties) 
* [Création d’une règle de pare-feu de niveau serveur](sql-database-get-started.md#create-a-server-level-firewall-rule) 
* [Connexion au serveur avec SSMS](sql-database-get-started.md#connect-to-the-server-with-ssms) 
* [Création d’une base de données avec des exemples de données](sql-database-get-started.md#create-a-database-with-sample-data) 
* [Affichage des propriétés de base de données](sql-database-get-started.md#view-the-database-properties) 
* [Interrogation de la base de données dans le portail Azure](sql-database-get-started.md#query-the-database-in-the-azure-portal) 
* [Connexion à la base de données et interrogation avec SSMS](sql-database-get-started.md#connect-and-query-the-database-with-ssms) 
* [Création d’une base de données vide avec SSMS](sql-database-get-started.md#create-a-blank-database-with-ssms) 
* [Résolution des problèmes de connectivité](sql-database-get-started.md#troubleshoot-connectivity) 
* [Supprimer une base de données](sql-database-get-started.md#delete-a-single-database) 


Dans ce didacticiel de démarrage rapide, vous allez créer un exemple de base de données et une base de données vide s’exécutant dans un groupe de ressources Azure, le tout attaché à un serveur logique. Vous créerez également deux règles de pare-feu de niveau serveur configurées pour permettre au principal au niveau du serveur de se connecter au serveur à partir de deux adresses IP spécifiées. Enfin, vous apprendrez à interroger une base de données dans le portail Azure, ainsi qu’à vous connecter et à effectuer des requêtes à l’aide de SQL Server Management Studio. 

**Durée estimée** : ce didacticiel prend environ 30 minutes (en supposant que vous remplissez déjà les conditions préalables).

> [!TIP]
> Vous pouvez exécuter ces mêmes tâches à l’aide de [C#](sql-database-get-started-csharp.md) ou de [PowerShell](sql-database-get-started-powershell.md).
>

## <a name="prerequisites"></a>Composants requis

* Vous avez besoin d’un compte Azure. Vous pouvez [ouvrir un compte Azure gratuit](/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer les avantages de l’abonnement à Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* Vous devez être en mesure de vous connecter au portail Azure à l’aide d’un compte qui soit membre du propriétaire de l’abonnement ou membre du rôle Collaborateur. Pour plus d’informations sur l’utilisation du contrôle d’accès en fonction du rôle (RBAC), consultez [Prise en main de la gestion des accès dans le portail Azure](../active-directory/role-based-access-control-what-is.md).

> [!NOTE]
> Ce didacticiel de démarrage rapide vous permet de vous familiariser avec le contenu des rubriques suivantes : [Vue d’ensemble du serveur Azure SQL Database](sql-database-server-overview.md), [Vue d’ensemble de SQL Database](sql-database-overview.md) et [Vue d’ensemble des règles de pare-feu d’Azure SQL Database](sql-database-firewall-configure.md).
>  


### <a name="sign-in-to-the-azure-portal-with-your-azure-account"></a>Connexion au portail Azure avec votre compte Azure
À l’aide de votre [compte Azure](https://account.windowsazure.com/Home/Index), suivez ces étapes pour vous connecter au portail Azure.

1. Ouvrez votre navigateur préféré et connectez-vous au [portail Azure](https://portal.azure.com/).
2. Connectez-vous au [portail Azure](https://portal.azure.com/).
3. Dans la page **de connexion** , entrez les informations d’identification de votre abonnement.
   
   ![de connexion](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="create-a-new-logical-sql-server"></a>Création d’un serveur logique SQL

Suivez les étapes de cette procédure pour créer un serveur logique avec le portail Azure, dans la région de votre choix.

1. Cliquez sur **Nouveau**, saisissez **sql server**, puis cliquez sur **ENTRÉE**.

    ![logical sql server](./media/sql-database-get-started/logical-sql-server.png)
2. Cliquez sur **Serveur SQL Server (serveur logique)**.
   
    ![create-logical sql server](./media/sql-database-get-started/create-logical-sql-server.png)
3. Cliquez sur **Créer** pour ouvrir le nouveau panneau Serveur SQL Server (serveur logique).

    ![new-logical sql server](./media/sql-database-get-started/new-logical-sql-server.png)
3. Dans la zone de texte Nom du serveur, indiquez un nom valide pour le nouveau serveur logique. Une coche verte indique que vous avez fourni un nom valide.
    
    ![new server name](./media/sql-database-get-started/new-server-name.png)

    > [!IMPORTANT]
    > Le nom complet de votre nouveau serveur se présente sous la forme suivante : <votre_nom_serveur>.database.windows.net.
    >
    
4. Dans la zone de texte serveur de connexion administrateur du serveur, fournissez un nom d’utilisateur pour la connexion d’authentification SQL de ce serveur. Cette connexion est connue comme la connexion principale du serveur. Une coche verte indique que vous avez fourni un nom valide.
    
    ![SQL admin login](./media/sql-database-get-started/sql-admin-login.png)
5. Dans les zones de texte **Mot de passe** et **Confirmer le mot de passe**, fournissez un mot de passe pour le compte de connexion principal du serveur. Une coche verte indique que vous avez fourni un mot de passe valide.
    
    ![SQL admin password](./media/sql-database-get-started/sql-admin-password.png)
6. Sélectionnez un abonnement dans lequel vous êtes autorisé à créer des objets.

    ![abonnement](./media/sql-database-get-started/subscription.png)
7. Dans la zone de texte Groupe de ressources, sélectionnez **Créer**, puis dans la zone de texte Groupe de ressources, fournissez un nom valide pour le nouveau groupe de ressources (vous pouvez également utiliser un groupe de ressources existant si vous en avez déjà créé un pour vous-même). Une coche verte indique que vous avez fourni un nom valide.

    ![new resource group](./media/sql-database-get-started/new-resource-group.png)

8. Dans la zone de texte **Emplacement**, sélectionnez un centre de données approprié à votre emplacement, tel que « Est de l’Australie ».
    
    ![server location](./media/sql-database-get-started/server-location.png)
    
    > [!TIP]
    > La case à cocher **Autoriser les services Azure à accéder au serveur** ne peut pas être modifiée dans ce panneau. Vous pouvez modifier ce paramètre dans le panneau de pare-feu du serveur. Pour plus d'informations, consultez [Prise en main de la sécurité](sql-database-control-access-sql-authentication-get-started.md).
    >
    
9. Cliquez sur **Create**.

    ![créer un bouton](./media/sql-database-get-started/create.png)

## <a name="view-the-logical-server-properties"></a>Affichage des propriétés du serveur logique

Suivez les étapes de cette procédure pour afficher les propriétés du serveur avec le portail Azure. Vous aurez besoin du nom de serveur complet pour vous connecter à ce serveur dans le cadre d’une procédure ultérieure. 

1. Dans le portail Azure, cliquez sur **More services** (Plus de services).

    ![more services](./media/sql-database-get-started/more-services.png)
2. Dans la zone de texte de filtre, tapez **SQL**, puis cliquez sur l’étoile pour les serveurs SQL afin de spécifier les serveurs SQL comme favoris dans Azure. 

    ![set favorite](./media/sql-database-get-started/favorite.png)
3. Dans le panneau par défaut, cliquez sur **Serveurs SQL** pour ouvrir la liste des serveurs SQL dans votre abonnement Azure. 

    ![new sql server](./media/sql-database-get-started/new-sql-server.png)

4. Cliquez sur votre nouveau serveur SQL pour afficher ses propriétés dans le portail Azure. Les didacticiels suivants vous aident à comprendre les options disponibles dans ce panneau.

    ![sql server blade](./media/sql-database-get-started/sql-server-blade.png)
5. Sous Paramètres, cliquez sur **Propriétés** pour afficher les différentes propriétés du serveur logique SQL.

    ![sql server properties](./media/sql-database-get-started/sql-server-properties.png)
6. Copiez le nom de serveur complet dans le Presse-papiers pour une utilisation ultérieure dans ce didacticiel.

    ![sql server full name](./media/sql-database-get-started/sql-server-full-name.png)

## <a name="create-a-server-level-firewall-rule"></a>créer une règle de pare-feu au niveau du serveur ;

Suivez les étapes de cette procédure pour créer une nouvelle règle de pare-feu de niveau serveur avec le portail Azure. Elle vous servira à vous connecter à votre serveur avec SQL Server Management Studio dans la procédure suivante.

1. Dans le panneau SQL Server, sous Paramètres, cliquez sur **Pare-feu** pour ouvrir le panneau de pare-feu pour le serveur SQL Server.

    ![sql server firewall](./media/sql-database-get-started/sql-server-firewall.png)

2. Cliquez sur **Ajouter une adresse IP cliente** dans la barre d’outils.

    ![add client IP](./media/sql-database-get-started/add-client-ip.png)

    > [!NOTE]
    > Vous pouvez ouvrir le pare-feu SQL Database sur le serveur pour une adresse IP unique ou un ensemble d’adresses. Ouvrir le pare-feu permet aux administrateurs et utilisateurs SQL de se connecter à toute base de données sur le serveur pour laquelle ils disposent d’informations d’identification valides.
    >

4. Cliquez sur **Enregistrer** sur la barre d’outils pour enregistrer cette règle de pare-feu de niveau serveur, puis sur **OK**.

    ![add client IP](./media/sql-database-get-started/save-firewall-rule.png)

## <a name="connect-to-the-server-with-ssms"></a>Connexion au serveur avec SSMS

Suivez les étapes de cette procédure pour vous connecter au serveur logique SQL avec SQL Server Management Studio.

1. Si ce n’est déjà fait, téléchargez et installez la dernière version de SSMS via [Téléchargement de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Pour vous permettre de rester à jour, la dernière version de SSMS vous envoie une invite lorsqu’une nouvelle version est disponible au téléchargement.

2. Une fois l’installation terminée, tapez **Microsoft SQL Server Management Studio** dans la zone de recherche de Windows, puis cliquez sur **Entrée** pour ouvrir SSMS :

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. Dans la boîte de dialogue Se connecter au serveur, entrez les informations nécessaires pour vous connecter à votre serveur SQL Server à l’aide de l’authentification SQL Server.

    ![connect to server](./media/sql-database-get-started/connect-to-server.png)
4. Cliquez sur **Connecter**.

    ![connected to server](./media/sql-database-get-started/connected-to-server.png)
5. Dans l’Explorateur d’objets, développez **Bases de données**, **Bases de données système** et **Master** pour afficher les objets dans la base de données master.

    ![master database](./media/sql-database-get-started/master-database.png)
6. Cliquez avec le bouton droit sur **Master**, puis cliquez sur **Nouvelle requête**.

    ![query master database](./media/sql-database-get-started/query-master-database.png)

8. Dans la fenêtre de requête, saisissez la requête suivante :

   ```select * from sys.objects```

9.  Dans la barre d’outils, cliquez sur **Exécuter** pour retourner une liste de tous les objets système dans la base de données master.

    ![query master database system objects](./media/sql-database-get-started/query-master-database-system-objects.png)

    > [!NOTE]
    > Pour explorer la sécurité SQL, consultez [Prise en main de la sécurité](sql-database-control-access-sql-authentication-get-started.md).
    >

## <a name="create-a-database-with-sample-data"></a>Création d’une base de données avec des exemples de données

Suivez les étapes de cette procédure pour créer une base de données avec des exemples de données, dans le portail Azure. La base de données créée sera attachée au serveur logique créé précédemment. Si le niveau de service De base n’est pas disponible dans la région dans laquelle vous avez créé votre serveur, supprimez celui-ci, puis recréez-le dans une autre région. Pour les étapes de suppression, consultez la dernière procédure de ce didacticiel.

1. Dans le portail Azure, cliquez sur **SQL databases** dans le panneau par défaut.

    ![bases de données SQL](./media/sql-database-get-started/new-sql-database.png)
2. Dans le panneau de bases de données SQL, cliquez sur **Ajouter**.

    ![add sql database](./media/sql-database-get-started/add-sql-database.png)
3. Dans le panneau de base de données SQL, examinez les informations remplies à votre place.

    ![sql database blade](./media/sql-database-get-started/sql-database-blade.png)
4. Fournissez un nom de base de données valide.

    ![sql database name](./media/sql-database-get-started/sql-database-name.png)
5. Sous Sélectionner une source, cliquez sur **Exemple**, puis sous Sélectionner un exemple, cliquez sur **AdventureWorksLT [V12]**.
   
    ![adventure works lt](./media/sql-database-get-started/adventureworkslt.png)
6. Sous Serveur, fournissez le nom d’utilisateur et le mot de passe admin de connexion du serveur.

    ![server credentials](./media/sql-database-get-started/server-credentials.png)

    > [!NOTE]
    > Lorsqu’une base de données est ajoutée à un serveur, elle peut être ajoutée en tant que base de données unique (valeur par défaut) ou ajoutée à un pool élastique. Pour plus d’informations sur les pools élastiques, consultez [Pools élastiques](sql-database-elastic-pool.md).
    >

7. Sous Niveau tarifaire, changez le niveau de tarification pour choisir **De base** (vous pouvez augmenter le niveau de tarification ultérieurement si vous le souhaitez, mais à des fins d’apprentissage, nous vous recommandons d’utiliser le niveau de coût le plus bas).

    ![Niveau de tarification](./media/sql-database-get-started/pricing-tier.png)
8. Cliquez sur **Create**.

    ![créer un bouton](./media/sql-database-get-started/create.png)

## <a name="view-the-database-properties"></a>Affichage des propriétés de base de données

Suivez les étapes de cette procédure pour interroger la base de données avec le portail Azure.

1. Dans le panneau de bases de données SQL, cliquez sur votre nouvelle base de données pour afficher ses propriétés dans le portail Azure. Les didacticiels suivants vous aident à comprendre les options disponibles dans ce panneau. 

    ![new sample db blade](./media/sql-database-get-started/new-sample-db-blade.png)
2. Cliquez sur **Propriétés** pour afficher des informations supplémentaires sur votre base de données.

    ![new sample db properties](./media/sql-database-get-started/new-sample-db-properties.png)

3. Cliquez sur **Afficher les chaînes de connexion de la base de données**.

    ![new sample db connection strings](./media/sql-database-get-started/new-sample-db-connection-strings.png)
4. Cliquez sur **Vue d’ensemble**, puis cliquez sur le nom de votre serveur dans le volet Essentials.
    
    ![new sample db essentials pane](./media/sql-database-get-started/new-sample-db-essentials-pane.png)
5. Dans le volet Essentials pour votre serveur, vous pouvez voir la base de données que vous venez d’ajouter.

    ![new sample db in server essentials pane](./media/sql-database-get-started/new-sample-db-server-essentials-pane.png)

## <a name="query-the-database-in-the-azure-portal"></a>Interrogation de la base de données dans le portail Azure

Suivez les étapes de cette procédure pour interroger la base de données avec l’éditeur de requêtes, dans le portail Azure. La requête affiche les objets dans la base de données.

1. Dans le panneau des bases de données SQL, cliquez sur **Outils** dans la barre d’outils.

    ![outils](./media/sql-database-get-started/tools.png)
2. Dans le panneau Outils, cliquez sur **Éditeur de requêtes (version préliminaire)**.

    ![query editor](./media/sql-database-get-started/query-editor.png)
3. Cliquez sur la case à cocher pour confirmer que l’éditeur de requêtes est une fonctionnalité préliminaire, puis cliquez sur **OK**.
4. Dans le panneau de l’**Éditeur de requêtes**, cliquez sur **Connexion**.

    ![query editor blade](./media/sql-database-get-started/query-editor-blade.png)
5. Examinez le type d’autorisation et la connexion, puis fournissez le mot de passe pour cette connexion. 

    ![query editor login](./media/sql-database-get-started/query-editor-login.png)
6. Cliquez sur **OK** pour tenter de vous connecter.
7. Si vous recevez une erreur de connexion indiquant que votre client n’est pas autorisé à se connecter en raison de l’absence de règle de pare-feu pour l’adresse IP du client, copiez l’adresse IP du client dans la fenêtre d’erreur puis, dans le panneau de serveur SQL pour cette base de données, créez une règle de pare-feu de niveau serveur.

    ![query editor error](./media/sql-database-get-started/query-editor-error.png)
8. Répétez les 6 étapes précédentes pour vous connecter à votre base de données.
9. Après vous être authentifié, saisissez la requête suivante dans la fenêtre de requête :

   ```select * from sys.objects```

    ![query editor query](./media/sql-database-get-started/query-editor-query.png)
10.  Cliquez sur **Exécuter**.
11. Passez en revue les résultats de la requête dans le panneau **Résultats**.

    ![query editor results](./media/sql-database-get-started/query-editor-results.png)

## <a name="connect-and-query-the-database-with-ssms"></a>Connexion à la base de données et interrogation avec SSMS

Suivez les étapes de cette procédure pour vous connecter à la base de données avec SQL Server Management Studio, puis interrogez les exemples de données pour afficher les objets dans la base de données.

1. Basculez vers SQL Server Management Studio et, dans l’Explorateur d’objets, cliquez sur **Bases de données**, puis sur **Actualiser** dans la barre d’outils pour afficher l’exemple de base de données.

    ![new sample db with ssms](./media/sql-database-get-started/new-sample-db-ssms.png)
2. Dans l’Explorateur d’objets, développez votre nouvelle base de données pour afficher ses objets.

    ![new sample db objects with ssms](./media/sql-database-get-started/new-sample-db-objects-ssms.png)
3. Cliquez avec le bouton droit sur votre exemple de base de données, puis cliquez sur **Nouvelle requête**.

    ![new sample db query with ssms](./media/sql-database-get-started/new-sample-db-query-ssms.png)
4. Dans la fenêtre de requête, saisissez la requête suivante :

   ```select * from sys.objects```
   
9.  Dans la barre d’outils, cliquez sur **Exécuter** pour retourner une liste de tous les objets système dans l’exemple de base de données.

    ![new sample db query system objects with ssms](./media/sql-database-get-started/new-sample-db-query-objects-ssms.png)

## <a name="create-a-blank-database-with-ssms"></a>Création d’une base de données vierge avec SSMS

Suivez les étapes de cette procédure pour créer une base de données sur le serveur logique avec SQL Server Management Studio.

1. Dans l’Explorateur d’objets, cliquez avec le bouton droit sur **Bases de données**, puis cliquez sur **Nouvelle base de données**.

    ![new blank database with ssms](./media/sql-database-get-started/new-blank-database-ssms.png)

    > [!NOTE]
    > Vous pouvez également ordonner à SSMS de créer pour vous un script de base de données afin de générer une base de données à l’aide de Transact-SQL.
    >

2. Dans la boîte de dialogue Nouvelle base de données, fournissez un nom dans la zone de texte Nom de la base de données. 

    ![new blank database name with ssms](./media/sql-database-get-started/new-blank-database-name-ssms.png)

3. Dans la boîte de dialogue Nouvelle base de données, cliquez sur **Options** puis modifiez l’édition pour choisir **De base**.

    ![new blank database options with ssms](./media/sql-database-get-started/new-blank-database-options-ssms.png)

    > [!TIP]
    > Passez en revue les autres options de cette boîte de dialogue que vous pouvez modifier pour une base de données SQL Azure. Pour plus d'informations sur ces options, consultez [Create Database](https://msdn.microsoft.com/library/dn268335.aspx).
    >

4. Cliquez sur **OK** pour créer la base de données vide.
5. Lorsque vous avez terminé, actualisez le nœud Base de données dans l’Explorateur d’objets pour afficher la base de données vide nouvellement créée. 

    ![new blank database in object explorer](./media/sql-database-get-started/new-blank-database-object-explorer.png)

## <a name="troubleshoot-connectivity"></a>Résoudre les problèmes de connectivité

> [!IMPORTANT]
> Si vous rencontrez des problèmes de connectivité, consultez [roblèmes de connectivité](sql-database-troubleshoot-common-connection-issues.md).
> 

## <a name="delete-a-single-database"></a>Suppression d’une base de données unique

Suivez les étapes de cette procédure pour supprimer une base de données unique avec le portail Azure.

1. Dans le panneau du portail Azure pour votre solution SQL Database, cliquez sur **Supprimer**.

    ![delete-database](./media/sql-database-get-started/delete-database.png)
2. Cliquez sur **Oui** pour confirmer que vous souhaitez supprimer définitivement cette base de données.

    ![delete-database-yes](./media/sql-database-get-started/delete-database-yes.png)

> [!TIP]
> Pendant la période de rétention de votre base de données, vous pouvez la restaurer à partir des sauvegardes automatiques initiées par le service. Pour les bases de données de l’édition de base, vous pouvez les restaurer dans les sept jours. Toutefois, ne supprimez pas de serveur. Si vous le faites, vous ne pourrez pas récupérer le serveur ou l’une de ses bases de données supprimées. Pour plus d’informations sur les sauvegardes de base de données, voir [Découvrir les sauvegardes SQL Database](sql-database-automated-backups.md) et pour plus d’informations sur la restauration d’une base de données à partir de sauvegardes, voir [Récupération de base de données](sql-database-recovery-using-backups.md). Pour connaître la procédure de restauration d’une base de données supprimée, consultez [Restaurer une base de données SQL supprimée (portail Azure)](sql-database-restore-deleted-database-portal.md).
>


## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez terminé ce didacticiel, vous souhaiterez peut-être explorer les nombreux autres didacticiels afin de mettre en œuvre ce que vous avez appris dans ce didacticiel. 

- Pour un didacticiel de prise en main de l’authentification SQL Server, voir [Authentification et autorisations SQL](sql-database-control-access-sql-authentication-get-started.md).
- Pour un didacticiel de prise en main de l’authentification Azure Active Directory, voir [Authentification et autorisations AAD](sql-database-control-access-aad-authentication-get-started.md).
* Si vous souhaitez interroger l’échantillon de base de données dans le portail Azure, consultez la section [Version préliminaire publique : Expérience interactive pour les requêtes portant sur les bases de données SQL](https://azure.microsoft.com/en-us/updates/azure-sql-database-public-preview-t-sql-editor/).
* Si vous connaissez Excel, découvrez comment [vous connecter à une base de données SQL dans Azure avec Excel](sql-database-connect-excel.md).
* Si vous êtes prêt à commencer le codage, choisissez votre langage de programmation dans [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md).
* Si vous souhaitez déplacer vos bases de données SQL Server locales vers Azure, consultez la page [Migration de base de données SQL Server vers SQL Database dans le cloud](sql-database-cloud-migrate.md).
* Si vous souhaitez charger des données dans une nouvelle table à partir d’un fichier CSV avec l’outil de ligne de commande BCP, voir [Chargement de données dans SQL Database à partir d’un fichier CSV à l’aide de BCP](sql-database-load-from-csv-with-bcp.md).
* Si vous souhaitez commencer à créer des tables et d’autres objets, consultez la rubrique « Création d’une table » dans [Création d’une table](https://msdn.microsoft.com/library/ms365315.aspx).

## <a name="additional-resources"></a>Ressources supplémentaires

- Pour une présentation technique, consultez [Définition de la base de données SQL - Présentation de SQL Database](sql-database-technical-overview.md).
- Pour plus d’informations sur la tarification, voir [Bases de données SQL - Tarification](https://azure.microsoft.com/pricing/details/sql-database/).




<!--HONumber=Feb17_HO2-->


