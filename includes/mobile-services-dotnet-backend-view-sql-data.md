La dernière étape facultative de ce didacticiel consiste à archiver la base de données SQL associée au service mobile et à vérifier les données stockées.

1.  Dans le portail de gestion Azure, cliquez sur Gérer pour la base de données associée à votre service mobile.

    ![se connecter pour gérer la base de données SQL][se connecter pour gérer la base de données SQL]

2.  Dans le portail de gestion, exécutez une requête pour afficher les modifications effectuées par l'application Windows Store. Votre requête sera semblable à la requête suivante mais le nom de votre base de données sera utilisé à la place de `todolist`.


        SELECT * FROM [todolist].[todoitems]

    ![interroger la base de données SQL pour obtenir les éléments stockés][interroger la base de données SQL pour obtenir les éléments stockés]

    Notez que la table inclut les colonnes Id, \_\_createdAt, \_\_updatedAt, et \_\_version. Ces colonnes prennent en charge la synchronisation des données hors connexion et sont implémentées dans la classe de base [EntityData][EntityData]. Pour plus d'informations, consultez la page [Prise en main de la synchronisation des données hors connexion].

  [se connecter pour gérer la base de données SQL]: ./media/mobile-services-dotnet-backend-view-sql-data/manage-sql-azure-database.png
  [interroger la base de données SQL pour obtenir les éléments stockés]: ./media/mobile-services-dotnet-backend-view-sql-data/sql-azure-query.png
  [EntityData]: http://msdn.microsoft.com/fr-fr/library/microsoft.windowsazure.mobile.service.entitydata.aspx
