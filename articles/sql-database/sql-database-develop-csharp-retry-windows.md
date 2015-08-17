<properties 
	pageTitle="Exemple de code : Logique C# de reconnexion à SQL Database | Microsoft Azure" 
	description="L’exemple C# inclut une logique de nouvelle tentative robuste permettant d'interagir avec la base de données SQL Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="genemi"/>


# Exemple de code : Logique de nouvelle tentative C# pour la connexion à une base de données SQL


<!--
sql-database-develop-csharp-retry-windows.md  ,  NEW
mgm4f20150723retryfrommsdn68
. . . .
Old Titles on MSDN:  Code sample: Retry logic for connecting to Azure SQL Database with ADO.NET
.
How to: Connect to Azure SQL Database by using ADO.NET
.
ShortId on MSDN was:  ee336243.aspx
Dx  4d7936fd-341c-4a37-8796-25e385ae6c5b
-->


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Cette rubrique fournit un exemple de code C# qui illustre la logique de nouvelle tentative personnalisée. La logique de nouvelle tentative est conçue pour traiter de manière appropriée les erreurs *temporaires* qui tendent à disparaître si le programme attend quelques secondes et effectue de nouvelles tentatives.


Les classes ADO.NET que vous utilisez pour vous connecter à un serveur Microsoft SQL local peuvent également se connecter à une base de données SQL Azure. Toutefois, par elles-mêmes, les classes ADO.NET classes ne peuvent pas fournir toute la robustesse et la fiabilité nécessaires à la production. Votre programme client peut rencontrer des erreurs temporaires qu’il devrait normalement résoudre de manière silencieuse et appropriée. Voici quelques exemples d’erreurs temporaires :


- Une connexion via Internet est soumise à de courtes coupures de réseau après lesquelles la connexion peut être recréée.

- Le cloud computing implique un équilibrage de charge qui peut bloquer brièvement les tentatives de connexion ou de requête.


## Identification des erreurs temporaires


Votre programme doit faire la distinction entre les erreurs temporaires et les erreurs persistantes. Si le programme a mal orthographié le nom de la base de données cible, l'erreur « Aucune base de données trouvée » persistera et se reproduira chaque fois que vous réexécutez le programme.


La liste des erreurs classées comme erreurs temporaires est disponible à l'adresse :


- [Messages d'erreur pour les programmes clients SQL Database](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - Consultez la section *Erreurs temporaires, erreurs de perte de connexion*.


## Exemple de code C#


L'exemple de code C# de la présente rubrique contient la logique de détection et de nouvelle tentative personnalisée permettant de gérer les erreurs temporaires.


L'exemple de code suit certaines règles élémentaires ou recommandations qui s'appliquent quelle que soit la technologie utilisée pour interagir avec la base de données SQL Azure. Vous pouvez consulter les recommandations générales ici :


- [Connexion à la base de données SQL : liens, meilleures pratiques et règles de conception](sql-database-connect-central-recommendations.md)


L'exemple de code C# se compose de deux fichiers .cs dont le contenu est collé dans les sections suivantes. Leurs noms de fichier sont les suivants :


- `Program.cs`
- `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`


## Compilation et exécution de l’exemple de code


Vous pouvez compiler l'exemple en effectuant les étapes suivantes :


1. Dans Visual Studio, créez un projet pour un modèle d’application console C#.

2. Cliquez avec le bouton droit sur votre projet, puis ajoutez le fichier .cs pour lequel la source de code est fournie dans cette rubrique.

3. Dans une fenêtre de commande `cmd.exe`, exécutez le programme comme indiqué ci-après. La sortie réelle d’une exécution est également affichée.


		[C:\MyVS\ConsoleApplication1\ConsoleApplication1\bin\Debug]
		>> ConsoleApplication1.exe
		database_firewall_rules_table   245575913
		filestream_tombstone_2073058421 2073058421
		filetable_updates_2105058535    2105058535
		
		[C:\MyVS\ConsoleApplication1\ConsoleApplication1\bin\Debug]
		>>


Le code source C# des fichiers .cs figurent dans les sections suivantes.


## Fichier Program.cs


Le programme de démonstration est conçu de sorte que :


- Une erreur temporaire au cours d’une tentative de connexion entraîne une nouvelle tentative.

- Une erreur temporaire au cours d’une commande de requête oblige le programme à ignorer la connexion et à en créer une nouvelle, avant de réessayer la commande de requête.


Microsoft ne recommande ni ne déconseille ce type de conception. Le programme de démonstration illustre quelques aspects de la flexibilité de conception qui vous sont accessibles.


### Longueur du code source


La durée du fichier `Program.cs` est principalement due à la logique qui détecte les exceptions.


Une [version abrégée](#shorter_program_cs) de ce fichier `Program.cs` est présentée à la fin de cette rubrique, dépourvue de toute logique de nouvelle tentative et gestion `Exception`.


### Pile d’appels


La méthode `Main` se trouve dans `Program.cs`. La pile d'appels s'exécute comme suit :


1. `Main` appelle `ConnectAndQuery`.

2. `ConnectAndQuery` appelle `EstablishConnection`.

3. `EstablishConnection` appelle `IssueQueryCommand`.


### Code source pour Program.cs


	using     System;  // C#, pure ADO.NET, no Enterprise Library.
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	using T = System.Threading;
	
	namespace ConsoleApplication1
	{
	    class Program
	    {
	        // Fields, shared among methods.
	        C.SqlConnection sqlConnection;
	        C.SqlConnectionStringBuilder scsBuilder;
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	        /// <summary>
	        /// Prepares values for a connection. Then inside a loop, it calls a method
	        /// that opens a connection. The called method calls yet another method
	        /// that issues a query.
	        /// The loop reiterates only if a transient error is encountered.
	        /// </summary>
	        void ConnectAndQuery()
	        {
	            int connectionTimeoutSeconds = 30;  // Default of 15 seconds is too short over the Internet, sometimes.
	            int maxCountTriesConnectAndQuery = 3;  // You can adjust the various retry count values.
	            int secondsBetweenRetries = 4;  // Simple retry strategy.
	
	            // [A.1] Prepare the connection string to Azure SQL Database.
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";  // @yourservername suffix sometimes.
	            this.scsBuilder["Password"] = "MyPassword";
	            this.scsBuilder["Database"] = "MyDatabase";
	            // Leave these values as they are.
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = connectionTimeoutSeconds;
	
	            //-------------------------------------------------------
	            // Preparations are complete.
	
	            for (int cc = 1; cc <= maxCountTriesConnectAndQuery; cc++)
	            {
	                try
	                {
	                    // [A.2] Connect, which proceeds to issue a query command.
	                    this.EstablishConnection();
	
	                    // [A.3] All has gone well, so let the program end.
	                    break;
	                }
	                catch (C.SqlException sqlExc)
	                {
	                    bool isTransientError;
	
	                    // [A.4] Check whether sqlExc.Number is on the whitelist of transients.
	                    isTransientError = Custom_SqlDatabaseTransientErrorDetectionStrategy
	                        .IsTransientStatic(sqlExc);
	
	                    if (isTransientError == false)  // Is a persistent error...
	                    {
	                        Console.WriteLine();
	                        Console.WriteLine("Persistent error suffered, SqlException.Number=={0}.  Will terminate.",
	                            sqlExc.Number);
	                        Console.WriteLine(sqlExc.ToString());
	
	                        // [A.5] Either the connection attempt or the query command attempt suffered a persistent SqlException.
	                        // Break the loop, let the hopeless program end.
	                        break;
	                    }
	
	                    // [A.6] The SqlException identified a transient fault from an attempt to issue a query command.
	                    // So let this method reloop and try again. However, we recommend that the new query
	                    // attempt should start at the beginning and establish a new connection.
	                    Console.WriteLine();
	                    Console.WriteLine("Transient error encountered.  SqlException.Number=={0}."
	                        + "  Program might retry by itself.", sqlExc.Number);
	                    Console.WriteLine("{0} = Attempts so far. Might retry.", cc);
	                    Console.WriteLine(sqlExc.Message);
	                }
	                catch (Exception exc)
	                {
	                    Console.WriteLine();
	                    Console.WriteLine("Unexpected exception type caught in Main. Will terminate.");
	
	                    // [A.7] The program must end, so re-throw the unrecognized error.
	                    throw exc;
	                }
	
	                // [A.8] Throw an application exception if transient SqlExceptions caused us
	                // to exceed our self-imposed maximum count of retries.
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
	                // Else, can retry.
	
	                // A very simple retry strategy, a brief pause before looping.
	                T.Thread.Sleep(1000 * secondsBetweenRetries);
	            } // for cc
	            return;
	        } // method ConnectAndQuery
	
	
	        /// <summary>
	        /// Open a connection, then call a method that issues a query.
	        /// </summary>
	        void EstablishConnection()
	        {
	            try
	            {
	                // [B.1] The 'using' statement will .Dispose() the connection.
	                // If you are working with a connection pool, you might want instead
	                // to merely .Close() the connection.
	                using (this.sqlConnection = new C.SqlConnection(this.scsBuilder.ToString()))
	                {
	                    // [B.2] Open a connection.
	                    sqlConnection.Open();
	                    // [B.3]
	                    this.IssueQueryCommand();
	                }
	            }
	            catch (Exception exc)
	            {
	                // [B.4] This re-throw means we discard the connection whenever
	                // any error occurs during query command, even for a transient error.
	                throw exc;  // [B.5] Let caller assess any exception, SqlException or any kind.
	            }
	            return;
	        } // method EstablishConnection
	
	
	        /// <summary>
	        /// Issue a query, then write the result rows to the console.
	        /// </summary>
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	            X.StringBuilder sBuilder = new X.StringBuilder(512);
	
	            try
	            {
	                // [C.1] Use the connection to create a query command.
	                using (dbCommand = this.sqlConnection.CreateCommand())
	                {
	                    dbCommand.CommandText =
	                        @"SELECT TOP 3
	                              ob.name,
	                              CAST(ob.object_id as nvarchar(32)) as [object_id]
	                          FROM sys.objects as ob
	                          WHERE ob.type='IT'
	                          ORDER BY ob.name;";
	
	                    // [C.2] Issue the query command through the connection.
	                    using (dReader = dbCommand.ExecuteReader())
	                    {
	                        // [C.3] Loop through all returned rows, writing the data to the console.
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
	                throw exc; // Let caller assess any exception.
	            }
	            return;
	        } // method IssueQueryCommand
	    } // class Program
	}


## Fichier de code Custom\_SqlDatabaseTransientErrorDetectionStrategy.cs


[`SqlDatabaseTransientErrorDetectionStrategy`](http://msdn.microsoft.com/library/microsoft.practices.enterpriselibrary.transientfaulthandling.sqldatabasetransienterrordetectionstrategy.aspx) est une classe dans l’API Enterprise Library (EntLib). L'exemple de code présent utilise la classe personnalisée suivante qui emprunte l'idée de la classe EntLib.


	using     System;
	using G = System.Collections.Generic;
	using C = System.Data.SqlClient;
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// A custom alternative to class SqlDatabaeTransientErrorDetectionStrategy.
	    /// </summary>
	    public class Custom_SqlDatabaseTransientErrorDetectionStrategy
	    {
	        static private G.List<int> M_listTransientErrorNumbers;
	
	
	        /// <summary>
	        /// This method happens to match ITransientErrorDetectionStrategy of EntLib60.
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
	                {4060, 10928, 10929, 40197, 40501, 40613 };
	
	            M_listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
	        }
	    } // class Custom_SqlDatabaseTransientErrorDetectionStrategy
	}


<a id="shorter_program_cs" name="shorter_program_cs"></a>


&nbsp;


## Version abrégée du fichier Program.cs


Le code source dans cette section est une répétition abrégée du fichier `Program.cs` plus long présenté précédemment. Tout ce qui concerne la logique de nouvelle tentative et la gestion des exceptions a été supprimé.


La version abrégée permet d’afficher facilement les appels ADO.NET, sachant que ceux-ci fonctionnent généralement. Normalement, aucune erreur temporaire ne se produit et aucune n’est renvoyée. Et un parachutiste n'a pas besoin d’un parachute de secours.


	using     System;  // C#, pure ADO.NET, no retry logic, no Exception handling.
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	
	namespace ConsoleApplication1_dn864744
	{
	    class Program
	    {
	        C.SqlConnection sqlConnection;
	        C.SqlConnectionStringBuilder scsBuilder;
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	        void ConnectAndQuery()
	        {
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";
	            this.scsBuilder["Password"] = "MyPassword";
	            this.scsBuilder["Database"] = "MyDatabase";
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = 30;
	
	            this.EstablishConnection();
	        } // method ConnectAndQuery
	
	        void EstablishConnection()
	        {
	            using (this.sqlConnection = new C.SqlConnection(this.scsBuilder.ToString()))
	            {
	                sqlConnection.Open();
	                this.IssueQueryCommand();
	            }
	        } // method EstablishConnection
	
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	            X.StringBuilder sBuilder = new X.StringBuilder(512);
	
	            using (dbCommand = this.sqlConnection.CreateCommand())
	            {
	                dbCommand.CommandText =
	                    @"SELECT TOP 3 ob.name, CAST(ob.object_id as nvarchar(32)) as [object_id]
	                        FROM sys.objects as ob
	                        WHERE ob.type='IT'
	                        ORDER BY ob.name;";
	
	                using (dReader = dbCommand.ExecuteReader())
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
	    } // class Program
	}


## Liens connexes


- [Exemples de code de démarrage rapide pour SQL Database](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=August15_HO6-->