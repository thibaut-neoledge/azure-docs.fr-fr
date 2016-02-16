
1. Dans les paramètres de serveur principal Mobile App, accédez à **Démarrage rapide** > votre plateforme cliente. 

2. Sous **Créer une table API**, sélectionnez le **langage du serveur principal**, **C#** ou **Node.js** :

	+ **Serveur principal Node.js** (via portail) : acceptez l’accusé de réception, puis cliquez sur **Créer une table TodoItem**. Cette action crée une table *TodoItem* dans votre base de données.
	 
		>[AZURE.IMPORTANT] Le basculement d’un serveur principal d’application existant vers Node.js remplacera tout le contenu du site.

	+ **Serveur principal .NET (C#)** : cliquez sur **Télécharger**, extrayez les fichiers de projet compressés sur votre ordinateur local, ouvrez la solution dans Visual Studio, générez le projet pour restaurer les packages NuGet, puis déployez le projet dans Azure. Pour savoir comment déployer un projet de serveur principal .NET sur Azure, consultez [Procédure : publication du projet de serveur](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#publish-server-project) dans la rubrique Kit de développement logiciel (SDK) de serveur principal .NET.
	 
Votre serveur principal Mobile App peut maintenant être utilisé avec votre application cliente.

<!---HONumber=AcomDC_0211_2016-->