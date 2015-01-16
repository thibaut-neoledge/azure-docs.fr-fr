<properties urlDisplayName="SQL Database" pageTitle="Utilisation de la base de données SQL Azure (Java) - Guide des fonctionnalités Azure" metaKeywords="" description="Découvrez comment utiliser la base de données SQL Azure à partir du code Java. " metaCanonical="" services="sql-database" documentationCenter="Java" title="How to Use Azure SQL Database in Java" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Utilisation de la base de données SQL Azure en Java

Les étapes suivantes montrent comment utiliser la base de données SQL Azure avec Java. Par souci de simplicité, les exemples présentés ici sont des exemples de ligne de commande qui pourraient en grande partie s'appliquer à des applications Web, qu'elles soient hébergées en local, dans Azure ou dans d'autres environnements. Ce guide décrit la création d'un serveur et d'une base de données à partir du [portail de gestion Azure](https://windows.azure.com).

## Définition de la base de données SQL Azure

La base de données SQL Azure fournit un système de gestion des bases de données relationnelles pour Azure. Elle est basée sur la technologie SQL Server. Une instance de base de données SQL permet d'approvisionner et de déployer facilement des solutions de base de données relationnelle dans le cloud. Elle tire parti d'un centre de données distribué qui fournit haute disponibilité, extensibilité et sécurité aux entreprises, avec les avantages de la protection des données et de l'auto-adaptation intégrées.

## Sommaire

-   [Concepts][]
-   [Configuration requise][]
-   [Création d'une base de données SQL Azure][]
-   [Détermination de la chaîne de connexion à la base de données SQL][]
-   [Autorisation de l'accès à une plage d'adresses IP][]
-   [Utilisation de la base de données SQL Azure en Java][]
-   [Communication avec la base de données SQL Azure à partir de votre code][]
-   [Création d'une table][]
-   [Création d'un index sur une table][]
-   [Insertion de lignes][]
-   [Extraction de lignes][]
-   [Extraction de lignes à l'aide d'une clause WHERE][]
-   [Extraction d'un nombre de lignes][]
-   [Mise à jour de lignes][]
-   [Suppression de lignes][]
-   [Vérification de l'existence d'une table][]
-   [Suppression d'un index][]
-   [Suppression d'une table][]
-   [Utilisation de la base de données SQL en Java dans un déploiement Azure][]
-   [Étapes suivantes][]

<h2><a id="concepts"></a>Concepts</h2>
La base de données SQL Azure repose sur les technologies SQL Server. De ce fait, il existe beaucoup de similitudes entre l'accès à la base de données SQL à partir de Java et l'accès à SQL Server à partir de Java. Vous pouvez développer une application en local (à l'aide de SQL Server) et vous connecter ensuite à la base de données SQL en modifiant simplement la chaîne de connexion. Vous pouvez utiliser un pilote JDBC SQL Server pour votre application. Toutefois, les quelques différences qui existent entre la base de données SQL et SQL Server peuvent avoir une incidence sur votre application. Pour plus d'informations, consultez la page [Instructions et limitations (Base de données SQL)](http://msdn.microsoft.com/fr-fr/library/windowsazure/ff394102.aspx).

Pour obtenir des ressources supplémentaires sur la base de données SQL, consultez la section [Étapes suivantes][].

<h2><a id="prerequisites"></a>Configuration requise</h2>

Si vous avez l'intention d'utiliser la base de données SQL avec Java, voici les éléments dont vous devez disposer comme condition préalable.

* Kit de développement logiciel (SDK) Java version 1.6 ou ultérieure
* Un abonnement à Azure, pouvant être souscrit à l'adresse suivante : <http://www.microsoft.com/windowsazure/offers/>.
* Si vous utilisez Eclipse, vous aurez besoin de l'environnement de développement intégré (IDE) Eclipse pour développeurs Java EE, Indigo ou une version ultérieure, Vous pouvez le télécharger à partir de <http://www.eclipse.org/downloads/>. De même, vous aurez besoin du plug-in Azure pour Eclipse avec Java (de Microsoft Open Technologies). Pendant l'installation de ce plug-in, assurez-vous que Microsoft JDBC Driver 4.0 pour SQL Server est inclus. Pour plus d'informations, consultez la page [Installation du plug-in Azure pour Eclipse avec Java (par Microsoft Open Technologies)](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh690946.aspx).
* Si vous n'utilisez pas Eclipse, vous aurez besoin de Microsoft JDBC Driver 4.0 pour SQL Server, disponible en téléchargement à l'adresse suivante : <http://www.microsoft.com/fr-fr/download/details.aspx?id=11774>.

<h2><a id="create_db"></a>Création d'une base de données SQL Azure</h2>

Avant d'utiliser la base de données SQL Azure dans du code Java, vous devez créer un serveur de base de données SQL Azure.

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2. Cliquez sur **Nouveau**.

    ![Create new SQL database][create_new]

3. Cliquez sur **Base de données SQL**, puis sur **Création personnalisée**.

    ![Create custom SQL database][create_new_sql_db]

4. Dans la boîte de dialogue **Paramètres de base de données**, spécifiez le nom de votre base de données. Pour les besoins de ce guide, utilisez **gettingstarted**.
5. Pour **Serveur**, sélectionnez **Nouveau serveur de base de données SQL**. Pour les autres champs, utilisez les valeurs par défaut.

    ![SQL database settings][create_database_settings]

6. Cliquez sur la flèche Suivant.	
7. Dans la boîte de dialogue **Paramètres du serveur**, spécifiez un nom d'utilisateur SQL Server. Pour ce guide, **MySQLAdmin** a été utilisé. Spécifiez un mot de passe et confirmez-le. Spécifiez une région et assurez-vous que **Autoriser les services Azure à accéder au serveur** est activé.

    ![SQL server settings][create_server_settings]

8. Cliquez sur le bouton de fin.

<h2><a id="determine_connection_string"></a>Détermination de la chaîne de connexion à la base de données SQL</h2>

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2. Cliquez sur **Bases de données SQL**.
3. Cliquez sur la base de données que vous souhaitez utiliser.
4. Cliquez sur **Afficher les chaînes de connexion**.
5. Mettez en surbrillance le contenu de la chaîne de connexion **JDBC**.

    ![Determine JDBC connection string][get_jdbc_connection_string]

6. Cliquez avec le bouton droit sur le contenu mis en surbrillance de la chaîne de connexion **JDBC**, puis cliquez sur **Copier**.
7. Vous pouvez maintenant coller cette valeur dans votre fichier de code pour créer une chaîne de connexion au format suivant. Remplacez *your_server* (à deux endroits) par le texte que vous avez copié à l'étape précédente, puis remplacez *your_password* par la valeur de mot de passe que vous avez spécifiée lorsque vous avez créé votre compte de base de données SQL (remplacez également les valeurs attribuées à **database=** et **user=** si vous n'avez pas utilisé **gettingstarted** et **MySQLAdmin**, respectivement). 

    String connectionString =
		"jdbc:sqlserver://*your_server*.database.windows.net:1433" + ";" +  
    	"database=gettingstarted" + ";" + 
    	"user=MySQLAdmin@*your_server*" + ";" +  
    	"password=*your_password*" + ";" +  
        "encrypt=true" + ";" +
        "hostNameInCertificate=*.int.mscds.com" + ";" +  
        "loginTimeout=30";

De fait, nous utiliserons cette chaîne plus loin dans ce guide. Pour l'heure, vous savez comment déterminer la chaîne de connexion. De même, selon les besoins de votre application, vous serez peut-être amené à utiliser les paramètres **encrypt** et **hostNameInCertificate** et à modifier le paramètre **loginTimeout**.

<h2><a id="specify_allowed_ips"></a>Autorisation de l'accès à une plage d'adresses IP</h2>
1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com).
2. Cliquez sur **Bases de données SQL**.
3. Cliquez sur **Serveurs**.
4. Cliquez sur le serveur que vous souhaitez utiliser.
5. Cliquez sur **Gérer**.
6. Cliquez sur **Configurer**.
7. Sous **Adresses IP autorisées**, entrez le nom d'une nouvelle règle IP. Spécifiez le début et la fin de la plage d'adresses IP. Pour vous faciliter la tâche, l'adresse IP cliente actuelle est indiquée. L'exemple suivant autorise la saisie d'une seule adresse IP cliente (votre adresse IP sera différente).

    ![Allowed IP addresses dialog][allowed_ips_dialog]

8. Cliquez sur le bouton de fin. Les adresses IP que vous spécifiez sont alors autorisées à accéder à votre serveur de base de données.

<h2><a id="use_sql_azure_in_java"></a>Utilisation de la base de données SQL Azure en Java</h2>

1. Créez un projet Java. Pour les besoins de ce didacticiel, appelez-le **HelloSQLAzure**.
2. Ajoutez au projet un fichier de classe Java nommé **HelloSQLAzure.java**.
3. Ajoutez **Microsoft JDBC Driver pour SQL Server** au chemin d'accès à votre build.

   Si vous utilisez Eclipse :

    1. Dans l'Explorateur de projets d'Eclipse, cliquez avec le bouton droit sur le  **HelloSQLAzure** project and click **Properties**.
    2. Dans le volet gauche de la boîte de dialogue **Propriétés**, cliquez sur **Chemin d'accès de la génération Java**.
    3. Cliquez sur l'onglet **Bibliothèques**, puis sur **Ajouter une bibliothèque**.
    4. Dans la boîte de dialogue **Ajouter une bibliothèque**, sélectionnez **Microsoft JDBC Driver 4.0 pour SQL Server**, cliquez sur **Suivant**, puis sur **Terminer**.
    5. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés**.

    Si vous n'utilisez pas Eclipse, ajoutez le fichier JAR de Microsoft JDBC Driver 4.0 pour SQL Server au chemin de la classe. Pour obtenir des informations associées, consultez la page [Utilisation du pilote JDBC](http://msdn.microsoft.com/fr-fr/library/ms378526.aspx).

4. Dans votre code **HelloSQLAzure.java**, ajoutez des instructions `import` comme indiqué dans l'exemple suivant :

        import java.sql.*;
        import com.microsoft.sqlserver.jdbc.*;

5. Spécifiez votre chaîne de connexion. Vous trouverez ci-dessous un exemple. Comme précédemment, remplacez *your_server* (à deux endroits), *your_user* et *your_password* par les valeurs appropriées pour votre serveur de base de données SQL.

        String connectionString =
        	"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
        		"database=master" + ";" + 
        		"user=your_user@your_server" + ";" +  
        		"password=your_password";

Vous êtes maintenant prêt à ajouter le code qui permettra la communication avec votre serveur de base de données SQL.

<h2><a id="communicate_from_code"></a>Communication avec la base de données SQL Azure à partir de votre code</h2>

Le reste de cette rubrique montre des exemples qui assurent les tâches suivantes :

1. Connexion au serveur de base de données SQL.
2. Définition d'une instruction SQL, par exemple, pour créer ou supprimer une table, insérer/sélectionner/supprimer des lignes, etc.
3. Exécution de l'instruction SQL via un appel à **executeUpdate** ou **executeQuery**.
4. Affichage des résultats de la requête, le cas échéant.

Les sections suivantes sont censées être lues (échantillonnées) dans l'ordre. Le premier extrait est un exemple complet ; les autres reposent sur une partie de l'ossature de l'exemple complet, à savoir, les instructions **import**, les déclarations **class** et **main**, la gestion des erreurs et la fermeture des ressources.

<h2><a id="to_create_table"></a>Création d'une table</h2>

Le code suivant montre comment créer une table nommée **Person**.

	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
	public class HelloSQLAzure {
	
	    public static void main(String[] args) 
	    {
	
			// Connection string for your SQL Database server.
			// Change the values assigned to your_server, 
			// your_user@your_server,
			// and your_password.
			String connectionString = 
				"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
					"database=gettingstarted" + ";" + 
					"user=your_user@your_server" + ";" +  
					"password=your_password";
			
			// The types for the following variables are
			// defined in the java.sql library.
			Connection connection = null;  // For making the connection
			Statement statement = null;    // For the SQL statement
			ResultSet resultSet = null;    // For the result set, if applicable
			
			try
			{
			    // Ensure the SQL Server driver class is available.
			    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
			
			    // Establish the connection.
			    connection = DriverManager.getConnection(connectionString);
			
			    // Define the SQL string.
			    String sqlString = 
					"CREATE TABLE Person (" + 
			        	"[PersonID] [int] IDENTITY(1,1) NOT NULL," +
			            "[LastName] [nvarchar](50) NOT NULL," + 
			            "[FirstName] [nvarchar](50) NOT NULL)";
			
			    // Use the connection to create the SQL statement.
			    statement = connection.createStatement();
			
			    // Execute the statement.
			    statement.executeUpdate(sqlString);
			
			    // Provide a message when processing is complete.
			    System.out.println("Processing complete.");
			
			}
			// Exception handling
	        catch (ClassNotFoundException cnfe)  
	        {
	            
	            System.out.println("ClassNotFoundException " +
	                               cnfe.getMessage());
	        }
	        catch (Exception e)
	        {
	            System.out.println("Exception " + e.getMessage());
	            e.printStackTrace();
	        }
	        finally
	        {
	            try
	            {
	                // Close resources.
	                if (null != connection) connection.close();
	                if (null != statement) statement.close();
	                if (null != resultSet) resultSet.close();
	            }
	            catch (SQLException sqlException)
	            {
	                // No additional action if close() statements fail.
	            }
	        }
	        
	    }
	
	}
	

<h2><a id="to_create_index"></a>Création d'un index sur une table</h2>

Le code suivant montre comment créer un index nommé **index1** sur la table **Person** en utilisant la colonne **PersonID**.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"CREATE CLUSTERED INDEX index1 " + "ON Person (PersonID)";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...



<h2><a id="to_insert_rows"></a>Insertion de lignes</h2>

Le code suivant montre comment ajouter des lignes à la table **Person**.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"SET IDENTITY_INSERT Person ON " + 
	        	"INSERT INTO Person " + 
	            "(PersonID, LastName, FirstName) " + 
	            "VALUES(1, 'Abercrombie', 'Kim')," + 
	            	  "(2, 'Goeschl', 'Gerhard')," + 
	                  "(3, 'Grachev', 'Nikolay')," + 
	                  "(4, 'Yee', 'Tai')," + 
	                  "(5, 'Wilson', 'Jim')";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

 
<h2><a id="to_retrieve_rows"></a>Extraction de lignes</h2>

Le code suivant montre comment récupérer des lignes à partir de la table **Person**.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = "SELECT TOP 10 * FROM Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    resultSet = statement.executeQuery(sqlString);
	
	    // Loop through the results
	    while (resultSet.next())
	    {
	        // Print out the row data
	        System.out.println(
	        	"Person with ID " + 
	        	resultSet.getString("PersonID") + 
	        	" has name " +
	        	resultSet.getString("FirstName") + " " +
	       		resultSet.getString("LastName"));
	        }
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

Le code ci-dessus a permis de sélectionner les 10 premières lignes de la table **Person**. Si vous souhaitez renvoyer toutes les lignes, modifiez l'instruction SQL comme suit :

	String sqlString = "SELECT * FROM Person";

 
<h2><a id="to_retrieve_rows_using_where"></a>Extraction de lignes à l'aide d'une clause WHERE</h2>

Pour extraire des lignes à l'aide d'une clause, utilisez le code ci-dessus tel quel, à l'exception de l'instruction SQL dans laquelle vous devez inclure une clause. L'instruction SQL suivante inclut une clause pour les lignes dont le paramètre **FirstName** a une valeur égale à **Jim**.

	// Définissez la chaîne SQL.
	String sqlString = "SELECT * FROM Person WHERE FirstName='Jim'";
	
Les clauses WHERE peuvent également être utilisées pour extraire des décomptes, mettre à jour des lignes ou supprimer des lignes.

<h2><a id="to_retrieve_row_count"></a>Extraction d'un nombre de lignes</h2>

Le code suivant montre comment récupérer un nombre de lignes à partir de la table **Person**.
 
	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	// Define the SQL string.
	    String sqlString = "SELECT COUNT (PersonID) FROM Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    resultSet = statement.executeQuery(sqlString);
	
	    // Print out the returned number of rows.
	    while (resultSet.next())
	    {
	        System.out.println("There were " + 
	                         resultSet.getInt(1) +
	                         " rows returned.");
	    }
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

<h2><a id="to_update_rows"></a>Mise à jour de lignes</h2>

Le code suivant montre comment mettre à jour des lignes. Dans cet exemple, la valeur du paramètre **LastName** est remplacée par **Kim** pour les lignes dont le paramètre **FirstName** a pour valeur **Jim**.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"UPDATE Person " + "SET LastName = 'Kim' " + "WHERE FirstName='Jim'";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	    
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}// Exception handling and resource closing not shown...

 

<h2><a id="to_delete_rows"></a>Suppression de lignes</h2>

Le code suivant montre comment supprimer des lignes. Dans cet exemple, les lignes dont le paramètre **FirstName** a pour valeur **Jim** sont supprimées.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"DELETE from Person " + 
				"WHERE FirstName='Jim'";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...
	
 
<h2><a id="to_check_table_existence"></a>Vérification de l'existence d'une table</h2>

Le code suivant montre comment déterminer si une table existe.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"IF EXISTS (SELECT 1 " +
	        	"FROM sysobjects " + 
	            "WHERE xtype='u' AND name='Person') " +
	            "SELECT 'Person table exists.'" +
	            "ELSE  " +
	            "SELECT 'Person table does not exist.'";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    resultSet = statement.executeQuery(sqlString);
	
	    // Display the result.
	    while (resultSet.next())
	    {
	        System.out.println(resultSet.getString(1));
	    }
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

<h2><a id="to_drop_index"></a>Suppression d'un index</h2>

Le code suivant montre comment supprimer un index nommé **index1** de la table **Person**.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +
			"database=gettingstarted" + ";" +
			"user=your_user@your_server" + ";" +
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"DROP INDEX index1 " + 
	        	"ON Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

 
<h2><a id="to_drop_table"></a>Suppression d'une table</h2>

Le code suivant montre comment ignorer une table nommée **Person**.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = "DROP TABLE Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

<h2><a id="using_in_azure"></a>Utilisation de la base de données SQL en Java dans un déploiement Azure</h2>

Pour utiliser la base de données SQL en Java dans un déploiement Azure, en plus d'avoir Microsoft JDBC Driver 4.0 pour SQL Server comme bibliothèque dans votre chemin de classe comme indiqué plus haut, vous devez l'empaqueter avec votre déploiement.


**Empaquetage de Microsoft JDBC Driver 4.0 SQL Server si vous utilisez Eclipse**

1. Dans l'Explorateur de projets Eclipse, cliquez avec le bouton droit sur le projet et cliquez sur **Properties**.
2. Dans le volet gauche de la boîte de dialogue **Propriétés**, cliquez sur **Assembly de déploiement**, puis sur **Ajouter**.
3. Dans la boîte de dialogue **Nouvelle directive d'Assembly**, cliquez sur **Entrées du chemin d'accès de la génération Java**, puis sur **Suivant**.
4. Sélectionnez **Microsoft JDBC Driver 4.0 SQL Server**, puis cliquez sur **Terminer**.
5. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés**.
6. Exportez le fichier WAR de votre projet dans votre dossier approot, puis régénérez votre projet Azure selon la procédure décrite dans la page [Création d'une application Hello World à l'aide du plug-in Azure pour Eclipse avec Java (de Microsoft Open Technologies)](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh690944.aspx). Cette rubrique montre également comment exécuter l'application dans l'émulateur de calcul et dans Azure.

**Empaquetage de Microsoft JDBC Driver 4.0 SQL Server si vous n'utilisez pas Eclipse**

* Assurez-vous que la bibliothèque Microsoft JDBC Driver 4.0 SQL Server est incluse dans le même rôle Azure que votre application Java et qu'elle a été ajoutée au chemin de classe de votre application.

<h2><a id="nextsteps"></a>Étapes suivantes</h2>

Pour plus d'informations sur Microsoft JDBC Driver pour SQL Server, consultez la page [Présentation du pilote JDBC](http://msdn.microsoft.com/fr-fr/library/ms378749.aspx). Pour plus d'informations sur la base de données SQL, consultez la page [Vue d'ensemble de la base de données SQL](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336241.aspx).

[Concepts]:#concepts
[Configuration requise]:#prerequisites
[Création d'une base de données SQL Azure]:#create_db
[Détermination de la chaîne de connexion à la base de données SQL]:#determine_connection_string
[Autorisation de l'accès à une plage d'adresses IP]:#specify_allowed_ips
[Utilisation de la base de données SQL Azure en Java]:#use_sql_azure_in_java
[Communication avec la base de données SQL Azure à partir de votre code]:#communicate_from_code
[Création d'une table]:#to_create_table
[Création d'un index sur une table]:#to_create_index
[Insertion de lignes]:#to_insert_rows
[Extraction de lignes]:#to_retrieve_rows
[Extraction de lignes à l'aide d'une clause WHERE]:#to_retrieve_rows_using_where
[Extraction d'un nombre de lignes]:#to_retrieve_row_count
[Mise à jour de lignes]:#to_update_rows
[Suppression de lignes]:#to_delete_rows
[Vérification de l'existence d'une table]:#to_check_table_existence
[Suppression d'un index]:#to_drop_index
[Suppression d'une table]:#to_drop_table
[Utilisation de la base de données SQL en Java dans un déploiement Azure]:#using_in_azure
[Étapes suivantes]:#nextsteps
[create_new]: ./media/sql-data-java-how-to-use-sql-database/WA_New.png
[create_new_sql_db]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_DB_Create.png
[create_database_settings]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_1.png
[create_server_settings]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_2.png
[get_jdbc_connection_string]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_JDBC_ConnectionString.png
[allowed_ips_dialog]: ./media/sql-data-java-how-to-use-sql-database/WA_Allowed_IPs.png
