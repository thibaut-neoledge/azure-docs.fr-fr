<properties 
	pageTitle="Utilisation de la base de données SQL à partir de .NET (C#)" 
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
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="tobiast"/>


# Utilisation de la base de données SQL à partir de .NET (C#) 


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


## Configuration requise

### .NET Framework

.NET Framework est préinstallé avec Windows. Pour Linux et Mac OS X, vous pouvez télécharger .NET Framework à partir du [projet Mono](http://www.mono-project.com/).

### Une base de données SQL

Consultez la [page de prise en main](sql-database-get-started.md) pour découvrir comment créer un exemple de base de données et obtenir votre chaîne de connexion.

## Se connecter à la base de données SQL

La [classe System.Data.SqlClient.SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) est utilisée pour la connexion à la base de données SQL.
	
```
using System.Data.SqlClient;

class Sample
{
  static void Main()
  {
	  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
	  {
		  conn.Open();	
	  }
  }
}	
```

## Exécution d'une requête et récupération du jeu de résultats 

Vous pouvez utiliser les classes [System.Data.SqlClient.SqlCommand](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx) et [SqlDataReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqldatareader.aspx) pour récupérer un jeu de résultats d'une requête à partir de la base de données SQL. Notez que System.Data.SqlClient prend également en charge la récupération de données dans une classe [System.Data.DataSet](https://msdn.microsoft.com/library/system.data.dataset.aspx) hors connexion.
	
```
using System;
using System.Data.SqlClient;

class Sample
{
	static void Main()
	{
	  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
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

## Insertion d'une ligne, transmission de paramètres et récupération de la valeur de la clé primaire générée 

Dans la base de données SQL, la propriété [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) et l'objet [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) peuvent être utilisés pour générer automatiquement des valeurs de [clé primaire](https://msdn.microsoft.com/library/ms179610.aspx). Dans cet exemple, vous verrez comment procéder pour exécuter une [instruction d'insertion](https://msdn.microsoft.com/library/ms174335.aspx), transmettre des paramètres en toute sécurité pour une protection contre une [injection SQL](https://msdn.microsoft.com/magazine/cc163917.aspx) et récupérer la valeur de la clé primaire générée automatiquement.

La méthode [ExecuteScalar](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executescalar.aspx) dans la classe [System.Data.SqlClient.SqlCommand](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx) peut être utilisée pour exécuter une instruction et récupérer la première colonne et la première ligne retournées par cette instruction. La clause [OUTPUT](https://msdn.microsoft.com/library/ms177564.aspx) de l'instruction INSERT peut être utilisée pour retourner les valeurs insérées comme jeu de résultats dans l'application appelante. Notez que la clause OUTPUT est également prise en charge par les instructions [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx), [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) et [MERGE](https://msdn.microsoft.com/library/bb510625.aspx). Si plus d'une ligne est insérée, utilisez la méthode [ExecuteReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executereader.aspx) pour récupérer les valeurs insérées pour toutes les lignes.
	
```
class Sample
{
    static void Main()
    {
		using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
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

 

<!---HONumber=July15_HO4-->