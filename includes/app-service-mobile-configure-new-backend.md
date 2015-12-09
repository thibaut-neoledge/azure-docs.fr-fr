
1. Dans les paramètres de serveur principal Mobile App, accédez à **Mobile**, cliquez sur **Mise en route** > Votre plateforme cliente. 

2. Sous **Créer une table API**, sélectionnez la **langue du serveur principal**, **C#** ou **Node.js** :

	+ **Node.js backend** (via portail) : acceptez l’accusé de réception, puis cliquez sur **Créer une table TodoItem**. Cette action crée une table *TodoItem* dans votre base de données.
	 
		>[AZURE.IMPORTANT]Le basculement d’un serveur principal d’application existant vers Node.js remplacera tout le contenu du site.

	+ **Serveur principal .NET (c#)** : cliquez sur **Télécharger**, extrayez les fichiers de projet compressés sur votre ordinateur local, ouvrez la solution dans Visual Studio, générez le projet pour restaurer les packages NuGet, puis déployez le projet sur Azure. Pour savoir comment déployer un projet de serveur principal .NET sur Azure, consultez la section *Déployer le projet sur l’application web* dans [Création d’une application web ASP.NET dans Azure App Service](../articles/app-service-web/web-sites-dotnet-get-started.md#deploy-the-project-to-the-web-app). Dans App Service, un serveur principal Mobile App est équivalent à une application web.
	 
Votre serveur principal Mobile App peut maintenant être utilisé avec votre application cliente.

<!---HONumber=AcomDC_1203_2015-->