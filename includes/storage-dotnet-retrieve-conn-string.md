### Récupération de votre chaîne de connexion
Vous pouvez utiliser le type **CloudStorageAccount** pour représenter vos informations de compte de stockage. Si vous utilisez un modèle de projet Azure et/ou qu’une référence pointe vers l’espace de noms Microsoft.WindowsAzure.CloudConfigurationManager, vous pouvez utiliser le type **CloudConfigurationManager** pour extraire votre chaîne de connexion et les informations de votre compte de stockage depuis la configuration du service Azure :

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Si vous créez une application sans référence pointant vers Microsoft.WindowsAzure.CloudConfigurationManager et que votre chaîne de connexion est située dans le fichier `web.config` ou `app.config` comme indiqué précédemment, vous pouvez utiliser **ConfigurationManager** pour récupérer la chaîne de connexion. Vous devez ajouter une référence pointant vers System.Configuration.dll à votre projet, ainsi qu’une autre déclaration d’espace de noms :

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.AppSettings["StorageConnectionString"]);

<!---HONumber=AcomDC_0309_2016-->