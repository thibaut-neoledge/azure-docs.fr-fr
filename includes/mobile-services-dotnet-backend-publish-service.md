

Après avoir testé l'application cliente sur le service mobile local, la dernière étape de ce didacticiel consiste à publier le service mobile sur Azure et à exécuter l'application sur le service en ligne.

>[AZURE.NOTE] Cette procédure montre comment publier votre service mobile à l'aide des outils de Visual Studio. Vous pouvez également publier votre service mobile principal .NET à l'aide du contrôle de code source. Pour plus d'informations, consultez [Stocker le code du projet dans le contrôle de code source](/fr-FR/documentation/articles/mobile-services-dotnet-backend-store-code-source-control/).

1. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet de service mobile, cliquez sur **Publier**, puis dans la boîte de dialogue **Publier le site web**, cliquez sur **Azure Mobile Services**.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish.png)
	
2. Connectez-vous avec les informations d'identification de votre compte Azure, sélectionnez votre service sous **Services mobiles existants**, puis cliquez sur **OK**.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-select-service.png)

	Visual Studio télécharge vos paramètres de publication depuis Azure.

	>[AZURE.NOTE] Visual Studio stocke vos informations d'identification Azure jusqu'à ce que vous vous déconnectiez explicitement.

3. Cliquez sur **Valider la connexion** pour vérifier que la publication est correctement configurée, puis sur **Publier**.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-2.png)

	Une fois la publication effectuée, vous accédez à une page vous confirmant l'exécution du service mobile sur Azure, cette fois.


<!--HONumber=42-->
