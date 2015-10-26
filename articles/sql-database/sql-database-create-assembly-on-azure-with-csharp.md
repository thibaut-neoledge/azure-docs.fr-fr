<properties 
	pageTitle="Création d’un assembly sur la base de données SQL Azure avec CSharp"
	description="Fournit le code source C# permettant d’émettre une instruction CREATE ASSEMBLY vers la base de données SQL Azure après l’encodage initial d’un fichier DLL dans une chaîne contenant un nombre hexadécimal long." 
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
	ms.date="07/14/2015" 
	ms.author="genemi"/>


# Création d’un assembly sur la base de données SQL Azure avec CSharp


<!--
GeneMi , Latest edit = 2015-March-25  Wednesday  10:22am
Converting plain text "CREATE ASSEMBLY" into a link to the MSDN topic, ms189524.aspx. And ms186755.aspx for "CREATE FUNCTION".
-->


Cette rubrique fournit un exemple de code C# permettant d’émettre une instruction [CREATE ASSEMBLY](http://msdn.microsoft.com/library/ms189524.aspx) vers la base de données SQL Azure. Pour la base de données SQL, la clause FROM ne peut pas accepter le format simple d’un chemin d’accès sur l’ordinateur local qui héberge la base de données. Une solution consiste à encoder d’abord les éléments binaires du fichier DLL de l’assembly dans une longue chaîne contenant un nombre hexadécimal, puis à définir cette chaîne en tant que valeur dans la clause FROM.


### Conditions préalables


Pour comprendre cette rubrique, vous devez posséder des connaissances de base sur les éléments suivants :


- [Fonctions table CLR](http://msdn.microsoft.com/library/ms131103.aspx)<br/>Explique le fonctionnement de l’instruction CREATE ASSEMBLY Transact-SQL avec d’autres instructions pour le serveur Microsoft SQL local.


## A. Technique globale


1. Utilisez les instructions DROP FUNCTION et DROP ASSEMBLY si nécessaire pour nettoyer une exécution précédente.
2. Veillez à vous souvenir de l’emplacement du fichier DLL d’assembly Microsoft .NET Framework compilé à partir de votre propre code. Vous devrez l’indiquer à l’étape suivante. 
3. Exécutez le fichier EXE dont le code source C# est donné dans cette rubrique. Indiquez au fichier EXE où votre fichier DLL se trouve.
 - Votre fichier DLL binaire est encodé dans une longue chaîne contenant un nombre hexadécimal.
 - Une instruction CREATE ASSEMBLY est émise avec la chaîne hexadécimale définie dans la clause FROM.
4. Utilisez l’instruction [CREATE FUNCTION](http://msdn.microsoft.com/library/ms186755.aspx) pour faire référence à une méthode de votre assembly.
5. Utilisez l’instruction T-SQL SELECT pour appeler et tester votre fonction.


La liste précédente ne fait pas mention de...<br/> **execute sp\_configure ’clr enabled’, 1;**<br/> .. .car cela n’est pas nécessaire pour la base de données SQL Azure, même si cela est nécessaire pour Microsoft SQL Server.


Si besoin pour les nouvelles exécutions, le code T-SQL permettant de supprimer la fonction et l’assembly est le suivant :


    DROP FUNCTION fnCompareStringsCaseSensitive;
    DROP ASSEMBLY CreateAssemblyFunctions3;


## B. Fichier DLL d’assembly simple pour la fonction T-SQL à laquelle faire référence


L’exemple de code C# simple donné dans cette section peut être compilé dans un fichier DLL d’assembly.


Cet exemple de code contient la méthode **CompareCaseSensitiveNet**, à laquelle il est fait référence ultérieurement dans une instruction T-SQL CREATE FUNCTION. Notez que la méthode est décorée avec un attribut .NET nommé **SqlFunction**. Une méthode décorée avec cet attribut peut être appelée par votre code T-SQL en tant que fonction.


	using           System;   // C#
	using SDSqTyp = System.Data.SqlTypes;
	using MSqServ = Microsoft.SqlServer.Server;
	
	namespace CreateAssemblyFunctions3
	{
	    public class SqlFunctionMethodsForSprocs
	    {
	        /// <summary> This method is referenced
	        /// by a T-SQL CREATE FUNCTION statement. </summary>
	        [MSqServ.SqlFunction(IsDeterministic = true, IsPrecise = true)]
	        static public SDSqTyp.SqlInt32 CompareCaseSensitiveNet(string strA, string strB)
	        {
	            return String.Compare(strA, strB, false);
	        }
	    }
	}


## C. Exemple de code C&#x23; pour un fichier EXE qui émet une instruction CREATE ASSEMBLY


La séquence suivante est mise en œuvre lorsque vous exécutez le fichier EXE créé à partir de cet exemple de code C# :


1. La ligne de commande d’exécution du fichier EXE appelle la méthode **Main**.
2. La méthode Main appelle la méthode **ObtainHexStringOfAssembly**.
 - Cette méthode génère une chaîne SqlString qui stocke l’assembly sous forme de nombre hexadécimal.
3. La méthode Main appelle la méthode **SubmitCreateAssemblyToAzureSqlDb**.
 - L’entrée principale est la chaîne SqlString.
 - La sortie est un appel CREATE ASSEMBLY qui est envoyé à la base de données SQL Azure.


			using             System;   // C#
			using SDat      = System.Data;
			using SDSClient = System.Data.SqlClient;
			using SGlo      = System.Globalization;
			using SIo       = System.IO;
			using STex      = System.Text;
			
			namespace CreateAssemblyFromHexString6
			{
			    /// <summary>
			    /// Run the Main method on your local computer console, so it can issue a
			    /// a CREATE ASSEMBLY statement to your Azure SQL Database server:
			    /// </summary>
			    class Program
			    {
			        /// <summary>
			        /// Calls the methods in the proper sequence.
			        /// </summary>
			        /// <param name="args">
			        /// Parameters for the cmd.exe command line
			        ///    run of CreateAssemblyFromHexString6.exe:
			        /// args[0] = FullDirPathFileNameOfAssembly.
			        /// args[1] = AssemblyName.
			        ///    For the CREATE ASSEMBLY assemblyName statement.
			        /// args[2] = Azure SQL Database - ServerName, including a suffix like .database.windows.net .
			        /// args[3] = Azure SQL Database - DatabaseName.
			        /// args[4] = Azure SQL Database - LoginName.
			        /// args[5] = Azure SQL Database - Password for login.
			        ///    (Better if from .config file.)
			        /// </param>
			        static int Main(string[] args)
			        {
			            int returnCode = 1; // Only 0 (zero) means Good Success.
			            string
			                fullPathNameAssemblyFile,
			                assemblyName,
			                AzureSqlDbServerName,
			                AzureSqlDbDatabaseName,
			                AzureSqlDbLoginName,
			                AzureSqlDbPassword;
			
			            try
			            {
			                fullPathNameAssemblyFile = args[0];
			                assemblyName             = args[1];
			                AzureSqlDbServerName     = args[2];
			                AzureSqlDbDatabaseName   = args[3];
			                AzureSqlDbLoginName      = args[4];
			                AzureSqlDbPassword       = args[5];
			
			                string hexStringOfAssembly = Program
			                    .ObtainHexStringOfAssembly(fullPathNameAssemblyFile);
			
			                Program.SubmitCreateAssemblyToAzureSqlDb(
			                    hexStringOfAssembly,
			                    assemblyName,
			                    AzureSqlDbServerName,
			                    AzureSqlDbDatabaseName,
			                    AzureSqlDbLoginName,
			                    AzureSqlDbPassword);
			
			                returnCode = 0;
			            }
			            catch (Exception ex)
			            {
			                Console.WriteLine("Bad, Failure.");
			                throw ex;
			            }
			            Console.WriteLine("Good, Success.");
			            return returnCode;
			        }
			
			        /// <summary> Encodes the binary bits of the assembly DLL into a 
			        /// string containing a hexadecimal number. </summary>
			        /// <param name="fullPathToAssembly"
			        /// >Full directory path plus the file name, to the .DLL file.</param>
			        /// <returns>A string containing a hexadecimal number that encodes
			        /// the binary bits of the .DLL file.</returns>
			        static private string ObtainHexStringOfAssembly
			            (string fullPathToAssembly)
			        {
			            STex.StringBuilder sbuilder = new STex.StringBuilder("0x");
			            using (SIo.FileStream fileStream = new SIo.FileStream(
			                fullPathToAssembly,
			                SIo.FileMode.Open, SIo.FileAccess.Read, SIo.FileShare.Read)
			                )
			            {
			                int byteAsInt;
			                while (true)
			                {
			                    byteAsInt = fileStream.ReadByte();
			                    if (-1 >= byteAsInt) { break; }
			                    sbuilder.Append(byteAsInt.ToString
			                        ("X2", SGlo.CultureInfo.InvariantCulture));
			                }
			            }
			            return sbuilder.ToString();
			        }
			
			        /// <summary>
			        /// Sends a Transact-SQL CREATE ASSEMBLY FROM hexString.
			        /// </summary>
			        static private void SubmitCreateAssemblyToAzureSqlDb(
			            string hexStringOfAssembly,
			            string assemblyName,
			            string AzureSqlDbServerName,
			            string AzureSqlDbDatabaseName,
			            string AzureSqlDbLoginName,
			            string AzureSqlDbPassword)
			        {
			            string sqlCreateAssembly = "CREATE ASSEMBLY [" + assemblyName
			                + "] FROM " + hexStringOfAssembly + ";";
			            STex.StringBuilder sbuilderConnection = new STex.StringBuilder();
			
			            sbuilderConnection.Append("Server=tcp:");
			            sbuilderConnection.Append(AzureSqlDbServerName);
			            sbuilderConnection.Append(",1433;");
			
			            sbuilderConnection.Append("Database=");
			            sbuilderConnection.Append(AzureSqlDbDatabaseName);
			            sbuilderConnection.Append(";");
			
			            sbuilderConnection.Append("Trusted_Connection=False;");
			            sbuilderConnection.Append("Connection Timeout=30;");
			
			            sbuilderConnection.Append("User ID=");
			            sbuilderConnection.Append(AzureSqlDbLoginName);
			            sbuilderConnection.Append(";");
			
			            sbuilderConnection.Append("Password=");
			            sbuilderConnection.Append(AzureSqlDbPassword);
			            sbuilderConnection.Append(";");
			
			            using (SDSClient.SqlConnection sqlConnection =
			                new SDSClient.SqlConnection())
			            {
			                SDSClient.SqlCommand sqlCommand;
			                sqlConnection.ConnectionString = sbuilderConnection.ToString();
			                sqlCommand = sqlConnection.CreateCommand();
			                sqlCommand.CommandType = SDat.CommandType.Text;
			                sqlCommand.CommandText = sqlCreateAssembly;
			                sqlConnection.Open();
			                sqlCommand.ExecuteNonQuery();
			            }
			            return;
			        }
			    }
			}


### C.1 Compilation des références et des versions


Pour compiler et tester l’exemple de code pour l’outil EXE, nous avons utilisé les éléments suivants :


- Visual Studio 2013 Update 4
 - Notre type de modèle de projet était l’application console simple.
- .NET Framework 4.5


Notre projet Visual Studio faisait référence aux assemblies suivantes pour la compilation :


- Microsoft.CSharp
- Système
- System.Core
- System.Data
- System.Data.DataSetExtensions
- System.Xml
- System.Xml.Linq


### C.2 Ligne de commande pour l’exécution du fichier EXE


Le bloc de commande suivant affiche un exemple de ligne de commande à entrer pour exécuter le fichier EXE à partir de la console. Les paramètres de la ligne de commande sont encapsulés artificiellement ici pour un meilleur affichage.


	CreateAssemblyFromHexString6.exe
		C:\my\bin\debug\CreateAssemblyFunctions3.dll
		CreateAssemblyFunctions3
		myazuresqldbsvr2.database.windows.net
		myazuresqldbdab4
		myazurelogin
		Mypassword123


Par souci de simplification, dans cet exemple, le mot de passe est transmis en tant que paramètre de ligne de commande. Une meilleure conception consiste à faire en sorte que le code C# obtienne le mot de passe à partir d’un fichier CONFIG.


## D. Exécution d’une instruction CREATE FUNCTION


Une fois l’assembly stocké dans votre serveur de base de données SQL Azure, vous devez exécuter une instruction CREATE FUNCTION Transact-SQL faisant référence à la méthode de l’assembly.


Le bloc de code Transact-SQL suivant inclut quelques instructions SELECT non essentielles afin de démontrer que le système de base de données comporte des enregistrements pour votre assembly et votre fonction. Enfin, il y a une instruction qui appelle la fonction.


	SELECT a11.*, am2.*
		FROM           sys.assemblies       AS a11
		    INNER JOIN sys.assembly_modules AS am2 ON am2.assembly_id = a11.assembly_id
		WHERE a11.name = 'CreateAssemblyFunctions3';
	GO
	
	CREATE FUNCTION fnCompareStringsCaseSensitive
	    (@strA nvarchar(128), @strB nvarchar(128))
	    returns int
	    AS EXTERNAL NAME
	        CreateAssemblyFunctions3
	            .[CreateAssemblyFunctions3.SqlFunctionMethodsForSprocs]
	                .CompareCaseSensitiveNet;
	GO
	SELECT * FROM sys.objects WHERE name = 'fnCompareStringsCaseSensitive';
	
	 -- Use the new function.
	SELECT dbo.fnCompareStringsCaseSensitive('BLUE', 'blue') as returnedValue;
	GO


Le bloc de code Transact-SQL précédent se termine par une instruction SELECT qui appelle la nouvelle fonction. Vous pouvez placer l’instruction SELECT dans une procédure stockée.


<!-- EndOfFile -->

<!---HONumber=Oct15_HO3-->