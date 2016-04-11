<properties
	pageTitle="Connexion à la base de données SQL à l’aide de Java avec JDBC sous Windows"
	description="Cette rubrique présente un exemple de code Java que vous pouvez utiliser pour vous connecter à la base de données SQL Azure. L'exemple utilise JDBC et s'exécute sur un ordinateur client Windows."
	services="sql-database"
	documentationCenter=""
	authors="LuisBosquez"
	manager="jeffreyg"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="lbosq"/>


# Connexion à la base de données SQL à l’aide de Java avec JDBC sous Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Cette rubrique présente un exemple de code Java que vous pouvez utiliser pour vous connecter à la base de données SQL Azure. L'exemple Java s'appuie sur le Kit de développement Java (JDK) version 1.8. L'exemple se connecte à une base de données SQL Azure en utilisant le pilote JDBC.

## Étape 1 : configurer l’environnement de développement

Installez les pilotes et les bibliothèques :

- [Pilote Microsoft JDBC pour SQL Server - SQL JDBC 4](http://www.microsoft.com/download/details.aspx?displaylang=en&id=11774).
- N'importe quelle plateforme de système d'exploitation qui exécute le [Kit de développement Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

## Étape 2 : créer une base de données SQL

Consultez la [page de prise en main](sql-database-get-started.md) pour apprendre à créer une base de données.

## Étape 3 : créer une table SQL

L'exemple de code Java dans cette rubrique suppose que le tableau de test suivant existe déjà dans votre base de données SQL Azure.

<!--
Could this instead be a #tempPerson table, so that the Java code sample could be fully self-sufficient and be runnable (with automatic cleanup)?
-->


	CREATE TABLE Person
	(
		id         INT    PRIMARY KEY    IDENTITY(1,1),
		firstName  VARCHAR(32),
		lastName   VARCHAR(32),
		age        INT
	);


## Étape 4 : obtenir la chaîne de connexion

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE] Si vous utilisez le pilote JTDS JDBC, vous devez ajouter « ssl=require » à l’URL de la chaîne de connexion et vous devez définir l’option suivante pour l’environnement JVM : « -Djsse.enableCBCProtection=false ». Cette option JVM désactive un correctif pour un problème de sécurité. Par conséquent, assurez-vous que vous savez quels risques vous prenez avant de définir cette option.


## Étape 5 : compiler l’exemple de code Java


La section contient l'essentiel de l'exemple de code Java. Il comporte des commentaires indiquant l'emplacement où vous devez copier et coller les segments Java plus petits qui sont présentés dans les sections suivantes. L'exemple de cette section pourrait être compilé et exécuté même sans les opérations de copier-coller près des commentaires, mais il ne ferait que se connecter et se terminer. Vous trouverez les commentaires suivants :


1. `// INSERT two rows into the table.`
2. `// TRANSACTION and commit for an UPDATE.`
3. `// SELECT rows from the table.`


Voici l'essentiel de l'exemple de code Java. L'exemple inclut la fonction `main` de la classe `SQLDatabaseTest`.


	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;

	public class SQLDatabaseTest {

		public static void main(String[] args) {
			String connectionString =
				"jdbc:sqlserver://your_server.database.windows.net:1433;"
				+ "database=your_database;"
				+ "user=your_user@your_server;"
				+ "password=your_password;"
				+ "encrypt=true;"
				+ "trustServerCertificate=false;"
				+ "hostNameInCertificate=*.database.windows.net;"
				+ "loginTimeout=30;";

			// Declare the JDBC objects.
			Connection connection = null;
			Statement statement = null;
			ResultSet resultSet = null;
			PreparedStatement prepsInsertPerson = null;
			PreparedStatement prepsUpdateAge = null;

			try {
				connection = DriverManager.getConnection(connectionString);

				// INSERT two rows into the table.
				// ...

				// TRANSACTION and commit for an UPDATE.
				// ...

				// SELECT rows from the table.
				// ...
			}
			catch (Exception e) {
				e.printStackTrace();
			}
			finally {
				// Close the connections after the data has been handled.
				if (prepsInsertPerson != null) try { prepsInsertPerson.close(); } catch(Exception e) {}
				if (prepsUpdateAge != null) try { prepsUpdateAge.close(); } catch(Exception e) {}
				if (resultSet != null) try { resultSet.close(); } catch(Exception e) {}
				if (statement != null) try { statement.close(); } catch(Exception e) {}
				if (connection != null) try { connection.close(); } catch(Exception e) {}
			}
		}
	}


Bien entendu, pour réellement exécuter l'exemple de code Java précédent, vous devez placer les valeurs réelles dans la chaîne de connexion pour remplacer les espaces réservés :


- your\_server
- your\_database
- your\_user
- your\_password


## Étape 6 : insérer des lignes


Ce segment Java émet une instruction INSERT Transact-SQL pour insérer deux lignes dans le tableau Person. La séquence générale est la suivante :


1. Générez un objet `PreparedStatement` à l'aide de l'objet `Connection`.
 - Nous incluons le paramètre `Statement.RETURN_GENERATED_KEYS` afin de pouvoir obtenir ultérieurement la valeur qui a été générée automatiquement pour la valeur de clé **id**.
2. Appelez la méthode `execute` sur l'objet `PreparedStatement`.
3. Obtenez la valeur numérique qui a été générée automatiquement pour la clé primaire, à l'aide de l'objet `PreparedStatement`.
 - Ceci est lié à la spécification AUTO\_INCREMENT de la colonne **id** du tableau Person


Copiez et collez ce court segment Java dans l'exemple de code principal où vous voyez le commentaire `// INSERT two rows into the table.`.


	// Create and execute an INSERT SQL prepared statement.
	String insertSql = "INSERT INTO Person (firstName, lastName, age) VALUES "
		+ "('Bill', 'Gates', 59), "
		+ "('Steve', 'Ballmer', 59);";

	prepsInsertPerson = connection.prepareStatement(
		insertSql,
		Statement.RETURN_GENERATED_KEYS);
	prepsInsertPerson.execute();
	// Retrieve the generated key from the insert.
	resultSet = prepsInsertPerson.getGeneratedKeys();
	// Iterate through the set of generated keys.
	while (resultSet.next()) {
		System.out.println("Generated: " + resultSet.getString(1));
	}


## Étape 7 : valider une transaction

Le segment suivant de code Java émet une instruction UPDATE Transact-SQL pour augmenter la valeur `age` pour chaque ligne du tableau Person. La séquence générale est la suivante :


1. La méthode `setAutoCommit` est appelée pour empêcher que des mises à jour ne soient automatiquement validées dans la base de données.
2. La méthode `executeUpdate` est appelée pour exécuter UPDATE dans le contexte de la transaction.
3. La méthode `commit` est appelée pour valider explicitement la transaction.


Copiez et collez ce court segment Java dans l'exemple de code principal où vous voyez le commentaire `// TRANSACTION and commit for an UPDATE.`.


	// Set AutoCommit value to false to execute a single transaction at a time.
	connection.setAutoCommit(false);

	// Write the SQL Update instruction and get the PreparedStatement object.
	String transactionSql = "UPDATE Person SET Person.age = Person.age + 1;";
	prepsUpdateAge = connection.prepareStatement(transactionSql);

	// Execute the statement.
	prepsUpdateAge.executeUpdate();

	//Commit the transaction.
	connection.commit();

	// Return the AutoCommit value to true.
	connection.setAutoCommit(true);


## Étape 8 : exécuter une requête


Ce segment Java exécute une instruction SELECT Transact-SQL pour afficher toutes les lignes mises à jour du tableau Person. La séquence générale est la suivante :


1. Générez un objet `Statement` à l'aide de l'objet `Connection`.
2. Générez un objet `ResultSet` à l'aide de l'objet `Statement`.
3. Créez une boucle d'un appel à `resultSet.next` pour parcourir toutes les lignes retournées.


Copiez et collez ce court segment Java dans l'exemple de code principal où vous voyez le commentaire `// SELECT rows from a table.`.


	// Create and execute a SELECT SQL statement.
	String selectSql = "SELECT firstName, lastName, age FROM dbo.Person";
	statement = connection.createStatement();
	resultSet = statement.executeQuery(selectSql);

	// Iterate through the result set and print the attributes.
	while (resultSet.next()) {
		System.out.println(resultSet.getString(2) + " "
			+ resultSet.getString(3));
	}

## Étapes suivantes

Pour plus d’informations, consultez le [Centre pour développeurs Java](/develop/java/).

<!---HONumber=AcomDC_0330_2016-->