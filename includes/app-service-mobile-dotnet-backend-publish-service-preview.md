Une fois que l'application cliente a été testée par rapport à l'application mobile locale, la dernière étape de ce didacticiel consiste à publier le serveur principal d'applications mobiles vers Azure et à exécuter l'application sur le service en ligne.

> [AZURE.NOTE] Cette procédure montre comment publier le serveur principal de votre application mobile en utilisant Visual Studio Tools. Vous pouvez également publier votre serveur principal .NET à l'aide du contrôle de code source.

1. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet de code d'application mobile (il s'agit du nom de votre application avec le terme " Service " ajouté) et sélectionnez **Publier**. 

	![Sélectionner Publier dans le projet de code d'application](./media/app-service-mobile-dotnet-backend-publish-service-preview/dotnet-project-publish.png)

2. Dans la boîte de dialogue **Publier le site web**, sélectionnez **Application mobile Azure** comme cible de publication. Dans la boîte de dialogue qui s'affiche, sélectionnez votre application mobile existante, qui correspond au nom de votre application mobile, avec le terme " code " ajouté.

    ![Sélectionner une application web existante pour y effectuer la publication](./media/app-service-mobile-dotnet-backend-publish-service-preview/mobile-quickstart-publish-select-service.png)

3. Cliquez sur **Valider la connexion** pour vérifier que la publication est correctement configurée, puis cliquez sur **Publier**.

	![Page finale de l'Assistant Paramètres de publication](./media/app-service-mobile-dotnet-backend-publish-service-preview/dotnet-publish-settings.png)

   Une fois la publication effectuée, la page de confirmation indiquant que le serveur principal d'applications mobiles est opérationnel dans Azure s'affiche. La fenêtre Sortie de Visual Studio indique également la réussite de l'opération.

<!--HONumber=49-->