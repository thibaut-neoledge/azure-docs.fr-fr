<properties 
    pageTitle="Configurer la géoréplication pour Base de données SQL Azure avec le portail Azure | Microsoft Azure" 
    description="Configurer la géoréplication pour Base de données SQL Azure avec le portail Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="06/14/2016"
    ms.author="sstein"/>

# Configurer la géoréplication pour Base de données SQL Azure avec le portail Azure


> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-database-geo-replication-overview.md)
- [Portail Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

Cet article montre comment configurer la géoréplication active pour la Base de données SQL à l’aide du [portail Azure](http://portal.azure.com).

Pour lancer un basculement, consultez [Lancer un basculement planifié ou non planifié pour une base de données SQL Azure](sql-database-geo-replication-failover-portal.md).

>[AZURE.NOTE] La géo-réplication active (bases de données secondaires accessibles en lecture) est désormais disponible pour toutes les bases de données de tous les niveaux de service. En avril 2017 sera retiré le type secondaire non accessible en lecture et les bases de données non accessibles en lecture deviendront automatiquement des bases de données secondaires accessibles en lecture.

Pour configurer la géoréplication à l’aide du portail Azure, vous devez disposer des éléments suivants :

- Un abonnement Azure. 
- Une base de données SQL Azure : base de données primaire que vous souhaitez répliquer vers une autre région géographique.



## Ajout d'une base de données secondaire

Les étapes suivantes créent une nouvelle base de données secondaire dans un partenariat de géoréplication.

Pour ajouter une base de données secondaire, vous devez être le propriétaire ou copropriétaire de l’abonnement.

La base de données secondaire a le même nom que la base de données primaire et, par défaut, le même niveau de service. La base de données secondaire peut être accessible en lecture ou non, et il peut s’agir d’une base de données unique ou d’une base de données élastique. Pour plus d’informations, consultez [Niveaux de service](sql-database-service-tiers.md). Une fois la base de données secondaire créée et amorcée, une réplication asynchrone des données de la base de données primaire vers la base de données secondaire commence.

> [AZURE.NOTE] Si la base de données partenaire existe déjà (par exemple, suite à l’arrêt d’une relation de géoréplication précédente), la commande échoue.




### Ajouter une base de données secondaire

1. Dans le [portail Azure](http://portal.azure.com), accédez à la base de données que vous souhaitez configurer pour la géoréplication.
2. Dans le panneau de la base de données SQL, sélectionnez **Tous les paramètres** > **Géo-réplication**.
3. Sélectionnez la région pour créer la base de données secondaire.


    ![Ajouter une base de données secondaire][1]


4. Configurez le **Type secondaire** (**Accessible en lecture** ou **Non accessible en lecture**).
5. Sélectionnez ou configurer le serveur pour la base de données secondaire.

    ![Créer une base de données secondaire][3]

5. Si vous le souhaitez, vous pouvez ajouter une base de données secondaire à un pool de base de données élastique :

       - Cliquez sur **pool de base de données élastique**, puis sélectionnez un pool sur le serveur cible dans lequel créer la base de données secondaire. Un pool doit déjà exister sur le serveur cible, car ce flux de travail ne crée pas un nouveau pool.

6. Cliquez sur **créer** pour ajouter la base de données secondaire.
 
6. La base de données secondaire est créée et le processus d’amorçage commence.
 
    ![amorçage][6]

7. Lorsque le processus d’amorçage est terminé, la base de données secondaire affiche son état (Non accessible en lecture).

    ![base de données secondaire prête][9]



## Suppression d’une base de données secondaire

L’opération arrête définitivement la réplication vers la base de données secondaire et modifie le rôle de la base de données secondaire en une base de données normale accessible en lecture et en écriture. Si la connectivité à la base de données secondaire est interrompue, la commande aboutit, mais la base de données secondaire ne passe pas en mode lecture-écriture une fois la connectivité rétablie.

1. Dans le [portail Azure](http://portal.azure.com), accédez à la base de données primaire dans le partenariat de géoréplication.
2. Dans le panneau de la base de données SQL, sélectionnez **Tous les paramètres** > **Géo-réplication**.
3. Dans la liste des bases de données **SECONDAIRES**, sélectionnez la base de données que vous souhaitez supprimer du partenariat de géoréplication.
4. Cliquez sur **Arrêter la réplication**.

    ![supprimer la base de données secondaire][7]


5. Un clic sur **Arrêter la réplication** ouvre une fenêtre de confirmation. Cliquer sur **Oui** pour supprimer la base de données du partenariat de géoréplication (définissez-la comme base de données en lecture-écriture ne faisant partie d’aucune réplication).


    ![confirmer la suppression][8]



   

## Étapes suivantes

- [Lancer un basculement planifié ou non planifié pour une base de données SQL Azure](sql-database-geo-replication-failover-portal.md)
- [Conception d’applications cloud pour la continuité d’activité à l’aide de la géo-réplication](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Exercices de récupération d'urgence](sql-database-disaster-recovery-drills.md)


## Ressources supplémentaires

- [Configuration de la sécurité de la géo-réplication](sql-database-geo-replication-security-config.md)
- [Coup de projecteur sur les nouvelles fonctionnalités de géoréplication](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [FAQ sur la continuité d’activité et la récupération d’urgence des bases de données SQL](sql-database-bcdr-faq.md)
- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Conception d'applications pour la récupération d'urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finaliser la base de données SQL Microsoft Azure restaurée](sql-database-recovered-finalize.md)


<!--Image references-->
[1]: ./media/sql-database-geo-replication-portal/configure-geo-replication.png
[2]: ./media/sql-database-geo-replication-portal/add-secondary.png
[3]: ./media/sql-database-geo-replication-portal/create-secondary.png
[4]: ./media/sql-database-geo-replication-portal/secondary-type.png
[5]: ./media/sql-database-geo-replication-portal/create.png
[6]: ./media/sql-database-geo-replication-portal/seeding0.png
[7]: ./media/sql-database-geo-replication-portal/remove-secondary.png
[8]: ./media/sql-database-geo-replication-portal/stop-confirm.png
[9]: ./media/sql-database-geo-replication-portal/seeding-complete.png
[10]: ./media/sql-database-geo-replication-portal/failover.png

<!---HONumber=AcomDC_0615_2016-->