<properties 
	pageTitle="Exemple de code : logique C# depuis Enterprise Library de reconnexion à SQL Database | Microsoft Azure"
	description="Enterprise Library est conçu pour faciliter la tâche consistant à inclure une logique de nouvelle tentative dans vos programmes clients qui accèdent aux services cloud."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor="" />


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="genemi"/>


# Exemple de code : logique C&#x23; depuis Enterprise Library de reconnexion à SQL Database


Cette rubrique présente un exemple de code complet illustrant la bibliothèque EntLib (Enterprise Library). EntLib permet au programmeur d’inclure facilement une logique de nouvelle tentative dans le client qui interagit avec le service cloud SQL Database de Microsoft Azure.


Enterprise Library 6 (EntLib60) est la dernière version.


Les liens suivants sont recommandés pour obtenir des informations détaillées sur EntLib :


- [Enterprise Library 6 – avril 2013](http://msdn.microsoft.com/library/dn169621.aspx)<br/>Fournit de nombreux liens vers davantage d’informations.

- Livre électronique gratuit au format .PDF de Microsoft :<br/>[Guide du développeur Microsoft Enterprise Library, 2e édition](http://www.microsoft.com/download/details.aspx?id=41145).


## Composants requis


EntLib n’est pas inclus quand vous installez Visual Studio et Microsoft .NET Framework. Vous devez effectuer une action de téléchargement distincte.


Le système **NuGet** via Visual Studio facilite le téléchargement. Le téléchargement crée un répertoire nommé packages\\ sous le répertoire qui abrite votre fichier .sln de solution Visual Studio.


Les fichiers .dll d’assembly EntLib sont installés dans un sous-répertoire. Les deux fichiers d’assembly sont nommés comme suit :


- `Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.dll`
- `Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.Data.dll`


## Description des fichiers de code C&#x23;


L’exemple de code C# se compose de trois fichiers .cs dont le contenu est collé dans les sections suivantes. Leurs noms de fichier sont les suivants :


- `Program.cs`
- `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`
- `ForcePersistent_SqlCommandTransientErrorDetectionStrategy.cs`


### Version plus courte de `Program.cs` sans les blocs `try/catch`


Un autre fichier .cs fourni est une version beaucoup plus courte de `Program.cs`. Tout le code `try/catch` a été supprimé. Vous pouvez ainsi mieux voir les appels EntLib.



### Fichier .cs de test


Tester votre logique de nouvelle tentative est délicat, car il n’existe aucun moyen évident de provoquer une erreur transitoire authentique. Une solution de test consiste à utiliser un code temporaire pour effectuer les opérations suivantes :


1. Provoquer une erreur transitoire fictive.
2. Corriger la cause de l’erreur transitoire.
3. Réessayer la connexion ou la requête, et vérifier que l’opération réussit.


Pour vous aider à effectuer le test, un autre fichier .cs est fourni, que vous pouvez utiliser en guise de remplacement temporaire :


- `Test2_TransientErrorDetectionStrategy`
 - Remplacement temporaire pour `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`.


Les emplacements de code auxquels vous devez apporter des modifications pour ce test sont balisés avec un commentaire qui contient la chaîne `TEST.PASSWORD_FIX`.


## Compilation et exécution de l’exemple de code


Vous pouvez compiler l'exemple en effectuant les étapes suivantes :


1. Dans Visual Studio, créez un projet pour un modèle d’application console C#.

2. Cliquez avec le bouton droit sur votre projet, puis ajoutez les fichiers .cs pour lesquels le code source est fourni dans cette rubrique.

3. Dans une fenêtre de commande `cmd.exe`, exécutez le programme comme indiqué ci-après. La sortie réelle d’une exécution est également affichée :


		[C:\MyVS\ConsoleApplication1\ConsoleApplication1\bin\Debug]
		>> ConsoleApplication1.exe
		
		database_firewall_rules_table   245575913
		filestream_tombstone_2073058421 2073058421
		filetable_updates_2105058535    2105058535
		
		[C:\MyVS\ConsoleApplication1\ConsoleApplication1\bin\Debug]
		>>


Le code source C# des fichiers .cs figurent dans les sections suivantes.


## Fichier Program.cs


Le fichier de code suivant, Program.cs, est long, car il contient des blocs `try/catch` qui ne s’exécutent que si une erreur se produit. Vers la fin de la présente rubrique, vous trouverez une version beaucoup plus courte de `Program.cs`, dépourvue de toutes les lignes `try/catch`.


La méthode `Main` se trouve dans `Program.cs`. La pile d'appels s'exécute comme suit :


1. `Main` appelle `ConnectAndQuery`.

2. `ConnectAndQuery` appelle `InitializeFields`.

3. `ConnectAndQuery` appelle `EstablishConnection`.

4. `EstablishConnection` appelle `IssueQueryCommand`.


&nbsp;


	using     System;  // C#
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1 
	{
	    /// <summary>
	    /// Demo sample C# program that uses Microsoft's Enterprise Library 6 to robustly
	    /// connect to and interact with Azure SQL Database.
	    /// .
	    /// The sample chooses a strategy of discarding the Sql Connection whenever
	    /// a Sql Command fails, even for a transient reason. The 'for' loop
	    /// manages this choice. We are neither recommending nor disrecommending
	    /// this particular choice, just demonstrating flexibility.
	    /// .
	    /// For an optional test mechanism manually built into this program, find all:
	    /// 'TEST.PASSWORD_FIX' (related to connection).
	    /// </summary>
	    class Program
	    {
	        // const fields, so that SqlException objects thrown from Sql Commands
	        // are tested for transience by our custom logic.
	        private const string M_progressCreatingConnection = "CreatingConnection";
	        private const string M_progressIssuingCommand     = "IssuingCommand";
	
	        // Fields, shared among methods.
	        private string progressLocation;
	        private E.ReliableSqlConnection rsConnection;
	        private E.RetryPolicy connectionRetryPolicy;
	        private E.RetryPolicy commandRetryPolicy;
	        private C.SqlConnectionStringBuilder scsBuilder;
	        private Action EstablishConnection_action;
	        private Action IssueQueryCommand_action;
	        private string sqlSelectCode;
	        private ForcePersistent_SqlCommandTransientErrorDetectionStrategy
	            forcePersistent_SqlCommandTransientErrorDetectionStrategy;
	
	        // Also consider E.FixedInterval or E.Incremental.
	        private E.ExponentialBackoff exponentialBackoffStrategy;
	
	        // TEST.PASSWORD_FIX.  Replace with Test2_TransientErrorDetectionStrategy.
	        private Custom_SqlDatabaseTransientErrorDetectionStrategy
	            custom_SqlDatabaseTransientErrorDetectionStrategy;
	
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	
	        /// <summary>
	        /// Called from Main.
	        /// Calls a method to establish a connection, which calls
	        /// another method to issue a query through the connection. 
	        /// </summary>
	        void ConnectAndQuery()
	        {
	            int maxCountTriesConnectAndQuery = 3;  // Adjustable.
	
	            this.InitializeFields();
	
	            //-------------------------------------------------------
	            // Preparations are complete.
	            // Next is a call .ExecuteAction to invoke the method EstablishConnection.
	            // Method EstablishConnection then calls another .ExecuteAction to
	            //     invoke the method IssueQueryCommand.
	            // Then control is returned to this method.
	
	            for (int cc = 1; cc <= maxCountTriesConnectAndQuery; cc++)
	            {
	                if (cc >= 2) { Console.WriteLine("-- Another iteration of outer loop, cc=={0}. --", cc); }
	
	                try
	                {
	                    // [A.1] Connect, which proceeds to issue a query command.
	                    this.connectionRetryPolicy.ExecuteAction(this.EstablishConnection_action);
	
	                    break; // [A.2] All has gone well, so let the program end.
	                }
	                catch (C.SqlException sqlExc)
	                {
	                    bool isTransientErrorNumber = false;
	
	                    // [A.3] Call our collection of transient error numbers.
	                    isTransientErrorNumber =
	                        Custom_SqlDatabaseTransientErrorDetectionStrategy // TEST.PASSWORD_FIX.
	                        .IsTransientStatic(sqlExc);
	
	                    if (isTransientErrorNumber == false)
	                    {
	                        Console.WriteLine();
	                        Console.WriteLine("Persistent error suffered, SqlException.Number = {0}."
	                            + "  Will terminate.", sqlExc.Number);
	                        Console.WriteLine(sqlExc.ToString());
	
	                        // [A.4] Either the connection attempt or the query command attempt
	                        //     suffered a persistent SqlException.
	                        // Break the query command loop, let the hopeless program end.
	                        break;
	                    }
	
	                    if (this.progressLocation == M_progressCreatingConnection)
	                    {
	                        // [A.5] The EntLib retry mechanisms already gave the Sql Connection
	                        //     enough retries, so this block will not give connection any more tries.
	                        // This block gives retries to certain Sql Command only.
	                        break;
	                    }
	
	                    // [A.6] The SqlException identified a transient error from an attempt
	                    //     to issue a query command.
	                    // So let this method reloop and try again. However, we recommend that the new
	                    // query attempt should start at the beginning and establish a new connection.
	                    Console.WriteLine();
	                    Console.WriteLine("Transient error encountered.  SqlException.Number = {0}."
	                        + "  Program might retry by itself.", sqlExc.Number);
	                    Console.WriteLine("{0} = Attempts so far. Might retry.", cc);
	                    Console.WriteLine(sqlExc.Message);
	                }
	                catch (Exception exc)
	                {
	                    Console.WriteLine();
	                    Console.WriteLine("Unexpected exception type caught.  Will terminate.");
	                    throw exc; // [A.7] The program must end, so re-throw the unrecognized exception.
	                }
	
	
	                // [A.8] Throw an exception if transient SqlExceptions caused us
	                // to exceed our self-imposed maximum of retries.
	                if (cc > maxCountTriesConnectAndQuery)
	                {
	                    Console.WriteLine();
	                    string mesg = String.Format(
	                        "Transient errors suffered in too many retries ({0}). Will terminate.",
	                        cc - 1);
	                    Console.WriteLine(mesg);
	
	                    // [A.9] To end the program, throw a new exception of a different type.
	                    ApplicationException appExc = new ApplicationException(mesg);
	                    throw appExc;
	                }
	            } // for cc
	            return;
	        } // method ConnectAndQuery
	
	
	        void EstablishConnection()
	        {
	            try
	            {
	                // [C.1]
	                using ( this.rsConnection = new E.ReliableSqlConnection(
	                    this.scsBuilder.ToString(),
	                    this.connectionRetryPolicy,
	                    this.commandRetryPolicy )
	                    )
	                {
	                    this.progressLocation = M_progressCreatingConnection;
	                    this.rsConnection.Open(); // [C.2] Open the newly constructed reliable connection.
	
	                    // [C.3] Prepare delegate, then issue a query command.
	                    this.commandRetryPolicy.ExecuteAction(this.IssueQueryCommand_action);
	                }
	            }
	            // TEST.PASSWORD_FIX.
	                // Step_1: In InitializeFields(), set password to a wrong value.
	                // Step_2: Uncomment the following 'catch' of sqlExc.
	            //catch (C.SqlException sqlExc)  // For TESTING only.  Usually comment out this one CATCH.
	            //{
	            //    if (sqlExc.Number == 18456)  // Could mean wrong password, could not connect.
	            //    {
	            //        this.scsBuilder["Password"] = "MyCorrectPassword";
	            //        Console.WriteLine("Testing.  Password now fixed in catch.");
	            //    }
	            //    throw sqlExc;
	            //}
	            catch (Exception exc)
	            {
	                throw exc;  // [C.4] Let caller assess any exception, SqlException or any kind.
	            }
	            return;
	        } // method EstablishConnection
	
	
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	
	            X.StringBuilder sBuilder = new X.StringBuilder(256);
	
	            try
	            {
	                // [D.1] Use the connection to create a query command.
	                using (dbCommand = this.rsConnection.CreateCommand())
	                {
	                    dbCommand.CommandText = this.sqlSelectCode;
	
	                    // [D.2] Issue the query command through the connection.
	                    this.progressLocation = M_progressIssuingCommand;
	                    using (dReader = this.rsConnection.ExecuteCommand<D.IDataReader>(dbCommand))
	                    {
	                        Console.WriteLine();
	                        // [D.3] Loop through all returned rows, writing the data to the console.
	                        while (dReader.Read())
	                        {
	                            sBuilder.Length = 0;
	                            sBuilder.Append(dReader.GetString(0));
	                            sBuilder.Append("\t");
	                            sBuilder.Append(dReader.GetString(1));
	
	                            Console.WriteLine(sBuilder.ToString());
	                        }
	                    }
	                }
	            }
	            catch (Exception exc)
	            {
	                throw exc; // [D.4] Let caller assess any exception.
	            }
	            return;
	        } // method IssueQueryCommand
	
	
	        void InitializeFields()
	        {
	            // [B.1] Prepare the connection string to Azure SQL Database.
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";  // @yourservername suffix sometimes.
	            this.scsBuilder["Password"] = "MyCorrectPassword";  // TEST.PASSWORD_FIX.
	            this.scsBuilder["Database"] = "MyDatabase";
	            // Leave these values as they are.
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = 30; // Default is 15 seconds, too brief over Internet!
	
	            // [B.2] Prepare the Transact-SQL select statement.
	            this.sqlSelectCode =
	                @"SELECT TOP 3 name, CAST(object_id as nvarchar(32)) as [object_id]
	                  FROM sys.objects
	                  WHERE type='IT'
	                  ORDER BY name;";
	
	            // [B.3] Begin steps to create a retry policy for connecting.
	            this.exponentialBackoffStrategy = new E.ExponentialBackoff(
	                "exponentialBackoffStrategy",
	                2,                               // Maximum number of times to retry.
	                TimeSpan.FromMilliseconds(2000), // Minimum interval between retries.
	                TimeSpan.FromMilliseconds(8000), // Maximum interval between retries.
	                TimeSpan.FromMilliseconds(2000)  // Factor for random differences in interval between retries.
	                );
	
	            this.forcePersistent_SqlCommandTransientErrorDetectionStrategy =
	                new ForcePersistent_SqlCommandTransientErrorDetectionStrategy();
	
	            this.custom_SqlDatabaseTransientErrorDetectionStrategy =
	                new Custom_SqlDatabaseTransientErrorDetectionStrategy();// TEST.PASSWORD_FIX.
	
	            this.connectionRetryPolicy = new E.RetryPolicy(
	                this.custom_SqlDatabaseTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	            this.commandRetryPolicy = new E.RetryPolicy(
	                this.forcePersistent_SqlCommandTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	
	            this.EstablishConnection_action = delegate() { this.EstablishConnection(); };
	            this.IssueQueryCommand_action   = delegate() { this.IssueQueryCommand(); };
	
	            return;
	        } // method InitializeFields
	    } // class Program
	}


&nbsp;


## Fichier `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`


EntLib60 a une classe nommée `ReliableSqlConnection`. Vous pouvez contrôler la manière dont l’instance de connexion décide si une exception est une erreur transitoire en lui assignant une classe qui implémente `ITransientErrorDetectionStrategy`.

EntLib60 fournit la classe `SqlDatabaseTransientErrorDetectionStrategy`. Mais, dans cette rubrique, nous avons choisi d’implémenter et d’utiliser notre propre classe personnalisée `Custom_SqlDatabaseTransientErrorDetectionStrategy`. Notre classe personnalisée possède une liste approuvée de valeurs à comparer à `SqlException.Number`.


	using     System;
	using G = System.Collections.Generic;
	using C = System.Data.SqlClient;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// A custom alternative to class SqlDatabaeTransientErrorDetectionStrategy.
	    /// </summary>
	    public class Custom_SqlDatabaseTransientErrorDetectionStrategy
	        : E.ITransientErrorDetectionStrategy
	    {
	        static private G.List<int> M_listTransientErrorNumbers;
	
	
	        /// <summary>
	        /// This method is required by ITransientErrorDetectionStrategy.
	        /// </summary>
	        public bool IsTransient(Exception exc)
	        {
	            return IsTransientStatic(exc);
	        }
	
	
	        /// <summary>
	        /// For general use beyond formal Enterprise Library classes.
	        /// </summary>
	        static public bool IsTransientStatic(Exception exc)
	        {
	            bool returnBool = false;  // Assume is a persistent error.
	            C.SqlException sqlExc;
	
	            if (exc is C.SqlException)
	            {
	                sqlExc = exc as C.SqlException;
	                if (M_listTransientErrorNumbers.Contains(sqlExc.Number) == true)
	                {
	                    returnBool = true;  // Error is transient, not persistent.
	                }
	            }
	            return returnBool;
	        }
	
	
	        /// <summary>
	        /// Lists the SqlException.Number values that are considered
	        /// to indicate transient errors.
	        /// </summary>
	        static Custom_SqlDatabaseTransientErrorDetectionStrategy()
	        {
	            int[] arrayOfTransientErrorNumbers =
	                {4060, 10928, 10929, 40197, 40501, 40613};
	
	            M_listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
	        }
	    } // class Custom_SqlDatabaseTransientErrorDetectionStrategy
	}


&nbsp;


## Fichier `ForcePersistent_SqlCommandTransientErrorDetectionStrategy.cs`


Cette classe personnalisée qui implémente `ITransientErrorDetectionStrategy` applique notre choix de ne jamais permettre à EntLib60 de considérer une erreur de commande de requête comme simplement transitoire. En revanche, l’ensemble du programme est conçu pour évaluer `SqlException.Number` dans sa propre logique personnalisée.


En incluant la classe dans cette section, nous avons fait le choix conceptuel de permettre à notre programme de supprimer une connexion et d’en recréer une autre avant de retenter une commande de requête ayant échoué, que celle-ci soit transitoire ou non.


	using     System;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// Is a substitute for class E.SqlCommandTransientErrorDetectionStrategy.
	    /// Choosing to always discard the Sql Connection whenever the command fails,
	    /// regardless of whether command failure was transient.
	    /// Program try/catch and for-loop logic prefers to discard then re-construct
	    /// a new Sql Connection.
	    /// </summary>
	    public class ForcePersistent_SqlCommandTransientErrorDetectionStrategy
	        : E.ITransientErrorDetectionStrategy
	    {
	        public bool IsTransient(Exception exc)
	        {
	            return false;  // Force a failure, even if is transient.  No retry of sql command.
	        }
	    }
	}


&nbsp;


## Tester votre logique de nouvelle tentative


Il est délicat de tester votre programme EntLib60. Il est difficile de provoquer une erreur véritablement transitoire et de contrôler son minutage. Ce code de démonstration comprend une technique simple, que vous pouvez utiliser pour simuler une erreur transitoire.


La technique consiste à utiliser notre classe `Test2_TransientErrorDetectionStrategy` de détection personnalisée à la place de la classe `Custom_SqlTransientErrorDetectionStrategy`. De cette façon, vous pouvez forcer le paramètre `ReliableSqlConnection` d’EntLib60 à évaluer *toutes* les occurrences de `SqlException` comme étant transitoires. L’astuce consiste à demander au programme de résoudre automatiquement le problème avant la première nouvelle tentative.


### Application de l’astuce dans le cas d’un mot de passe mal orthographié


Une approche simple consiste à partir d’une valeur mal orthographiée pour le mot de passe de connexion. Quand cette situation entraîne une `SqlException` avec `sqlExc.Number==18456`, le programme doit corriger le mot de passe. À la nouvelle tentative, le programme réussit.


Pour implémenter ce test dans notre programme de démonstration, recherchez toutes les occurrences de `TEST.PASSWORD_FIX` dans le code source. Après avoir trouvé toutes les occurrences de `TEST.PASSWORD_FIX` et apporté les modifications qui sont décrites dans les commentaires à chaque emplacement, vous aurez effectué les modifications suivantes :


- Dans le code en direct de la méthode `InitializeFields`, attribuez au mot de passe une valeur incorrecte.

- Dans le code commenté `//`, dans la méthode`EstablishConnection`, il existe un bloc `catch` complet...<br/>`//catch (SDSqlC.SqlException sqlExc)`<br/>qui est commenté. Supprimez les marques de commentaire de l’ensemble du bloc.

- Dans le nouveau bloc `catch` sans marques de commentaire, affectez la valeur de mot de passe correcte.

- Quand la classe `Custom_TransientErrorDetectionStrategy` est référencée dans le code, remplacez la référence par `Test2_TransientErrorDetectionStrategy`. Vous pouvez conserver le même nom de variable.


### Fichier `Test2_TransientErrorDetectionStrategy.cs`


	using     System;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// For testing, is a substitute for class E.SqlDatabaseTransientErrorDetectionStrategy.
	    /// Change the true/false assignment to returnValue for different tests.
	    /// </summary>
	    public class Test2_TransientErrorDetectionStrategy
	        : E.ITransientErrorDetectionStrategy
	    {
	        public bool IsTransient(Exception exc)
	        {
	            bool returnValue = Test2_TransientErrorDetectionStrategy.IsTransientStatic(exc);
	            Console.WriteLine("Inside Test2_TransientErrorDetectionStrategy .IsTransientStatic,"
	                + " returning {0}.", returnValue);
	            return returnValue;
	        }
	        static public bool IsTransientStatic(Exception exc)
	        {
	            return true;  //false;
	        }
	    }
	}


&nbsp;


## Version plus courte de `Program.cs`, sans les blocs `try/catch`


La version raccourcie du code présentée dans cette section peut se compiler et doit pouvoir s’exécuter sans problème. Toutefois, cette version n’est pas destinée à être exécutée dans un environnement de production.


Le seul objectif de cet exemple plus court de `Program.cs` est de faciliter la lecture des appels EntLib qui se trouvent dans le véritable fichier `Program.cs`. La suppression de tous les blocs `try/catch` permet de mieux voir les appels EntLib.


	using     System;  // C#
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	using E = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
	
	namespace ConsoleApplication1_Shorter_No_TryCatch
	{
	    class Program
	    {
	        private E.ReliableSqlConnection rsConnection;
	        private E.RetryPolicy connectionRetryPolicy;
	        private E.RetryPolicy commandRetryPolicy;
	        private E.ExponentialBackoff exponentialBackoffStrategy;
	        private C.SqlConnectionStringBuilder scsBuilder;
	        private Action EstablishConnection_action;
	        private Action IssueQueryCommand_action;
	        private string sqlSelectCode;
	        private Custom_SqlDatabaseTransientErrorDetectionStrategy
	            custom_SqlDatabaseTransientErrorDetectionStrategy;
	        private ForcePersistent_SqlCommandTransientErrorDetectionStrategy
	            forcePersistent_SqlCommandTransientErrorDetectionStrategy;
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	        void ConnectAndQuery()
	        {
	            this.InitializeFields();
	            this.connectionRetryPolicy.ExecuteAction(this.EstablishConnection_action);
	        } // method ConnectAndQuery
	
	        void EstablishConnection()
	        {
	            using ( this.rsConnection = new E.ReliableSqlConnection(
	                this.scsBuilder.ToString(),
	                this.connectionRetryPolicy,
	                this.commandRetryPolicy )
	                    )
	            {
	                this.rsConnection.Open();
	                this.commandRetryPolicy.ExecuteAction(this.IssueQueryCommand_action);
	            }
	        } // method EstablishConnection
	
	
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	            X.StringBuilder sBuilder = new X.StringBuilder(256);
	
	            using (dbCommand = this.rsConnection.CreateCommand())
	            {
	                dbCommand.CommandText = this.sqlSelectCode;
	                using (dReader = this.rsConnection.ExecuteCommand<D.IDataReader>(dbCommand))
	                {
	                    while (dReader.Read())
	                    {
	                        sBuilder.Length = 0;
	                        sBuilder.Append(dReader.GetString(0));
	                        sBuilder.Append("\t");
	                        sBuilder.Append(dReader.GetString(1));
	                        Console.WriteLine(sBuilder.ToString());
	                    }
	                }
	            }
	        } // method IssueQueryCommand
	
	        void InitializeFields()
	        {
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";
	            this.scsBuilder["Password"] = "MyPassword";  // TEST.PASSWORD_FIX.
	            this.scsBuilder["Database"] = "MyDatabase";
	            // Leave these values as they are.
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = 30;
	
	            this.sqlSelectCode =
	                @"SELECT TOP 3 name, CAST(object_id as nvarchar(32)) as [object_id]
	                  FROM sys.objects
	                  WHERE type='IT'
	                  ORDER BY name;";
	
	            this.exponentialBackoffStrategy = new E.ExponentialBackoff(
	                "exponentialBackoffStrategy",
	                2,
	                TimeSpan.FromMilliseconds(2000),
	                TimeSpan.FromMilliseconds(8000),
	                TimeSpan.FromMilliseconds(2000)
	                );
	
	            this.forcePersistent_SqlCommandTransientErrorDetectionStrategy =
	                new ForcePersistent_SqlCommandTransientErrorDetectionStrategy();
	            this.custom_SqlDatabaseTransientErrorDetectionStrategy =
	                new Custom_SqlDatabaseTransientErrorDetectionStrategy();
	
	            this.connectionRetryPolicy = new E.RetryPolicy(
	                this.custom_SqlDatabaseTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	            this.commandRetryPolicy = new E.RetryPolicy(
	                this.forcePersistent_SqlCommandTransientErrorDetectionStrategy,
	                this.exponentialBackoffStrategy);
	
	            this.EstablishConnection_action = delegate() { this.EstablishConnection(); };
	            this.IssueQueryCommand_action   = delegate() { this.IssueQueryCommand(); };
	        } // method InitializeFields
	    } // class Program
	}


## Liens connexes


- [Espace de noms Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling](http://msdn.microsoft.com/library/microsoft.practices.enterpriselibrary.transientfaulthandling.aspx)

- [Bibliothèque de classes Enterprise Library 6](http://msdn.microsoft.com/library/dn170426.aspx)

- [Exemple de code : logique C# de reconnexion à SQL Database avec ADO.NET](sql-database-develop-csharp-retry-windows.md)

- [Exemples de code de démarrage rapide pour SQL Database](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=August15_HO6-->