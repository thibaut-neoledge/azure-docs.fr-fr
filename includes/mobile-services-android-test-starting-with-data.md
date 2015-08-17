Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, vous pouvez la tester avec Mobile Services à l'aide de l'émulateur Android ou d'un téléphone Android.

1. Dans le menu **Exécuter**, cliquez sur **Exécuter** pour démarrer le projet.

	Cela permet d'exécuter votre application, créée avec le Kit de développement logiciel (SDK) Android, qui utilise la bibliothèque cliente pour envoyer une requête renvoyant des éléments à partir de votre service mobile.

5. Comme auparavant, tapez un texte explicite, puis cliquez sur **Ajouter**.

   	Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

    Vous pouvez redémarrer l'application pour vous assurer que les modifications ont été conservées dans la base de données sous Azure. Vous pouvez aussi examiner la base de données à partir du portail de gestion Azure : les deux étapes suivantes permettent de voir les modifications apportées à votre base de données.


4. Dans le portail de gestion Azure, cliquez sur Gérer pour la base de données associée à votre service mobile.

    ![](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png)

5. Dans le portail de gestion, exécutez une requête pour afficher les modifications effectuées par l'application Windows Store. Votre requête sera semblable à la requête suivante mais le nom de votre base de données sera utilisé à la place de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png)

Cela conclut le didacticiel **Prise en main des données** pour Android.

<!---HONumber=August15_HO6-->