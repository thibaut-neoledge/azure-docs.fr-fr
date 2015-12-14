<properties 
    pageTitle="Configurer la géo-réplication pour Base de données SQL Azure avec le portail Azure | Microsoft Azure" 
    description="Configurer la géo-réplication pour Base de données SQL Azure avec le portail Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="12/01/2015"
    ms.author="sstein"/>

# Configurer la géo-réplication pour Base de données SQL Azure avec le portail Azure


> [AZURE.SELECTOR]
- [Azure portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


Cet article montre comment configurer la géo-réplication pour Base de données SQL à l’aide du [portail Azure](https://portal.azure.com).

La géo-réplication permet de créer jusqu’4 réplicas de bases de données (secondaires) dans différents centres de données (régions). Les bases de données secondaires sont disponibles en cas d’indisponibilité d’un centre de données ou l’incapacité à se connecter à la base de données primaire.

La géo-réplication est uniquement disponible pour les bases de données Standard et Premium.

Les bases de données standard peuvent avoir un serveur secondaire non accessible en lecture et doivent utiliser la région recommandée. Les bases de données Premium peuvent avoir jusqu’à quatre bases de données secondaires dans l’une des régions disponibles.


Pour configurer la géo-réplication, vous devez disposer des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d’un abonnement Azure, cliquez simplement sur **VERSION D’ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- Une base de données SQL Azure : base de données primaire que vous souhaitez répliquer vers une autre région géographique.



## Ajout d’une base de données secondaire

Les étapes suivantes créent une base de données secondaire dans un partenariat géo-réplication.

Pour ajouter une base de données secondaire, vous devez être le propriétaire ou copropriétaire de l’abonnement.

La base de données secondaire a le même nom que la base de données primaire et, par défaut, le même niveau de service. La base de données secondaire peut être accessible en lecture (niveau Premium uniquement) ou non, et il peut s’agir d’une base de données unique ou d’une base de données élastique. Pour plus d’informations, consultez [Niveaux de service](sql-database-service-tiers.md). Une fois la base de données secondaire créée et amorcée, une réplication asynchrone des données de la base de données primaire vers la base de données secondaire commence.

> [AZURE.NOTE]Si la base de données partenaire existe déjà (par exemple, suite à l’arrêt d’une relation de géo-réplication antérieure), la commande échoue.




### Ajouter une base de données secondaire

1. Dans le [portail Azure](https://portal.azure.com), accédez à la base de données que vous souhaitez configurer pour la géo-réplication.
2. Dans le panneau de la base de données SQL, sélectionnez **Tous les paramètres** > **Géo-réplication**.
3. Sélectionnez la région pour créer la base de données secondaire. Les bases de données Premium peuvent utiliser n’importe quelle région pour une base de données secondaire. Les bases de données Standard doivent utiliser la région recommandée :


    ![Ajouter une base de données secondaire][1]


4. Configurez le **type secondaire** (**Accessible en lecture**, ou **Non accessible en lecture**). Seules les bases de données Premium peuvent avoir des bases de données secondaires. Les bases de données secondaires Standard peuvent être uniquement définies sur **Non accessible en lecture**.
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

1. Dans le [portail Azure](https://portal.azure.com), accédez à la base de données primaire dans le partenariat de géo-réplication.
2. Dans le panneau Base de données SQL, sélectionnez **Tous les paramètres** > **Géo-réplication**.
3. Dans la liste des bases de données **secondaires**, sélectionnez la base de données que vous souhaitez supprimer du partenariat de géo-réplication.
4. Cliquez sur **Arrêter la réplication**.

    ![supprimer la base de données secondaire][7]


5. Un clic sur **Arrêter la réplication** ouvre une fenêtre de confirmation. Cliquer sur **Oui** pour supprimer la base de données du partenariat de géo-réplication (définissez-la comme base de données en lecture-écriture ne faisant partie d’aucune réplication).


    ![confirmer la suppression][8]



## Initialisation d’un basculement

La base de données secondaire peut être basculée en base de données primaire.

1. Dans le [portail Azure](https://portal.azure.com), accédez à la base de données primaire dans le partenariat de géo-réplication.
2. Dans le panneau Base de données SQL, sélectionnez **Tous les paramètres** > **Géo-réplication**.
3. Dans la liste des bases de données **secondaires** sélectionnez la base de données qui doit devenir la nouvelle base de données primaire.
4. Cliquez sur **Basculement**.

    ![basculement][10]

La commande exécute le flux de travail suivant :

1. Basculer temporairement la réplication en mode synchrone. Les transactions en attente seront alors transférées dans la base de données secondaire. 

2. Inversez les rôles primaire et secondaire des deux bases de données dans le partenariat de géo-réplication.

En cas de basculement planifié, cette séquence empêche toute perte de données. Il existe une courte période pendant laquelle les deux bases de données ne sont pas disponibles (de l’ordre de 0 à 25 secondes) pendant le basculement des rôles. Toute l’opération devrait prendre moins d’une minute dans des circonstances normales.

   

## Étapes suivantes

- [Conception d’applications cloud pour la continuité des activités à l’aide de la géo-réplication](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Exercices de récupération d’urgence](sql-database-disaster-recovery-drills.md)


## Ressources supplémentaires

- [Coup de projecteur sur les nouvelles fonctionnalités de géo-réplication](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication)
- [Conception d’applications cloud pour la continuité des activités à l’aide de la géo-réplication](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)


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

<!---HONumber=AcomDC_1203_2015-->