La [Bibliothèque Microsoft Azure Configuration Manager pour .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) que vous avez référencée ci-dessus fournit une classe pour l’analyse d’une chaîne de connexion à partir d’un fichier de configuration. La [classe CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) analyse les paramètres de configuration, que l’application cliente soit exécutée sur le bureau, sur un appareil mobile, dans une machine virtuelle Azure ou dans un service cloud Azure.

Pour référencer le package CloudConfigurationManager, ajoutez l’instruction `using` suivante à votre classe :

	using Microsoft.Azure;	//Namespace for CloudConfigurationManager

Voici un exemple montrant comment utiliser une chaîne de connexion d'un fichier de configuration :

    // Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		CloudConfigurationManager.GetSetting("StorageConnectionString"));

L’utilisation d’Azure Configuration Manager est facultative. Vous pouvez également utiliser une API, par exemple la [classe ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) de .NET Framework.

<!---HONumber=AcomDC_0601_2016-->