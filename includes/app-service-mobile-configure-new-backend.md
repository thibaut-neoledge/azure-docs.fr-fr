
1. Dans les paramètres de serveur principal Mobile App, accédez à **Prise en main** > votre plateforme cliente. 

2. Sous **Créer une table API**, sélectionnez le **langage du serveur principal**, **C#** ou **Node.js** :

	+ **Serveur principal Node.js** (via portail) : acceptez l’accusé de réception, puis cliquez sur **Créer une table TodoItem**. Cette action crée une table *TodoItem* dans votre base de données.
	 
		>[AZURE.IMPORTANT]Le basculement d’un serveur principal d’application existant vers Node.js remplacera tout le contenu du site.

	+ **Serveur principal .NET (C#)** : cliquez sur **Télécharger**, extrayez les fichiers de projet compressés sur votre ordinateur local, ouvrez la solution dans Visual Studio, générez le projet pour restaurer les packages NuGet, puis déployez le projet dans Azure. Pour savoir comment déployer un projet de serveur principal .NET dans Azure, consultez la section *Déploiement du projet dans l’application web* de [Création d’une application web ASP.NET dans Azure App Service](../articles/app-service-web/web-sites-dotnet-get-started.md#deploy-the-project-to-the-web-app). Dans App Service, un serveur principal Mobile App est équivalent à une application web.
	 
Votre serveur principal Mobile App peut maintenant être utilisé avec votre application cliente.

<!---HONumber=AcomDC_1210_2015-->