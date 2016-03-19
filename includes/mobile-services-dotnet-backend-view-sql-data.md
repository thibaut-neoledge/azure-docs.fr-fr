
La dernière étape facultative de ce didacticiel consiste à archiver la base de données SQL associée au service mobile et à vérifier les données stockées.

1. Dans le[portail Azure Classic](https://manage.windowsazure.com/), cliquez sur Gérer pour la base de données associée à votre service mobile.
 
	![se connecter pour gérer la base de données SQL](./media/mobile-services-dotnet-backend-view-sql-data/manage-sql-azure-database.png)

2. Dans le portail, exécutez une requête pour afficher les modifications effectuées par l’application Windows Store. Votre requête sera semblable à la requête suivante mais le nom de votre base de données sera utilisé à la place de <code>todolist</code>.</p>

        SELECT * FROM [todolist].[todoitems]

    ![interroger la base de données SQL pour obtenir les éléments stockés](./media/mobile-services-dotnet-backend-view-sql-data/sql-azure-query.png)

	Notez que la table inclut les colonnes Id, \_\_createdAt, \_\_updatedAt, et \_\_version. Ces colonnes prennent en charge la synchronisation des données hors connexion et sont implémentées dans la classe de base [EntityData](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx). Pour plus d'informations, consultez la page [Prise en main de la synchronisation des données hors connexion].

<!---HONumber=AcomDC_1203_2015-->