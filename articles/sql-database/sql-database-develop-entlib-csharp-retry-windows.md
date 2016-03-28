<properties
	pageTitle="Logique de nouvelle tentative EntLib pour se connecter à SQL Database | Microsoft Azure"
	description="Enterprise Library est conçu pour faciliter un grand nombre de tâches pour les programmes clients qui interagissent avec les services cloud, y compris l’ajout d’une logique de nouvelle tentative pour les erreurs temporaires."
	services="sql-database"
	documentationCenter=""
	authors="annemill"
	manager="jhubbard"
	editor="" />


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/15/2016"
	ms.author="annemill"/>


# Exemple de code : logique C&#x23; de nouvelle tentative Enterprise Library 6 pour se connecter à SQL Database

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Cette rubrique présente un exemple de code complet illustrant la bibliothèque EntLib (Enterprise Library). EntLib facilite un grand nombre de tâches pour les programmes clients qui interagissent avec les services cloud comme Azure SQL Database. Notre exemple porte sur une tâche primordiale qui est l’ajout d’une logique de nouvelle tentative pour les erreurs temporaires.


Les classes EntLib sont conçues pour différencier deux catégories d’erreurs d’exécution :

- Les erreurs qui ne peuvent pas se corriger d’elles-mêmes, par exemple un nom de serveur mal orthographié.
- Les erreurs temporaires, par exemple quand le serveur interrompt pendant quelques secondes son acceptation de nouvelles connexions, au cours de l’équilibrage de la charge système Azure.


Enterprise Library 6 (EntLib60) est la dernière version, disponible depuis avril 2013.

- Microsoft a publié le code source pour le grand public.
- Microsoft ne prévoit pas de maintenance supplémentaire pour le code source.


## Composants requis


#### .NET Framework 4.0 ou une version ultérieure


Microsoft .NET Framework 4.0 ou une version ultérieure doit être installé. Nous recommandons d’utiliser la version 4.6, qui est la dernière version disponible au moment de la rédaction de ce document.


#### Visual Studio Community (édition gratuite)


Vous avez besoin d’un moyen pour compiler le code source à partir de cet exemple. Comme solution, vous pouvez installer l’[édition gratuite Microsoft Visual Studio *Community*](http://www.visualstudio.com/products/free-developer-offers-vs.aspx).


Vous devrez peut-être enregistrer votre adresse de messagerie avec MSDN. La procédure est similaire à celle-ci :


1. [Accédez à MSDN](http://msdn.microsoft.com/).
2. Cliquez sur **Abonnements MSDN** dans la partie supérieure.
3. Cliquez sur **S’inscrire maintenant**.
4. Renseignez le formulaire.
5. Cliquez sur **Créer un compte** en bas de la page.


#### Enterprise Library 6 (EntLib60)


Vous pouvez installer EntLib60 de deux manières :


- Utilisez la fonctionnalité *NuGet* du gestionnaire de package dans Visual Studio :
 - Dans NuGet, recherchez **enterpriselibrary**.


- Dans la [rubrique principale consacrée à EntLib60](http://msdn.microsoft.com/library/dn169621.aspx), recherchez la ligne **Téléchargements**, puis cliquez sur [Microsoft Enterprise Library 6](http://go.microsoft.com/fwlink/?linkid=290898) pour télécharger les fichiers d’assembly .DLL binaires.


EntLib60 possède plusieurs fichiers d’assembly .DLL dont les noms commencent par le même préfixe **Microsoft.Practices.EnterpriseLibrary.&#x2a;.dll**, mais cet exemple de code concerne uniquement les deux assemblys suivants :

- Microsoft.Practices.EnterpriseLibrary.**TransientFaultHandling**.dll
- Microsoft.Practices.EnterpriseLibrary.**TransientFaultHandling.Data**.dll


## Combinaison de classes EntLib


Vous pouvez créer des classes EntLib à partir d’autres classes EntLib. Dans cet exemple de code, la séquence de création et d’utilisation est la suivante :


1. Créez un objet **ExponentialBackoff**.
2. Créez un objet **SqlDatabaseTransientErrorDetectionStrategy**.
3. Créez un objet **RetryPolicy**. Paramètres d’entrée :
 - Objet **ExponentialBackoff**.
 - Objet **SqlDatabaseTransientErrorDetectionStrategy**.
4. Créez un objet **ReliableSqlConnection**. Paramètres d’entrée :
 - Objet **String** contenant le nom du serveur et les autres informations de connexion.
 - Objet **RetryPolicy**.
5. Appelez la méthode **RetryPolicy .ExecuteAction** pour établir la connexion.
6. Appelez la méthode **ReliableSqlConnection .CreateCommand**.
 - Renvoie un objet **System.SqlClient.Data.DbCommand**, intégré à ADO.NET.
7. Appelez la méthode **RetryPolicy .ExecuteAction** pour effectuer une requête.


## Compilation et exécution de l’exemple de code


L’exemple de code source Program.cs est fourni plus loin dans cette rubrique. Pour compiler et exécuter l’exemple, procédez comme suit :


1. Dans Visual Studio, créez un projet pour un modèle d’application console C#.

2. Dans le volet Explorateur de solutions, modifiez le fichier Program.cs presque vide en remplaçant son contenu initial par le code Program.cs fourni plus loin dans cette rubrique.

3. Utilisez le menu Générer > Générer la solution dans Visual Studio pour compiler votre projet.

4. Dans une fenêtre de commande cmd.exe, exécutez le programme comme indiqué ci-après. La sortie réelle d’une exécution est également affichée :


```
[C:\MyVS\EntLib60Retry\EntLib60Retry\bin\Debug]
>> EntLib60Retry.exe

database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535

[C:\MyVS\EntLib60Retry\EntLib60Retry\bin\Debug]
>>
```


&nbsp;


## Code source Program.cs


Tout le code source pour cet exemple EntLib est fourni dans le fichier Program.cs ci-dessous.


```
using     System;   // C#
using G = System.Collections.Generic;
using D = System.Data;
using C = System.Data.SqlClient;
using X = System.Text;
using H = System.Threading;
using Y = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;

namespace EntLib60Retry
{
   class Program
   {
      static void Main(string[] args)
      {
         Program program = new Program();

         if (program.Run(args) == false)
         {
            Console.WriteLine("Something was unable to complete.  :-( ");
         }
      }

      bool Run(string[] _args)
      {
         int retryIntervalSeconds = 10;
         bool returnBool = false;

         this.InitializeEntLibObjects();

         for (int tries = 1; tries <= 3; tries++)
         {
            if (tries > 1)
            {
               H.Thread.Sleep(1000 * retryIntervalSeconds);
               retryIntervalSeconds = Convert.ToInt32(retryIntervalSeconds * 1.5);
            }

            try
            {
               this.reliableSqlConnection = new Y.ReliableSqlConnection(
                  this.sqlConnectionSB.ToString(),
                  this.retryPolicy, null
                  );
               this.retryPolicy.ExecuteAction(this.OpenTheConnection_action);  // Open the connection.

               this.dbCommand = this.reliableSqlConnection.CreateCommand();
               this.dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";

               // We retry connection .Open after transient faults, but
               // we do not retry commands that use the connection.
               this.IssueTheQuery();  // Run the query, loop through results.

               returnBool = true;
               break;
            }

            catch (C.SqlException sqlExc)
            {
               if (false == this.sqlDatabaseTransientErrorDetectionStrategy.IsTransient(sqlExc))
               {
                  throw sqlExc;
               }
            }
         }
         return returnBool;
      }

      void OpenTheConnection()  // Called by .ExecuteAction.
      {
         this.reliableSqlConnection.Open();
      }

      void IssueTheQuery()      // Called by .ExecuteAction.
      {
         X.StringBuilder sBuilder = new X.StringBuilder(256);

         this.dataReader = this.dbCommand.ExecuteReader();

         while (this.dataReader.Read())
         {
            sBuilder.Length = 0;
            sBuilder.Append(this.dataReader.GetString(0));
            sBuilder.Append("\t");
            sBuilder.Append(this.dataReader.GetString(1));

            Console.WriteLine(sBuilder.ToString());
         }
      }

      void InitializeEntLibObjects()
      {
         this.InitializeSqlConnectionStringBuilder();

         this.exponentialBackoff = new Y.ExponentialBackoff(
            "exponentialBackoff",
             3,                    // Maximum number of times to retry, then let exception bubble up.
             TimeSpan.FromMilliseconds(10000), // Minimum interval between retries.
             TimeSpan.FromMilliseconds(30000), // Maximum interval between retries.
             TimeSpan.FromMilliseconds( 2000)  // For random differences in interval between retries.
            );
         this.sqlDatabaseTransientErrorDetectionStrategy =
            new Y.SqlDatabaseTransientErrorDetectionStrategy();
         this.retryPolicy = new Y.RetryPolicy(
               this.sqlDatabaseTransientErrorDetectionStrategy,
               this.exponentialBackoff
               );
         this.OpenTheConnection_action = delegate() { this.OpenTheConnection(); };
      }

      void InitializeSqlConnectionStringBuilder()
      {
         // Prepare the connection string to Azure SQL Database.
         this.sqlConnectionSB = new C.SqlConnectionStringBuilder();

         // Change these values to your values.
         this.sqlConnectionSB["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
         this.sqlConnectionSB["User ID"] = "MyLogin";  // "@yourservername"  as suffix sometimes.
         this.sqlConnectionSB["Password"] = "MyPassword";
         this.sqlConnectionSB["Database"] = "MyDatabase";

         // Leave these values as they are.
         this.sqlConnectionSB["Trusted_Connection"] = false;
         this.sqlConnectionSB["Integrated Security"] = false;
         this.sqlConnectionSB["Encrypt"] = true;
         this.sqlConnectionSB["Connection Timeout"] = 30;
      }

      Program()   // Constructor.
      {
         int[] arrayOfTransientErrorNumbers =
                { 4060, 10928, 10929, 40197, 40501, 40613, 49918, 49919, 49920
                    //,11001   // 11001 for testing, pretend network error is transient.
                    //,18456   // 18456 for testing, purposely misspell database name.
                };
         listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
      }

      // Fields.
      private G.List<int> listTransientErrorNumbers;

      private Y.ReliableSqlConnection reliableSqlConnection;
      private Y.ExponentialBackoff exponentialBackoff;
      private Y.SqlDatabaseTransientErrorDetectionStrategy
                   sqlDatabaseTransientErrorDetectionStrategy;
      private Y.RetryPolicy retryPolicy;

      private Action OpenTheConnection_action;

      private C.SqlConnectionStringBuilder sqlConnectionSB;
      private D.IDbCommand dbCommand;
      private D.IDataReader dataReader;
   }
}
```


&nbsp;


## Liens connexes


- De nombreux liens pour obtenir plus d’informations sont proposés dans la rubrique [Enterprise Library 6 – Avril 2013](http://msdn.microsoft.com/library/dn169621.aspx).
 - Pour voir le code source, cliquez sur le bouton en haut de cette rubrique pour [télécharger le code source EntLib60](http://go.microsoft.com/fwlink/p/?LinkID=290898).


- Livre électronique gratuit au format .PDF de Microsoft : [Guide du développeur Microsoft Enterprise Library, deuxième édition](http://www.microsoft.com/download/details.aspx?id=41145).


- [Espace de noms Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling](http://msdn.microsoft.com/library/microsoft.practices.enterpriselibrary.transientfaulthandling.aspx)


- [Informations de référence sur la bibliothèque de classes Enterprise Library 6](http://msdn.microsoft.com/library/dn170426.aspx)


- [Exemple de code : logique C# de reconnexion à SQL Database avec ADO.NET](sql-database-develop-csharp-retry-windows.md)


- [Exemples de code de démarrage rapide client pour SQL Database](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=AcomDC_0316_2016-->