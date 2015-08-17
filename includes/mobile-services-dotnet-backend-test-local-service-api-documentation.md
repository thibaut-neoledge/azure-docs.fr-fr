
1. Dans Visual Studio, dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet de service, puis cliquez sur **Démarrer une nouvelle instance** sous le menu contextuel **Déboguer**.

    ![démarrer le projet de service mobile localement](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/vs-start-debug-service-project.png)

    Visual Studio ouvre la page web par défaut de votre service. Par défaut, Visual Studio héberge votre service mobile localement dans IIS Express.

2. Cliquez avec le bouton droit sur l’icône de barre d’état système IIS Express dans la barre des tâches Windows et vérifiez que votre service mobile a démarré.

	 ![vérifier le service mobile dans la barre des tâches](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/iis-express-tray.png)

3. Sur la page de démarrage de votre backend .NET, cliquez sur **try it out**.

    ![page de démarrage du service mobile](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-welcome-page.png)

    La page de documentation de l’API s’affiche et vous pouvez l’utiliser pour tester le service mobile.

	>[AZURE.NOTE]L'authentification n'est pas nécessaire pour accéder à cette page lors d'une exécution locale. En cas d'exécution avec Azure, vous devez fournir la clé d'application, ainsi que le mot de passe (sans nom d'utilisateur) pour accéder à cette page.

4. Cliquez sur le lien **GET tables/TodoItem**.

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-api-documentation-page.png)
   	
	La page de réponse GET pour l'API s'affiche alors.

5. Cliquez sur **try this out** puis sur **envoyer**.
 
	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-todoitems.png)

	Une requête GET est alors envoyée au service mobile local pour renvoyer toutes les lignes de la table TodoItem. Comme la table est amorçée par l'initialiseur, deux objets TodoItem sont renvoyés dans le corps du message de réponse. Pour plus d'informations au sujet des initialiseurs, consultez [Modifications des modèles de données pour un service mobile principal .NET](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-response.png)

<!---HONumber=August15_HO6-->