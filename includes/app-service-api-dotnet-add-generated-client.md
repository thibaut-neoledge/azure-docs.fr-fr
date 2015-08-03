3. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet (pas sur la solution), puis sélectionnez **Ajouter > Client d'application API Azure**. 

	![](./media/app-service-api-dotnet-add-generated-client/03-add-azure-api-client-v3.png)
	
3. Dans la boîte de dialogue **Ajouter un client d'application API Azure**, cliquez sur **Télécharger depuis une application API Azure**.

5. Dans la liste déroulante, sélectionnez l'application API que vous voulez appeler.

7. Cliquez sur **OK**.

	![Écran de génération](./media/app-service-api-dotnet-add-generated-client/04-select-the-api-v3.png)

	L'Assistant télécharge le fichier de métadonnées de l'API et génère une interface typée pour l'appel de l'application API.

	![Génération en cours](./media/app-service-api-dotnet-add-generated-client/05-metadata-downloading-v3.png)

	Une fois la génération de code terminée, un nouveau dossier s'affiche dans l'**Explorateur de solutions**, avec le nom de l'application API. Ce dossier contient le code qui implémente les classes et les modèles de données du client.

	![Génération terminée](./media/app-service-api-dotnet-add-generated-client/06-code-gen-output-v3.png)

<!---HONumber=July15_HO4-->