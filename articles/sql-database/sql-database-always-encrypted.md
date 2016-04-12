<properties
	pageTitle="Chiffrement intégral - Protéger les données sensibles dans Azure SQL Database avec le chiffrement de base de données"
	description="Protéger les données sensibles de votre base de données SQL en quelques minutes."
	keywords="chiffrer les données, chiffrement sql, chiffrement de base de données, données sensibles, chiffrement intégral"	
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="cgronlun"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/02/2016"
	ms.author="sstein"/>

# Chiffrement intégral - Protéger les données sensibles dans la base de données SQL avec le chiffrement de base de données et stocker vos clés de chiffrement dans le magasin de certificats Windows

> [AZURE.SELECTOR]
- [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)
- [Magasin de certificats Windows](sql-database-always-encrypted.md)


Cet article montre comment sécuriser les données sensibles dans une base de données SQL avec le chiffrement de base de données à l’aide de l’[Assistant Chiffrement intégral](https://msdn.microsoft.com/library/mt459280.aspx) dans [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx) et comment stocker vos clés de chiffrement dans le magasin de certificats Windows.

Le chiffrement intégral est une nouvelle technologie de chiffrement de données d’Azure SQL Database et de SQL Server qui protège les données sensibles au repos sur le serveur pendant le déplacement entre le client et le serveur, ainsi que pendant l’utilisation des données, pour s’assurer que les données sensibles ne s’affichent pas en clair dans le système de base de données. Une fois que vous avez chiffré les données, seuls les applications clientes ou les serveurs d’applications ayant accès aux clés peuvent accéder aux données en clair. Pour plus d’informations, consultez [Chiffrement intégral (moteur de base de données)](https://msdn.microsoft.com/library/mt163865.aspx).


Après avoir configuré la base de données pour le chiffrement intégral, nous allons créer une application cliente en C# avec Visual Studio afin de l’utiliser avec les données chiffrées.

Suivez les étapes de cet article et découvrez comment configurer le chiffrement intégral pour une base de données SQL Azure. Dans cet article, vous allez apprendre à effectuer les tâches suivantes :

- Utiliser l’Assistant Chiffrement intégral dans SSMS pour créer des [clés intégralement chiffrées](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3)
    - Créer une [clé principale de colonne (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
    - Créer une [clé de chiffrement de colonne (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
- Créer une table de base de données et chiffrer quelques colonnes.
- Créer une application qui insère, sélectionne et affiche les données des colonnes chiffrées.

> [AZURE.NOTE] Le chiffrement intégral pour base de données SQL Azure est actuellement en version préliminaire.


## Composants requis

Pour ce didacticiel, vous devez disposer des éléments suivants :

- Un compte Azure et un abonnement, avant de commencer. Si vous n’en avez pas, inscrivez-vous pour un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 13.0.700.242 ou version ultérieure.
- [.NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) ou version ultérieure (sur l’ordinateur client).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).



## Créer une base de données SQL vide
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Nouveau** > **Données et stockage** > **Base de données SQL**.
3. Créez une base de données **vide** nommée **Clinique** sur un serveur nouveau ou existant. Pour obtenir des instructions détaillées sur la création d’une base de données dans le portail Azure, consultez [Créer une base de données SQL en quelques minutes](sql-database-get-started.md).

	![créer une base de données vide](./media/sql-database-always-encrypted/create-database.png)

Vous aurez besoin de la chaîne de connexion plus loin dans ce didacticiel. Par conséquent, après avoir créé la base de données, naviguez jusqu’à la nouvelle base de données Clinique et copiez la chaîne de connexion (vous pouvez obtenir la chaîne de connexion à tout moment, mais puisque nous sommes déjà dans le portail, il est très facile de la copier maintenant).

1. Cliquez sur **Bases de données SQL** > **Clinique** > **Afficher les chaînes de connexion de la base de données**.
2. Copiez la chaîne de connexion pour **ADO.NET** :

	![copier la chaîne de connexion](./media/sql-database-always-encrypted/connection-strings.png)


## Connexion à la base de données avec SSMS

Ouvrez SSMS et connectez-vous au serveur avec la base de données Clinique.


1. Ouvrez SSMS (cliquez sur **Connexion** > **Moteur de base de données** pour ouvrir la fenêtre **Connexion au serveur** si ce n’est déjà fait).
2. Entrez le nom du serveur et vos informations d’identification. Le nom du serveur se trouve dans le panneau de la base de données SQL et dans la chaîne de connexion que vous avez copiée précédemment. Saisissez le nom complet du serveur, y compris *database.windows.net*.

	![copier la chaîne de connexion](./media/sql-database-always-encrypted/ssms-connect.png)

3. Si la fenêtre **Nouvelle règle de pare-feu** s'ouvre, connectez-vous à Azure et laissez SSMS créer une règle de pare-feu pour vous.


## Création d’une table

Nous allons d’abord créer une table pour y stocker les données des patients (une table standard pour commencer, nous configurerons le chiffrement à la section suivante).

1. Développez **Bases de données**.
1. Cliquez avec le bouton droit sur la base de données **Clinique** puis cliquez sur **Nouvelle requête**.
2. Collez l’élément Transact-SQL (T-SQL) suivant dans la fenêtre de la nouvelle requête, puis **exécutez-le** :


        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1), 
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL, 
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO


## Chiffrement de quelques colonnes (configuration Chiffrement intégral)

SSMS fournit un Assistant pour configurer facilement le chiffrement intégral en définissant pour vous la clé principale de colonne (CMK), la clé de chiffrement de colonne (CEK) et les colonnes chiffrées.

1. Développez **Bases de données** > **Clinique** > **Tables**.
2. Cliquez avec le bouton droit sur la table **Patients**, puis sélectionnez **Chiffrer les colonnes...** pour ouvrir l'Assistant Chiffrement intégral :

    ![chiffrer des colonnes](./media/sql-database-always-encrypted/encrypt-columns.png)

3. **Sélection de colonnes**

    Cliquez sur **Suivant** sur la page **Introduction** pour ouvrir la page **Sélection de colonnes** dans laquelle vous sélectionnez les colonnes à chiffrer, [le type de chiffrement et la clé de chiffrement de colonne (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) à utiliser.

    Pour chaque patient, vous pouvez chiffrer le **numéro de sécurité sociale** (SSN) et la **date de naissance** (BirthDate). La colonne SSN utilise le chiffrement déterministe, qui prend en charge de façon identique les recherches, les jointures et les regroupements. La colonne BirthDate utilisera le chiffrement aléatoire, qui ne prend pas en charge ces opérations.

    Sélectionnez et définissez le **type de chiffrement** de la colonne SSN sur **Déterministe**, et la colonne BirthDate sur **Lu aléatoirement**, puis cliquez sur **Suivant**.

    ![chiffrer des colonnes](./media/sql-database-always-encrypted/column-selection.png)

4. **Configuration de la clé principale** (CMK)

    La page **Configuration de la clé principale** vous permet de définir votre clé principale de colonne (CMK) et de sélectionner le fournisseur de magasin de clés où la CMK sera stockée. Actuellement, vous pouvez stocker une CMK dans le magasin de certificats Windows, le coffre de clés Azure ou un module de sécurité matériel (HSM). Ce didacticiel montre comment stocker les clés dans le magasin de certificats Windows.

    Vérifiez que l’option **Magasin de certificats Windows** est sélectionnée, puis cliquez sur **Suivant**.

    ![configuration de la clé principale](./media/sql-database-always-encrypted/master-key-configuration.png)


5. **Validation**

    Vous pouvez chiffrer les colonnes maintenant ou enregistrer un script PowerShell à exécuter ultérieurement. Pour ce didacticiel, sélectionnez **Terminer maintenant**, puis cliquez sur **Suivant**.

6. **Résumé**

    Vérifiez que les paramètres sont corrects, puis cliquez sur **Terminer** pour terminer la configuration du chiffrement intégral.


    ![summary](./media/sql-database-always-encrypted/summary.png)


### Que vient de faire précisément l'Assistant ?

Une fois l'Assistant terminé, votre base de données est configurée pour le chiffrement intégral, et les étapes suivantes sont terminées :

- Création d’une clé principale de colonne (CMK).
- Création d’une clé de chiffrement de colonne (CEK).
- Configuration des colonnes sélectionnées pour le chiffrement. (Notre table Patients ne contient aucune donnée pour l’instant, mais toutes les données existantes dans les colonnes sélectionnées sont désormais chiffrées).

Vous pouvez vérifier la création des clés dans SSMS en développant **Clinique** > **Sécurité** > **Clés intégralement chiffrées**. Vous pouvez maintenant afficher les nouvelles clés générées pour vous par l'Assistant.


## Création d’une application cliente compatible avec les données chiffrées

Maintenant que le chiffrement intégral est configuré, nous allons créer une application qui effectue des INSERTIONS et DES SÉLECTIONS sur les colonnes chiffrées. Pour exécuter correctement l'exemple d'application, vous devez l'exécuter sur le même ordinateur où vous avez exécuté l'Assistant Chiffrement intégral. Pour l'exécuter sur un autre ordinateur, vous devez déployer vos certificats de chiffrement intégral sur l'ordinateur exécutant l'application cliente.

> [AZURE.IMPORTANT] Votre application doit utiliser des objets [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) lors de la transmission de données en clair vers le serveur avec des colonnes intégralement chiffrées. La transmission de valeurs littérales sans objets SqlParameter entraînera une exception.


1. Ouvrez Visual Studio et créez une nouvelle application console C#. Assurez-vous que votre projet est défini sur **.NET Framework 4.6** ou version ultérieure.
2. Nommez le projet **AlwaysEncryptedConsoleApp**, puis cliquez sur **OK**.


	![nouvelle application de console](./media/sql-database-always-encrypted/console-app.png)



## Modification de votre chaîne de connexion pour activer le chiffrement intégral

Cette section explique simplement comment activer le chiffrement intégral dans votre chaîne de connexion de base de données. Il s’agit de la section suivante **Exemple d'application console intégralement chiffrée**, dans laquelle vous modifierez l'application console que vous venez de créer.


Pour activer le chiffrement intégral, vous devez ajouter le mot-clé **Paramètre de chiffrement de colonne** à votre chaîne de connexion et le définir sur **Activé**.

Vous pouvez définir cette option directement dans la chaîne de connexion, ou la définir à l'aide d'un paramètre [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). L'exemple d'application de la section suivante montre comment utiliser le paramètre**SqlConnectionStringBuilder**.

> [AZURE.NOTE] Il s'agit de la seule modification nécessaire dans une application cliente spécifique au chiffrement intégral. Si vous utilisez une application qui stocke sa chaîne de connexion en externe (par exemple, dans un fichier de configuration), vous pouvez activer le chiffrement intégral sans modifier le code.


### Activation du chiffrement intégral dans la chaîne de connexion

Ajoutez le mot-clé suivant à votre chaîne de connexion :

    Column Encryption Setting=Enabled


### Activation du chiffrement intégral avec un paramètre SqlConnectionStringBuilder

Le code suivant montre comment activer le chiffrement intégral en définissant le paramètre [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) sur [Activé](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder = 
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting = 
       SqlConnectionColumnEncryptionSetting.Enabled;



## Exemple d’application console intégralement chiffrée

Cet exemple montre comment :

- Modifier votre chaîne de connexion pour activer le chiffrement intégral.
- Insérer des données dans les colonnes chiffrées.
- Sélectionner un enregistrement en filtrant une valeur spécifique dans une colonne chiffrée.

Remplacez le contenu de **Program.cs** par le code suivant. Remplacez la chaîne de connexion pour la variable connectionString globale dans la ligne juste au-dessus de la méthode Main de votre chaîne de connexion valide à partir du portail Azure. Il s'agit de la seule modification que vous devez apporter à ce code.

Exécutez maintenant l'application pour vous assurer que le chiffrement intégral est activé.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;
    
    namespace AlwaysEncryptedConsoleApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure Portal.
        static string connectionString = @"Replace with your connection string";

        static void Main(string[] args)
        {
            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted 
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient() {
                SSN = "999-99-0001", FirstName = "Orlando", LastName = "Gee", BirthDate = DateTime.Parse("01/04/1964") });
            InsertPatient(new Patient() {
                SSN = "999-99-0002", FirstName = "Keith", LastName = "Harris", BirthDate = DateTime.Parse("06/20/1977") });
            InsertPatient(new Patient() {
                SSN = "999-99-0003", FirstName = "Donna", LastName = "Carreras", BirthDate = DateTime.Parse("02/09/1973") });
            InsertPatient(new Patient() {
                SSN = "999-99-0004", FirstName = "Janet", LastName = "Gates", BirthDate = DateTime.Parse("08/31/1985") });
            InsertPatient(new Patient() {
                SSN = "999-99-0005", FirstName = "Lucy", LastName = "Harrington", BirthDate = DateTime.Parse("05/06/1993") });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
         VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }


## Vérification du chiffrement des données

Pour vérifier rapidement que les données réelles sur le serveur sont chiffrées, nous pouvons facilement interroger les données des patients avec SSMS (en utilisant notre connexion actuelle où le paramètre de chiffrement de colonne n'est pas encore activé).

Exécutez la requête suivante sur la base de données Clinique :

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Vous pouvez afficher les colonnes chiffrées qui ne contiennent aucune donnée en clair.

   ![nouvelle application de console](./media/sql-database-always-encrypted/ssms-encrypted.png)


Pour utiliser SSMS afin d’accéder aux données en clair, nous pouvons ajouter le paramètre **Column Encryption Setting=enabled** à la connexion.

1. Dans SSMS, cliquez avec le bouton droit sur votre serveur dans **Explorateur d’objets** et sur **Déconnexion**.
2. Cliquez sur **Connexion** > **Moteur de base de données** pour ouvrir la fenêtre **Connexion au serveur**, puis cliquez sur **Options**.
3. Cliquez sur **Paramètres de connexion supplémentaires** et tapez **Column Encryption Setting=enabled**.

	![nouvelle application de console](./media/sql-database-always-encrypted/ssms-connection-parameter.png)

4. Exécutez la requête suivante sur la base de données Clinique :

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Vous pouvez maintenant afficher les données en clair dans les colonnes chiffrées.


	![nouvelle application de console](./media/sql-database-always-encrypted/ssms-plaintext.png)



> [AZURE.NOTE] Si vous vous connectez avec SSMS (ou n'importe quel client) depuis un autre ordinateur, celui-ci ne pourra pas accéder aux clés de chiffrement et ne pourra donc pas déchiffrer les données.



## Étapes suivantes
Après avoir créé une base de données qui utilise le chiffrement intégral, vous pouvez effectuer les opérations suivantes :

- Exécuter cet exemple à partir d'un autre ordinateur. Il n'aura pas accès aux clés de chiffrement, ne pourra pas afficher les données en clair, et ne s’exécutera pas correctement. 
- [Faire pivoter et nettoyer vos clés](https://msdn.microsoft.com/library/mt607048.aspx).
- [Migrer des données déjà chiffrées avec le chiffrement intégral](https://msdn.microsoft.com/library/mt621539.aspx)
- Déployer des certificats intégralement chiffrés sur d'autres ordinateurs client.


## Informations connexes

- [Chiffrement intégral (développement client)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Chiffrement transparent des données](https://msdn.microsoft.com/library/bb934049.aspx)
- [Chiffrement SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
- [Assistant Chiffrement intégral.](https://msdn.microsoft.com/library/mt459280.aspx)
- [Blog Chiffrement intégral.](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

<!---HONumber=AcomDC_0323_2016-->