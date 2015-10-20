<properties
	pageTitle="Interroger la base de données SQL avec le code C# | Microsoft Azure"
	description="Détails sur les adresses IP, les chaînes de connexion, le fichier de configuration pour une connexion sécurisée et Visual Studio gratuit, afin de permettre à votre programme C# de se connecter à votre base de données SQL Azure dans le cloud, à l’aide d’ADO.NET."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="10/09/2015"
	ms.author="genemi"/>


# Connexion et interrogation de votre base de données SQL avec C&#x23;

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Vous souhaitez écrire un programme C# qui utilise ADO.NET pour vous connecter à une base de données SQL Azure dans le cloud.

Cette rubrique décrit chaque étape pour les utilisateurs qui découvrent la base de données SQL Azure et le code C#. Les utilisateurs ayant de l’expérience avec Microsoft SQL Server et le code C# peuvent ignorer certaines étapes et se concentrer sur celles qui sont spécifiques à la base de données SQL.


## Composants requis


Pour exécuter l’exemple de code C#, vous devez disposer des éléments suivants :


- Un compte et un abonnement Azure. Vous pouvez vous inscrire à un [essai gratuit](http://azure.microsoft.com/pricing/free-trial/).


- Une base de données de démonstration **AdventureWorksLT** sur le service de base de données SQL Azure.
 - [Créez la base de données de démonstration](sql-database-get-started.md) en quelques minutes.


- Visual Studio 2013 Update 4 (ou version ultérieure). Microsoft propose désormais Visual Studio Community *gratuitement*.
 - [Visual Studio Community, téléchargement](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [Plus d’options gratuites Visual Studio](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)
 - Ou laissez l’[étape](#InstallVSForFree), plus loin dans cette rubrique, décrire comment le [portail Azure en version préliminaire](http://portal.azure.com/) vous guide dans l’installation de Visual Studio.


<a name="InstallVSForFree" id="InstallVSForFree"></a>

&nbsp;

## Étape 1 : Installer Visual Studio Community gratuitement


Si vous devez installer Visual Studio, vous pouvez :

- Installer Visual Studio Community gratuitement en accédant dans votre navigateur aux pages web de produit Visual Studio qui fournissent des téléchargements gratuits et d’autres options
- Laisser le [portail Azure en version préliminaire](http://portal.azure.com/) vous guider vers la page web de téléchargement, dont la description est fournie ci-après


### Visual Studio via le portail Azure en version préliminaire


1. Connectez-vous par le biais du [portail Azure en version préliminaire](http://portal.azure.com/), http://portal.azure.com/.

2. Cliquez sur **PARCOURIR* TOUT** > **Bases de données SQL**. Un volet s’ouvre pour la recherche des bases de données.

3. Dans la zone de texte de filtre près du haut, commencez à saisir le nom de votre base de données **AdventureWorksLT**.

4. Lorsque la ligne correspondant à votre base de données sur votre serveur s’affiche, cliquez dessus. Un volet s’ouvre pour votre base de données.

5. Pour des raisons pratiques, cliquez sur la commande de réduction sur chacun des volets précédents.

6. Cliquez sur le bouton **Ouvrir dans Visual Studio** vers le haut sur le volet de votre base de données. Un nouveau volet à propos de Visual Studio s’ouvre avec des liens vers des emplacements d’installation pour Visual Studio.
 
	![Bouton Ouvrir dans Visual Studio][20-OpenInVisualStudioButton]

7. Cliquez sur le lien **Community (gratuit)** ou sur un lien similaire. Une nouvelle page web est ajoutée.

8. Utilisez les liens de la nouvelle page web pour installer Visual Studio.

9. Une fois que Visual Studio est installé, sur le panneau **Ouvrir dans Visual Studio**, cliquez sur le bouton **Ouvrir dans Visual Studio**. Visual Studio s’ouvre.

10. Dans l’intérêt de son volet **Explorateur d’objets SQL Server **, Visual Studio vous demande de renseigner les champs de chaîne de connexion dans une boîte de dialogue.
 - Sélectionnez **Authentification SQL Server** et non **Authentification Windows**.
 - N’oubliez pas de spécifier votre base de données **AdventureWorksLT** (**Options** > **Propriétés de connexion** dans la boîte de dialogue).

11. Dans l’**Explorateur d’objets SQL Server**, développez le nœud pour votre base de données.


## Étape 2 : Créer un projet dans Visual Studio


Dans Visual Studio, créez un projet basé sur le modèle de démarrage pour le code C# > Windows > **Application console**.


1. Cliquez sur **Fichier** > **Nouveau** > **Projet**. La boîte de dialogue *** s’affiche.

2. Sous **Installé**, développez le code C# et Windows, afin que l’option **Application console** apparaisse dans le volet central.

	![Boîte de dialogue Nouveau projet][30-VSNewProject]

2. Pour le **Nom**, saisissez **ConnectAndQuery\_Example**. Cliquez sur **OK**.


## Étape 3 : Ajouter une référence d’assembly pour le processus de configuration


Notre exemple de code C# utilise l’assembly .NET Framework **System.Configuration.dll**, nous allons donc y ajouter une référence.


1. Dans le volet **Explorateur de solutions**, cliquez avec le bouton droit sur **Références** > **Ajouter une référence**. La fenêtre **Gestionnaire de références** s’ouvre.

2. Développez **Assemblys** > **Framework**.

3. Faites défiler l’écran, puis cliquez pour surligner **System.Configuration**. Vérifiez que la case est sélectionnée.

4. Cliquez sur **OK**.

5. Compilez votre programme en cliquant sur le menu **GÉNÉRER** > **Générer la solution**.


## Étape 4 : Obtenir la chaîne de connexion


Utilisez le [portail Azure en version préliminaire](http://portal.azure.com/) pour copier la chaîne de connexion de votre base de données.

Votre première utilisation permettra de connecter Visual Studio à votre base de données SQL Azure **AdventureWorksLT**.


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]


## Étape 5 : Ajouter la chaîne de connexion au fichier App.config


1. Dans Visual Studio, ouvrez le fichier App.config à partir du volet Explorateur de solutions.

2. Ajoutez l’élément **&#x3c;configuration&#x3e; &#x3c;/configuration&#x3e;**, comme indiqué dans l’exemple de code App.config suivant.
 - Remplacez *{vos\_espaces\_réservés}* par vos valeurs réelles :

```
	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup> 
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>
	
		<connectionStrings>
			<clear />
			<add name="ConnectionString4NoUserIDNoPassword"
			connectionString="Server=tcp:{your_serverName_here}.database.windows.net,1433; Database={your_databaseName_here}; Connection Timeout=30; Encrypt=True; TrustServerCertificate=False;"
			/>
		</connectionStrings>
	</configuration>
```

3. Enregistrez la modification App.config.

4. Dans le volet Explorateur de solutions, cliquez avec le bouton droit sur le nœud **App.config**, puis cliquez sur **Propriétés**.

5. Définissez l’option **Copier dans le répertoire de sortie** sur **Toujours copier**.
 - Ainsi, le contenu de votre fichier App.config remplace le contenu du fichier &#x2a;.exe.config dans le répertoire où le fichier &#x2a;.exe est créé. Le remplacement se produit chaque fois que vous recompilez le fichier &#x2a;.exe.
 - Le fichier &#x2a;.exe.config est lu lorsque notre exemple de programme C# s’exécute.

	![Copier dans le répertoire de sortie = Toujours copier][50-VSCopyToOutputDirectoryProperty]


## Étape 6 : Coller dans l’exemple de code C#


1. Dans Visual Studio, utilisez le volet **Explorateur de solutions** pour ouvrir votre fichier **Program.cs**. 

	![Coller dans notre exemple de code de programme C#][40-VSProgramCsOverlay]

2. Remplacez l’ensemble du code de démarrage dans Program.cs en collant l’exemple de code C# suivant.
 - Si vous souhaitez un exemple de code plus court, vous pouvez affecter l’ensemble de la chaîne de connexion en tant que littéral pour la variable **SQLConnectionString**. Ensuite, vous pouvez effacer les deux méthodes **GetConnectionStringFromExeConfig** et **GatherPasswordFromConsole**.


```
using System;  // C#
using G = System.Configuration;   // System.Configuration.dll
using D = System.Data;            // System.Data.dll
using C = System.Data.SqlClient;  // System.Data.dll
using T = System.Text;
	
namespace ConnectAndQuery_Example
{
	class Program
	{
		static void Main()
		{
			string connectionString4NoUserIDNoPassword,
				password, userName, SQLConnectionString;
	
			// Get most of the connection string from ConnectAndQuery_Example.exe.config
			// file, in the same directory where ConnectAndQuery_Example.exe resides.
			connectionString4NoUserIDNoPassword = Program.GetConnectionStringFromExeConfig
				("ConnectionString4NoUserIDNoPassword");
			// Get the user name from keyboard input.
			Console.WriteLine("Enter your User ID, without the trailing @ and server name: ");
			userName = Console.ReadLine();
			// Get the password from keyboard input.
			password = Program.GatherPasswordFromConsole();
	
			SQLConnectionString = "Password=" + password + ';' +
				"User ID=" + userName + ";" + connectionString4NoUserIDNoPassword;
	
			// Create an SqlConnection from the provided connection string.
			using (C.SqlConnection connection = new C.SqlConnection(SQLConnectionString))
			{
				// Formulate the command.
				C.SqlCommand command = new C.SqlCommand();
				command.Connection = connection;
	
				// Specify the query to be executed.
				command.CommandType = D.CommandType.Text;
				command.CommandText = @"
					SELECT TOP 9 CustomerID, NameStyle, Title, FirstName, LastName
					FROM SalesLT.Customer;  -- In AdventureWorksLT database.
					";
				// Open a connection to database.
				connection.Open();
	
				// Read data returned for the query.
				C.SqlDataReader reader = command.ExecuteReader();
				while (reader.Read())
				{
					Console.WriteLine("Values:  {0}, {1}, {2}, {3}, {4}",
						reader[0], reader[1], reader[2], reader[3], reader[4]);
				}
			}
			Console.WriteLine("View the results here, then press any key to finish...");
			Console.ReadKey(true);
		}
		//----------------------------------------------------------------------------------
	
		static string GetConnectionStringFromExeConfig(string connectionStringNameInConfig)
		{
			G.ConnectionStringSettings connectionStringSettings =
				G.ConfigurationManager.ConnectionStrings[connectionStringNameInConfig];
	
			if (connectionStringSettings == null)
			{
				throw new ApplicationException(String.Format
					("Error. Connection string not found for name '{0}'.",
					connectionStringNameInConfig));
			}
				return connectionStringSettings.ConnectionString;
		}
	
		static string GatherPasswordFromConsole()
		{
			T.StringBuilder passwordBuilder = new T.StringBuilder(32);
			ConsoleKeyInfo key;
			Console.WriteLine("Enter your password: ");
			do
			{
				key = Console.ReadKey(true);
				if (key.Key != ConsoleKey.Backspace)
				{
					passwordBuilder.Append(key.KeyChar);
					Console.Write("*");
				}
				else  // Backspace char was entered.
				{
					// Retreat the cursor, overlay '*' with ' ', retreat again.
					Console.Write("\b \b");
					passwordBuilder.Length = passwordBuilder.Length - 1;
				}
			}
			while (key.Key != ConsoleKey.Enter); // Enter key will end the looping.
			Console.WriteLine(Environment.NewLine);
			return passwordBuilder.ToString();
		}
	}
}
```


### Compilation de votre programme


1. Dans Visual Studio, compilez votre programme en cliquant sur le menu **Générer** > **Générer la solution**.


### Résumé des actions dans l’exemple de programme


1. Lecture de la plus grande partie de la chaîne de connexion SQL à partir d’un fichier de configuration.

2. Collecte du nom d’utilisateur et du mot de passe à partir du clavier et ajout de ces derniers afin de compléter la chaîne de connexion.

3. Utilisation de la chaîne de connexion et des classes ADO.NET pour se connecter à la base de données de démonstration **AdventureWorksLT** sur la base de données SQL Azure.

4. Émission d’une instruction SQL **SELECT** pour lire les éléments de la table **SalesLT**.

5. Impression des lignes renvoyées à la console.


Nous essayons de préserver la concision de l’exemple de code C#. Toutefois, nous avons ajouté du code pour lire un fichier de configuration afin de traiter plusieurs demandes de clients tels que vous. Nous admettons que les programmes de qualité de production doivent utiliser des fichiers de configuration au lieu de littéraux codés en dur dans le fichier .exe.


> [AZURE.WARNING]Afin de préserver la concision du code, dans cet exemple aux fins de formation, nous avons choisi de ne pas inclure de code pour la gestion des exceptions et la logique de nouvelle tentative. Toutefois, vos programmes de production qui interagissent avec une base de données cloud doivent inclure les deux.
>
> Vous trouverez [ici](sql-database-develop-csharp-retry-windows.md) un lien vers un exemple de code contenant la logique de nouvelle tentative.


## Étape 7 : Ajouter une plage d’adresses IP autorisées dans le pare-feu du serveur


Votre programme C# client ne peut pas se connecter à la base de données SQL Azure avant que l’adresse IP de l’ordinateur client ait été ajoutée dans le pare-feu de la base de données SQL. Votre programme échouera avec un message d’erreur pratique indiquant l’adresse IP nécessaire.


Vous pouvez utiliser le [portail Azure en version préliminaire](http://portal.azure.com/) pour ajouter l’adresse IP.



[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]



Pour plus d’informations, consultez <br/> [Procédure : configuration des paramètres du pare-feu sur SQL Database](sql-database-configure-firewall-settings.md).



## Étape 8 : Exécuter le programme


1. Dans Visual Studio, exécutez votre programme en cliquant sur le menu **DÉBOGUER** > **Démarrer le débogage**. Une fenêtre de console s’affiche.

2. Entrez votre nom d’utilisateur et un mot de passe, comme indiqué.
 - Quelques outils de connexion nécessitent l’ajout de la mention "@{votre\_Nomserveur\_ici}" à votre nom d’utilisateur mais, pour ADO.NET, ce suffixe est facultatif. Ne vous embêtez pas à taper le suffixe.

3. Des lignes de données s’affichent.


## Liens connexes


- [Exemples de code de démarrage rapide client pour SQL Database](sql-database-develop-quick-start-client-code-samples.md)

- Si votre programme client s’exécute sur une machine virtuelle Azure, découvrez des informations sur les ports TCP autres que 1433 à la page <br/>[Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md).



<!-- Image references. -->

[20-OpenInVisualStudioButton]: ./media/sql-database-connect-query/connqry-free-vs-e.png

[30-VSNewProject]: ./media/sql-database-connect-query/connqry-vs-new-project-f.png

[40-VSProgramCsOverlay]: ./media/sql-database-connect-query/connqry-vs-program-cs-overlay-g.png

[50-VSCopyToOutputDirectoryProperty]: ./media/sql-database-connect-query/connqry-vs-appconfig-copytoputputdir-h.png

<!---HONumber=Oct15_HO3-->