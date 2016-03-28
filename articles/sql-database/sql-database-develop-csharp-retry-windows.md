<properties
	pageTitle="Logique de nouvelle tentative C# pour se connecter à SQL Database | Microsoft Azure"
	description="L’exemple C# inclut une logique de nouvelle tentative permettant d’interagir de manière fiable avec Azure SQL Database."
	services="sql-database"
	documentationCenter=""
	authors="annemill"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/15/2016"
	ms.author="annemill"/>


# Exemple de code : Logique de nouvelle tentative C# pour la connexion à une base de données SQL



[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]



Cette rubrique fournit un exemple de code C# qui illustre la logique de nouvelle tentative personnalisée. La logique de nouvelle tentative est conçue pour traiter de manière appropriée les erreurs temporaires ou défaillances *transitoires* qui tendent à disparaître si le programme attend quelques secondes et effectue de nouvelles tentatives.


Les classes ADO.NET que vous utilisez pour vous connecter à un serveur Microsoft SQL local peuvent également se connecter à une base de données SQL Azure. Toutefois, par elles-mêmes, les classes ADO.NET classes ne peuvent pas fournir toute la robustesse et la fiabilité nécessaires à la production. Votre programme client peut rencontrer des erreurs temporaires qu’il devrait normalement résoudre de manière silencieuse et appropriée.


Voici quelques exemples d’erreurs temporaires :


- Une connexion via Internet est soumise à de courtes coupures de réseau après lesquelles la connexion peut être recréée.

- Le cloud computing implique un équilibrage de charge qui peut bloquer brièvement les tentatives de connexion ou de requête.


## Identification des erreurs temporaires


Votre programme doit faire la distinction entre les erreurs temporaires et les erreurs persistantes. Si le programme a mal orthographié le nom de la base de données cible, l'erreur « Aucune base de données trouvée » persistera et se reproduira chaque fois que vous réexécutez le programme.


La liste des erreurs classées comme erreurs temporaires est disponible à l'adresse :


- [Messages d'erreur pour les programmes clients SQL Database](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - Consultez la section principale intitulée *Erreurs temporaires, erreurs de perte de connexion*.


## Exemple de code C#


L'exemple de code C# de la présente rubrique contient la logique de détection et de nouvelle tentative personnalisée permettant de gérer les erreurs temporaires. L’exemple part du principe que .NET Framework 4.5.1 (ou une version ultérieure) est installé.


L'exemple de code suit certaines règles élémentaires ou recommandations qui s'appliquent quelle que soit la technologie utilisée pour interagir avec Azure SQL Database. Vous pouvez consulter les recommandations générales ici :


- [Connexion à SQL Database : liens, bonnes pratiques et règles de conception](sql-database-connect-central-recommendations.md)


L’exemple de code C# se compose d’un fichier nommé Program.cs. Son code est collé dans la section suivante.


### Capturer et compiler l’exemple de code


Vous pouvez compiler l'exemple en effectuant les étapes suivantes :


1. Dans l’[édition Visual Studio Community gratuite](https://www.visualstudio.com/products/visual-studio-community-vs), créez un projet à partir du modèle Application console C#.
 - Fichier > Nouveau > Projet > Installé > Modèles > Visual C# > Windows > Bureau classique > Application console
 - Nommez le projet **RetryAdo2**.

2. Ouvrez le volet Explorateur de solutions.
 - Vérifiez que le nom de votre projet est présent.
 - Vérifiez que le nom du fichier Program.cs est présent.

3. Enregistrez le fichier Program.cs.

4. Remplacez entièrement le contenu du fichier Program.cs par le code du bloc de code suivant.

5. Cliquez sur le menu Générer > Générer la solution.


#### Code source C# à coller


Collez ce code dans le fichier **Program.cs**.


Vous devez ensuite modifier les chaînes de nom de serveur, de mot de passe, et ainsi de suite. Ces chaînes figurent dans la méthode nommée **GetSqlConnectionStringBuilder**.


```
using System;   // C#
using G = System.Collections.Generic;
using D = System.Data;
using C = System.Data.SqlClient;
using X = System.Text;
using H = System.Threading;

namespace RetryAdo2
{
	class Program
	{
		static void Main(string[] args)
		{
			Program program = new Program();
			bool returnBool;

			returnBool = program.Run(args);
			if (returnBool == false)
			{
				Console.WriteLine("Something failed.  :-( ");
			}
			return;
		}

		bool Run(string[] _args)
		{
			C.SqlConnectionStringBuilder sqlConnectionSB;
			C.SqlConnection sqlConnection;
			D.IDbCommand dbCommand;
			D.IDataReader dataReader;
			X.StringBuilder sBuilder = new X.StringBuilder(256);
			int retryIntervalSeconds = 10;
			bool returnBool = false;

			for (int tries = 1; tries <= 5; tries++)
			{
				try
				{
					if (tries > 1)
					{
						H.Thread.Sleep(1000 * retryIntervalSeconds);
						retryIntervalSeconds = Convert.ToInt32(retryIntervalSeconds * 1.5);
					}
					this.GetSqlConnectionStringBuilder(out sqlConnectionSB);

					sqlConnection = new C.SqlConnection(sqlConnectionSB.ToString());

					dbCommand = sqlConnection.CreateCommand();
					dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";

					sqlConnection.Open();
					dataReader = dbCommand.ExecuteReader();

					while (dataReader.Read())
					{
						sBuilder.Length = 0;
						sBuilder.Append(dataReader.GetString(0));
						sBuilder.Append("\t");
						sBuilder.Append(dataReader.GetString(1));

						Console.WriteLine(sBuilder.ToString());
					}
					returnBool = true;
					break;
				}

				catch (C.SqlException sqlExc)
				{
					if (this.m_listTransientErrorNumbers.Contains(sqlExc.Number) == true)
					{ continue; }
					else
					{ throw sqlExc; }
				}
			}
			return returnBool;
		}

		void GetSqlConnectionStringBuilder(out C.SqlConnectionStringBuilder _sqlConnectionSB)
		{
			// Prepare the connection string to Azure SQL Database.
			_sqlConnectionSB = new C.SqlConnectionStringBuilder();

			// Change these values to your values.
			_sqlConnectionSB["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
			_sqlConnectionSB["User ID"] = "MyLogin";  // "@yourservername"  as suffix sometimes.
			_sqlConnectionSB["Password"] = "MyPassword";
			_sqlConnectionSB["Database"] = "MyDatabase";

			// Adjust these values if you like. (.NET 4.5.1 or later.)
			_sqlConnectionSB["ConnectRetryCount"] = 3;
			_sqlConnectionSB["ConnectRetryInterval"] = 10;  // Seconds.

			// Leave these values as they are.
			_sqlConnectionSB["Trusted_Connection"] = false;
			_sqlConnectionSB["Integrated Security"] = false;
			_sqlConnectionSB["Encrypt"] = true;
			_sqlConnectionSB["Connection Timeout"] = 30;
		}

		Program()   // Constructor.
		{
			int[] arrayOfTransientErrorNumbers =
				{ 4060, 40197, 40501, 40613, 49918, 49919, 49920
					//,11001   // 11001 for testing, pretend network error is transient.
				};
			m_listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
		}

		private G.List<int> m_listTransientErrorNumbers;
	}
}
```


### Exécuter le programme


L’exécutable **RetryAdo2.exe** n’entre aucun paramètre. Pour exécuter le fichier .exe dans Visual Studio


1. Définissez un point d’arrêt sur l’instruction **return;** dans la méthode **Main**.

2. Cliquez sur la flèche verte de démarrage. Une fenêtre de console s’affiche.

3. Lorsque le débogueur s’arrête à la fin de **Main**, basculez vers la fenêtre de console.

4. Observez les trois lignes semblables aux suivantes :


```
database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535
```


## Tester votre logique de nouvelle tentative


Voici un moyen pratique de tester la logique de nouvelle tentative :


1. Ajoutez temporairement **11001** à votre collection de valeurs **SqlConnection.Number** qui seront considérées comme des erreurs temporaires.

2. Recompilez votre programme.

3. Déconnectez votre ordinateur client du réseau.

4. Exécutez votre programme dans un débogueur, avec un point d’arrêt dans la boucle.
 - La première boucle échoue avec l’erreur 11001.

5. Quand le programme est placé sur un point d’arrêt pendant la seconde boucle, reconnectez votre ordinateur au réseau.

6. Reprenez l’exécution de votre programme. Elle aboutit durant la deuxième boucle.


### Autre option de test


Une alternative consiste à ajouter une logique à votre programme pour reconnaître une valeur de paramètre de ligne de commande « test ». En réponse au paramètre, le programme effectuerait les opérations suivantes :


1. Il ajouterait temporairement des lettres indésirables pour mal orthographier le nom du serveur de bases de données SQL.

2. Ajoutez temporairement **40615** à la liste des erreurs temporaires.

3. Au début de sa deuxième boucle, ce qui signifie sa première boucle de nouvelle tentative, le programme effectuerait les opérations suivantes :
 - Il annulerait la faute d’orthographe dans le nom de serveur.
 - Il supprimerait 40615 de la liste temporaire.


Il exécuterait le programme avec le paramètre « test » et vérifierait que le programme échoue d’abord, mais réussit lors de la deuxième boucle.


## Liens connexes


- [Exemples de code de démarrage rapide client pour SQL Database](sql-database-develop-quick-start-client-code-samples.md)

- [Essayer la Base de données SQL : Utiliser C# pour créer une Base de données SQL avec la bibliothèque de base de données SQL pour .NET](sql-database-get-started-csharp.md)

<!---HONumber=AcomDC_0316_2016-->