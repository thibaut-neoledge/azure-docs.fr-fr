### Création de l’application console et obtention de l’assembly

Pour créer une application console dans Visual Studio et installer le package NuGet contenant Azure Storage Client Library :

1. Dans Visual Studio, choisissez **Fichier > Nouveau projet**, puis **Windows > Application console** dans la liste de modèles Visual C#.
2. Nommez l’application console, puis cliquez sur **OK**.
3. Une fois le projet créé, cliquez dessus avec le bouton droit dans l’Explorateur de solutions et choisissez **Gérer les packages NuGet**. Effectuez une recherche en ligne sur « WindowsAzure.Storage », puis cliquez sur **Installer** pour installer le package Azure Storage Client Library pour .NET et ses dépendances.

Les exemples de code présentés dans cet article utilisent également [Microsoft Azure Configuration Manager Library](https://msdn.microsoft.com/library/azure/mt634646.aspx) pour récupérer la chaîne de connexion de stockage à partir d’un fichier app.config dans l’application console. Azure Configuration Manager vous permet de récupérer votre chaîne de connexion lors de l’exécution, que votre application soit exécutée dans Microsoft Azure ou depuis un ordinateur de bureau, un appareil mobile ou une application web.

Pour installer le package Azure Configuration Manager, cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions et sélectionnez **Gérer les packages NuGet**. Exécutez une recherche en ligne sur « ConfigurationManager » et cliquez sur **Installer** pour installer le package.

L’utilisation d’Azure Configuration Manager est facultative. Vous pouvez également utiliser une API, par exemple la [classe ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) de .NET Framework.

<!---HONumber=AcomDC_0309_2016-->