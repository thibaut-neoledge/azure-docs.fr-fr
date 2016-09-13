
<!--
includes/sql-database-create-new-database-portal.md

Latest Freshness check:  2016-04-11 , carlrab.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md

-->
## Créer une base de données SQL Azure

Suivez les étapes suivantes dans le portail Azure pour créer une base de données SQL Azure sur un serveur logique de base de données SQL Azure nouveau ou existant.

1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](http://portal.azure.com).
2. Cliquez sur **Nouveau**, entrez **Base de données SQL**, puis cliquez sur **Base de données SQL (nouvelle base de données)**.

     ![Nouvelle base de données](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-1.png)

3. Cliquez sur **Base de données SQL (nouvelle base de données)**.

     ![Nouvelle base de données](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-2.png)

4. Cliquez sur **Créer** pour créer une base de données dans le service Base de données SQL.

     ![Nouvelle base de données](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-3.png)

5. Indiquez des valeurs pour les propriétés de serveur suivantes :

 - Nom de la base de données
 - Abonnement : cela s’applique uniquement si vous avez plusieurs abonnements.
 - Groupe de ressources : si vous en êtes à la phase de mise en route, utilisez le groupe de ressources du serveur logique.
 - Sélectionner une source : vous pouvez choisir une base de données vide, des exemples de données ou une sauvegarde de base de données Azure. Pour migrer une base de données SQL Server locale ou charger des données à l’aide de l’outil de ligne de commande BCP, consultez les liens à la fin de cet article.
 - Serveur : un serveur logique nouveau ou existant.
 - Connexion d’administrateur serveur
 - Mot de passe
 - Niveau tarifaire : si vous en êtes à la phase de mise en route, utilisez la valeur par défaut, S0.
 - Classement : cela s’applique uniquement si vous avez choisi une base de données vide.

        ![New database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-4.png)

6.  Cliquez sur **Create**. Vous pouvez voir dans la zone de notification que le déploiement a démarré.

     ![Nouvelle base de données](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-5.png)

7. Attendez que le déploiement se termine avant de passer à l’étape suivante.

     ![Nouvelle base de données](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-6.png)

<!---HONumber=AcomDC_0907_2016-->