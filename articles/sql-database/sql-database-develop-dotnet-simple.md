<properties
	pageTitle="Connexion à SQL Database à l’aide de .NET (C#)"
	description="Utilisez l'exemple de code dans cette prise en main pour créer une application moderne avec C# et soutenue par une base de données relationnelle puissante dans le cloud avec la base de données SQL Azure."
	services="sql-database"
	documentationCenter=""
	authors="tobbox"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/08/2015"
	ms.author="tobiast"/>


# Utilisation de la base de données SQL à partir de .NET (C#)


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


## Composants requis

### .NET Framework

.NET Framework est préinstallé avec Windows. Pour Linux et Mac OS X, vous pouvez télécharger .NET Framework à partir du [projet Mono](http://www.mono-project.com/).

### Base de données SQL

Consultez la [page de prise en main](sql-database-get-started.md) pour apprendre à créer un exemple de base de données. Il est important que vous suiviez le guide pour créer un **modèle de base de données AdventureWorks**. Les exemples ci-dessous fonctionnent uniquement avec le **schéma AdventureWorks**.

## Étape 1 : obtenir la chaîne de connexion

[AZURE.INCLUDE [sql-database-include-connection-string-dotnet-20-portalshots](../../includes/sql-database-include-connection-string-dotnet-20-portalshots.md)]

## Étape 2 : se connecter

La [classe System.Data.SqlClient.SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) est utilisée pour la connexion à la base de données SQL.


```
using System.Data.SqlClient;

class Sample
{
  static void Main()
  {
	  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={yourpassword};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
	  {
		  conn.Open();
	  }
  }
}
```

## Étape 3 : exécuter une requête

Vous pouvez utiliser les classes [System.Data.SqlClient.SqlCommand](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx) et [SqlDataReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqldatareader.aspx) pour récupérer un jeu de résultats d'une requête à partir de la base de données SQL. Notez que System.Data.SqlClient prend également en charge la récupération de données dans une classe [System.Data.DataSet](https://msdn.microsoft.com/library/system.data.dataset.aspx) hors connexion.

```
using System;
using System.Data.SqlClient;

class Sample
{
	static void Main()
	{
	  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={yourpassword};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
		{
			var cmd = conn.CreateCommand();
			cmd.CommandText = @"
					SELECT
						c.CustomerID
						,c.CompanyName
						,COUNT(soh.SalesOrderID) AS OrderCount
					FROM SalesLT.Customer AS c
					LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID
					GROUP BY c.CustomerID, c.CompanyName
					ORDER BY OrderCount DESC;";

			conn.Open();

			using(var reader = cmd.ExecuteReader())
			{
				while(reader.Read())
				{
					Console.WriteLine("ID: {0} Name: {1} Order Count: {2}", reader.GetInt32(0), reader.GetString(1), reader.GetInt32(2));
				}
			}					
		}
	}
}

```  

## Étape 4 : insérer une ligne

Dans cet exemple, vous allez découvrir comment exécuter une instruction [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) en toute sécurité, passer des paramètres pour protéger votre application des vulnérabilités découlant de l’injection de code SQL (https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) et récupérer la valeur de la [Clé primaire](https://msdn.microsoft.com/library/ms179610.aspx) générée automatiquement.

```
using System;
using System.Data.SqlClient;

class Sample
{
    static void Main()
    {
		using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={yourpassword};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
        {
            var cmd = conn.CreateCommand();
            cmd.CommandText = @"
                INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate)
                OUTPUT INSERTED.ProductID
                VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP)";

            cmd.Parameters.AddWithValue("@Name", "SQL Server Express");
            cmd.Parameters.AddWithValue("@Number", "SQLEXPRESS1");
            cmd.Parameters.AddWithValue("@Cost", 0);
            cmd.Parameters.AddWithValue("@Price", 0);

            conn.Open();

            int insertedProductId = (int)cmd.ExecuteScalar();

            Console.WriteLine("Product ID {0} inserted.", insertedProductId);
        }
    }
}
```

<!---HONumber=AcomDC_1210_2015-->