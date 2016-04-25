<properties
	pageTitle="Logique de nouvelle tentative en C# pour SQL Database | Microsoft Azure"
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
	ms.date="04/08/2016"
	ms.author="annemill"/>


# Exemple de code : Logique de nouvelle tentative C# pour la connexion à une base de données SQL



[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]



Cette rubrique fournit un exemple de code C# qui illustre la logique de nouvelle tentative personnalisée. La logique de nouvelle tentative est conçue pour traiter de manière appropriée les erreurs temporaires ou *défaillances transitoires* qui tendent à disparaître si le programme attend quelques secondes et effectue de nouvelles tentatives.


Les classes ADO.NET pour la connexion à un serveur Microsoft SQL local peuvent également se connecter à Azure SQL Database. Toutefois, par elles-mêmes, les classes ADO.NET classes ne peuvent pas fournir toute la robustesse et la fiabilité nécessaires à la production. Votre programme client peut rencontrer des erreurs temporaires qu’il doit normalement résoudre de manière silencieuse et appropriée.


Voici quelques exemples d’erreurs temporaires :


- Une connexion via Internet est soumise à de courtes coupures de réseau après lesquelles la connexion peut être recréée.
- Le cloud computing implique un équilibrage de charge qui peut bloquer brièvement les tentatives de connexion ou de requête.


## A. Identification des erreurs temporaires


Votre programme doit faire la distinction entre les erreurs temporaires et les erreurs persistantes. Si le programme a mal orthographié le nom de la base de données cible, l'erreur « Aucune base de données trouvée » persistera et se reproduira chaque fois que vous réexécutez le programme.

La liste des erreurs classées comme erreurs temporaires est disponible à l'adresse :

- [Messages d'erreur pour les programmes clients SQL Database](sql-database-develop-error-messages.md#bkmk_connection_errors)
  - Consultez la section principale intitulée *Erreurs temporaires, erreurs de perte de connexion*.


L’exemple de code C# plus loin dans cette rubrique répertorie les numéros d’erreur temporaire dans un champ nommé **TransientErrorNumbers**.



## B. Exemple de code C#


L'exemple de code C# de la présente rubrique contient la logique de détection et de nouvelle tentative personnalisée permettant de gérer les erreurs temporaires. L’exemple part du principe que .NET Framework 4.5.1 (ou une version ultérieure) est installé.


L'exemple de code suit certaines règles élémentaires ou recommandations qui s'appliquent quelle que soit la technologie utilisée pour interagir avec Azure SQL Database. Vous pouvez consulter les recommandations générales ici :


- [Connexion à SQL Database : liens, bonnes pratiques et règles de conception](sql-database-connect-central-recommendations.md)


L’exemple de code C# se compose d’un fichier nommé Program.cs. Le code correspondant est fourni dans la section suivante.


### B.1 Capturer et compiler l’exemple de code


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
using S = System.Data.SqlClient;
using T = System.Threading;
   
namespace RetryAdo2
{
   public class Program
   {
      static public int Main(string[] args)
      {
         bool succeeded = false;
         int totalNumberOfTimesToTry = 4;
         int retryIntervalSeconds = 10;
   
         for (int tries = 1;
            tries <= totalNumberOfTimesToTry;
            tries++)
         {
            try
            {
               if (tries > 1)
               {
                  Console.WriteLine
                     ("Transient error encountered. Will begin attempt number {0} of {1} max...",
                     tries, totalNumberOfTimesToTry
                     );
                  T.Thread.Sleep(1000 * retryIntervalSeconds);
                  retryIntervalSeconds = Convert.ToInt32
                     (retryIntervalSeconds * 1.5);
               }
               AccessDatabase();
               succeeded = true;
               break;
            }
   
            catch (S.SqlException sqlExc)
            {
               if (TransientErrorNumbers.Contains
                  (sqlExc.Number) == true)
               {
                  Console.WriteLine("{0}: transient occurred.", sqlExc.Number);
                  continue;
               }
               else
               {
                  Console.WriteLine(sqlExc);
                  succeeded = false;
                  break;
               }
            }
   
            catch (TestSqlException sqlExc)
            {
               if (TransientErrorNumbers.Contains
                  (sqlExc.Number) == true)
               {
                  Console.WriteLine("{0}: transient occurred. (TESTING.)", sqlExc.Number);
                  continue;
               }
               else
               {
                  Console.WriteLine(sqlExc);
                  succeeded = false;
                  break;
               }
            }
   
            catch (Exception Exc)
            {
               Console.WriteLine(Exc);
               succeeded = false;
               break;
            }
         }
   
         if (succeeded == true)
         {
            return 0;
         }
         else
         {
            Console.WriteLine("ERROR: Unable to access the database!");
            return 1;
         }
      }
   
      /// <summary>
      /// Connects to the database, reads,
      /// prints results to the console.
      /// </summary>
      static public void AccessDatabase()
      {
         //throw new TestSqlException(4060); //(7654321);  // Uncomment for testing.
   
         using (var sqlConnection = new S.SqlConnection
                  (GetSqlConnectionString()))
         {
            using (var dbCommand = sqlConnection.CreateCommand())
            {
               dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";
   
               sqlConnection.Open();
               var dataReader = dbCommand.ExecuteReader();
   
               while (dataReader.Read())
               {
                  Console.WriteLine("{0}\t{1}",
                     dataReader.GetString(0),
                     dataReader.GetString(1));
               }
            }
         }
      }
   
      /// <summary>
      /// You must edit the four 'my' string values.
      /// </summary>
      /// <returns>An ADO.NET connection string.</returns>
      static private string GetSqlConnectionString()
      {
         // Prepare the connection string to Azure SQL Database.
         var sqlConnectionSB = new S.SqlConnectionStringBuilder();
   
         // Change these values to your values.
         sqlConnectionSB.DataSource = "tcp:myazuresqldbserver.database.windows.net,1433"; //["Server"]
         sqlConnectionSB.InitialCatalog = "MyDatabase"; //["Database"]
   
         sqlConnectionSB.UserID = "MyLogin";  // "@yourservername"  as suffix sometimes.
         sqlConnectionSB.Password = "MyPassword";
         sqlConnectionSB.IntegratedSecurity = false;
   
         // Adjust these values if you like. (ADO.NET 4.5.1 or later.)
         sqlConnectionSB.ConnectRetryCount = 3;
         sqlConnectionSB.ConnectRetryInterval = 10;  // Seconds.
   
         // Leave these values as they are.
         sqlConnectionSB.IntegratedSecurity = false;
         sqlConnectionSB.Encrypt = true;
         sqlConnectionSB.ConnectTimeout = 30;
   
         return sqlConnectionSB.ToString();
      }
   
      static public G.List<int> TransientErrorNumbers =
         new G.List<int> { 4060, 40197, 40501, 40613,
         49918, 49919, 49920, 11001 };
   }
   
   /// <summary>
   /// For testing retry logic, you can have method
   /// AccessDatabase start by throwing a new
   /// TestSqlException with a Number that does
   /// or does not match a transient error number
   /// present in TransientErrorNumbers.
   /// </summary>
   internal class TestSqlException : ApplicationException
   {
      internal TestSqlException(int testErrorNumber)
      { this.Number = testErrorNumber; }
   
      internal int Number
      { get; set; }
   }
}
```



## C. Exécuter le programme


L’exécutable **RetryAdo2.exe** n’entre aucun paramètre. Pour exécuter l’exécutable :

1. Ouvrez une fenêtre de console à l’endroit où vous avez compilé le fichier binaire RetryAdo2.exe.
2. Exécutez RetryAdo2.exe, sans paramètres d’entrée.



```
database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535
```



## D. Méthodes pour tester votre logique de nouvelle tentative

Il existe différentes façons de simuler une erreur temporaire pour tester votre logique de nouvelle tentative.


### D.1 Lever une exception de test

L’exemple de code inclut ce qui suit :

- Une petite seconde classe nommée **TestSqlException**, qui a une propriété nommée **Number**.
- `//throw new TestSqlException(4060);`, dont vous pouvez supprimer les marques de commentaire.

Si vous supprimez les marques de commentaire puis que vous recompilez, la prochaine exécution de **RetryAdo2.exe** génère un résultat semblable à ce qui suit.



```
[C:\_MainW\VS15\RetryAdo2\RetryAdo2\bin\Debug]
>> RetryAdo2.exe
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 2 of 4 max...
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 3 of 4 max...
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 4 of 4 max...
4060: transient occurred. (TESTING.)
ERROR: Unable to access the database!

[C:\_MainW\VS15\RetryAdo2\RetryAdo2\bin\Debug]
>>
```



#### Tester à nouveau avec une erreur persistante


Pour prouver que le code gère correctement les erreurs persistantes, exécutez à nouveau le test précédent, sans utiliser de numéro d’erreur temporaire réelle comme 4060. Utilisez plutôt un nombre sans signification particulière tel que 7654321. Le programme doit traiter ceci comme une erreur persistante et doit ignorer toute nouvelle tentative.



### D.2 Se déconnecter du réseau

1. Déconnectez votre ordinateur client du réseau.
  - Pour un ordinateur de bureau, débranchez le câble réseau.
  - Pour un ordinateur portable, appuyez sur la combinaison de touches permettant de désactiver la carte réseau.
2. Démarrez RetryAdo2.exe et attendez que la console affiche la première erreur temporaire, probablement 11001.
3. Reconnectez-vous au réseau pendant que RetryAdo2.exe continue de s’exécuter.
4. Regardez la réussite du rapport de la console sur une nouvelle tentative ultérieure.


### D.3 Mal orthographier temporairement le nom du serveur

1. Ajoutez temporairement 40615 comme autre numéro d’erreur à **TransientErrorNumbers**, puis recompilez.
2. Définissez un point d’arrêt sur la ligne : `new S.SqlConnectionStringBuilder()`.
3. Utilisez la fonctionnalité *Modifier et continuer* pour mal orthographier volontairement le nom du serveur, deux lignes en dessous.
  - Laissez le programme s’exécuter et revenez à votre point d’arrêt.
  - L’erreur 40615 se produit.
4. Corrigez la faute d’orthographe.
5. Laissez le programme s’exécuter et se terminer correctement.
6. Supprimez 40615 et recompilez.


## E. Liens connexes

- [Exemples de code de démarrage rapide client pour SQL Database](sql-database-develop-quick-start-client-code-samples.md)
- [Essayer la Base de données SQL : Utiliser C# pour créer une Base de données SQL avec la bibliothèque de base de données SQL pour .NET](sql-database-get-started-csharp.md)

<!---HONumber=AcomDC_0413_2016-->