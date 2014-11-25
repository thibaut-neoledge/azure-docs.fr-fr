1.  Dans Visual Studio dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet de service, puis cliquez sur **Démarrer une nouvelle instance** sous le menu contextuel **Déboguer**.

    ![démarrer le projet de service mobile localement][démarrer le projet de service mobile localement]

    Visual Studio ouvre la page Web par défaut de votre service. Par défaut, Visual Studio héberge votre service mobile localement dans IIS Express.

2.  Cliquez avec le bouton droit sur l'icône de barre d'état système IIS Express dans la barre des tâches Windows et vérifiez que votre service mobile a démarré.

    ![vérifier le service mobile dans la barre des tâches][vérifier le service mobile dans la barre des tâches]

3.  Sur la page de démarrage de votre service mobile, cliquez sur **try it out**.

    ![page de démarrage du service mobile][page de démarrage du service mobile]

    La page de documentation de l'API s'affiche et vous pouvez l'utiliser pour tester le service mobile.

    > [WACOM.NOTE]Authentication is not required to access this page when running locally. En cas d'exécution avec Azure, vous devez fournir la clé d'application ainsi que le mot de passe (sans nom d'utilisateur) pour accéder à cette page.

4.  Cliquez sur le lien **GET tables/TodoItem**.

    ![][0]

    La page de réponse GET pour l'API s'affiche alors.

5.  Cliquez sur **try this out** puis sur **envoyer**.

    ![][1]

    Une requête GET est alors envoyée au service mobile local pour renvoyer toutes les lignes de la table TodoItem. Comme la table est amorçée par l'initialiseur, deux objets TodoItem sont renvoyés dans le corps du message de réponse. Pour plus d'informations au sujet des initialisateurs, consultez [Modifications des modèles de données pour un service mobile principal .NET][Modifications des modèles de données pour un service mobile principal .NET].

    ![][2]

  [démarrer le projet de service mobile localement]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/vs-start-debug-service-project.png
  [vérifier le service mobile dans la barre des tâches]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/iis-express-tray.png
  [page de démarrage du service mobile]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-welcome-page.png
  [0]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-api-documentation-page.png
  [1]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-todoitems.png
  [Modifications des modèles de données pour un service mobile principal .NET]: ./fr-fr/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations/
  [2]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-response.png
