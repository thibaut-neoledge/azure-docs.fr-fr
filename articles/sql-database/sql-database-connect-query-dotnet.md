---
title: "Se connecter à Azure SQL Database à l’aide de .NET (C#) | Microsoft Docs"
description: "Cet article présente un exemple de code .NET que vous pouvez utiliser pour vous connecter à la base de données Azure SQL Database et pour interroger cette dernière."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 7faca033-24b4-4f64-9301-b4de41e73dfd
ms.service: sql-database
ms.custom: develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/23/2017
ms.author: andrela
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 4faa029062fef6495debd5d787a1d86f4747453b
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017


---
# <a name="azure-sql-database-use-net-c-to-connect-and-query-data"></a>Azure SQL Database : utilisez .NET (C#) pour vous connecter et interroger des données

Ce guide de démarrage rapide indique comment utiliser [C# et ADO.NET](https://msdn.microsoft.com/library/kb9s9ks0.aspx) pour se connecter à une base de données SQL Azure, puis utiliser les instructions Transact-SQL pour interroger, insérer, mettre à jour et supprimer des données dans la base de données à partir des plateformes Windows, Mac OS et Ubuntu Linux.

## <a name="prerequisites"></a>Composants requis

Ce guide de démarrage rapide utilise comme point de départ les ressources créées dans l’une de ces instructions de démarrage rapide :

- [Créer une base de données - Portail](sql-database-get-started-portal.md)
- [Créer une base de données - CLI](sql-database-get-started-cli.md)
- [Créer une base de données - PowerShell](sql-database-get-started-powershell.md)

## <a name="install-net"></a>Installer .NET

Les étapes de cette section supposent que vous êtes familiarisé avec le développement à l’aide de .NET et que vous ne savez pas utiliser la base de données SQL Azure. Si vous êtes novice en développement avec .NET, consultez [Créer une application à l’aide de SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) et sélectionnez **C#** , puis sélectionnez votre système d’exploitation.

### <a name="windows-net-framework-and-net-core"></a>**Windows .NET Framework et .NET Core**

Visual Studio 2017 Community est un IDE complet, extensible et gratuit qui permet de créer des applications modernes pour Android, iOS et Windows, ainsi que des applications web et de base de données et des services cloud. Vous pouvez soit installer le composant .NET Framework complet, soit installer uniquement .NET Core. Les extraits de code du guide de démarrage rapide fonctionnent dans les deux cas. Si vous disposez déjà de Visual Studio sur votre ordinateur, ignorez les étapes suivantes.

1. Téléchargez le [programme d’installation de Visual Studio 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15). 
2. Exécutez le programme d’installation et suivez que les invites.

### <a name="mac-os"></a>**Mac OS**
Ouvrez votre terminal et accédez au répertoire dans lequel vous envisagez de créer votre projet .NET Core. Entrez les commandes suivantes pour installer **brew**, **OpenSSL** et **.NET Core**. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

Installez .NET Core sur macOS. Téléchargez le [programme d’installation officiel](https://go.microsoft.com/fwlink/?linkid=843444). Ce programme d’installation installe les outils et les place dans votre chemin d’accès afin que vous puissiez exécuter dotnet à partir de la Console

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Ouvrez votre terminal et accédez au répertoire dans lequel vous envisagez de créer votre projet .NET Core. Entrez les commandes suivantes pour installer **.NET Core**.

```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
sudo apt-get update
sudo apt-get install dotnet-dev-1.0.1
```

## <a name="get-connection-information"></a>Obtenir des informations de connexion

Obtenez les informations de connexion requises pour la connexion à la base de données SQL Azure. Vous aurez besoin du nom du serveur complet, du nom de la base de données et des informations de connexion dans les procédures suivantes.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis cliquez sur votre base de données dans la page **Bases de données SQL**. 
3. Sur la page **Vue d’ensemble** de votre base de données, vérifiez le nom complet du serveur, comme indiqué dans l’image ci-dessous. Vous pouvez pointer sur le nom du serveur pour afficher l’option **Cliquez pour copier**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Si vous avez oublié vos informations de connexion à votre serveur Azure SQL Database, accédez à la page du serveur SQL Database pour afficher le nom de l’administrateur du serveur et, si nécessaire, réinitialiser le mot de passe.

5. Cliquez sur **Afficher les chaînes de connexion de la base de données**.

6. Passez en revue la chaîne de connexion **ADO.NET** complète.

    ![Chaîne de connexion ADO.NET](./media/sql-database-connect-query-dotnet/adonet-connection-string.png)
  
## <a name="add-systemdatasqlclient"></a>Ajouter System.Data.SqlClient
Lorsque vous utilisez .NET Core, ajoutez System.Data.SqlClient au fichier ***csproj*** de votre projet en tant que dépendance.

```xml
<ItemGroup>
    <PackageReference Include="System.Data.SqlClient" Version="4.3.0" />
</ItemGroup>
```

## <a name="select-data"></a>Sélectionner des données

1. Dans votre environnement de développement, ouvrez un fichier de code vide.
2. Ajoutez ```using System.Data.SqlClient``` à votre fichier de code ([System.Data.SqlClient namespace](https://msdn.microsoft.com/library/system.data.sqlclient.aspx)). 

3. Utilisez le code suivant pour rechercher les 20 premiers produits par catégorie à l’aide de la commande [SqlCommand.ExecuteReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executereader.aspx) avec une instruction Transact-SQL [SELECT](https://msdn.microsoft.com/library/ms189499.aspx). Ajoutez les valeurs appropriées pour votre serveur, base de données, utilisateur et mot de passe.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                    sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                    sb.Append("JOIN [SalesLT].[Product] p ");
                    sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                    String sql = sb.ToString();

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        using (SqlDataReader reader = command.ExecuteReader())
                        {
                            while (reader.Read())
                            {
                                Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                            }
                        }
                    }                    
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## <a name="insert-data"></a>Insertion des données

Utilisez le code suivant pour insérer un nouveau produit dans la table SalesLT.Product à l’aide de la commande [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) avec une instruction Transact-SQL [INSERT](https://msdn.microsoft.com/library/ms174335.aspx). Ajoutez les valeurs appropriées pour votre serveur, base de données, utilisateur et mot de passe.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nInsert data example:");
                    Console.WriteLine("=========================================\n");

                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("INSERT INTO [SalesLT].[Product] ([Name], [ProductNumber], [Color], [StandardCost], [ListPrice], [SellStartDate]) ");
                    sb.Append("VALUES (@Name, @ProductNumber, @Color, @StandardCost, @ListPrice, @SellStartDate);");
                    String sql = sb.ToString();
                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        command.Parameters.AddWithValue("@Name", "BrandNewProduct");
                        command.Parameters.AddWithValue("@ProductNumber", "200989");
                        command.Parameters.AddWithValue("@Color", "Blue");
                        command.Parameters.AddWithValue("@StandardCost", 75);
                        command.Parameters.AddWithValue("@ListPrice", 80);
                        command.Parameters.AddWithValue("@SellStartDate", "7/1/2016");
                        int rowsAffected = command.ExecuteNonQuery();
                        Console.WriteLine(rowsAffected + " row(s) inserted");
                    }         
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## <a name="update-data"></a>Mettre à jour des données

Utilisez le code suivant pour mettre à jour le nouveau produit que vous avez ajouté précédemment à l’aide de la commande [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) avec une instruction Transact-SQL [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx). Ajoutez les valeurs appropriées pour votre serveur, base de données, utilisateur et mot de passe.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nUpdate data example");
                    Console.WriteLine("=========================================\n");

                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("UPDATE [SalesLT].[Product] SET ListPrice = @ListPrice WHERE Name = @Name;");
                    String sql = sb.ToString();
                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        command.Parameters.AddWithValue("@Name", "BrandNewProduct");
                        command.Parameters.AddWithValue("@ListPrice", 500);
                        int rowsAffected = command.ExecuteNonQuery();
                        Console.WriteLine(rowsAffected + " row(s) updated");
                    }         
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## <a name="delete-data"></a>Suppression de données

Utilisez le code suivant pour supprimer le nouveau produit que vous avez ajouté précédemment à l’aide de la commande [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) avec une instruction Transact-SQL [DELETE](https://msdn.microsoft.com/library/ms189835.aspx). Ajoutez les valeurs appropriées pour votre serveur, base de données, utilisateur et mot de passe.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nDelete data example");
                    Console.WriteLine("=========================================\n");

                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("DELETE FROM SalesLT.Product WHERE Name = @Name;");
                    String sql = sb.ToString();
                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        command.Parameters.AddWithValue("@Name", "BrandNewProduct");
                        int rowsAffected = command.ExecuteNonQuery();
                        Console.WriteLine(rowsAffected + " row(s) deleted");
                    }         
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
- [Concevoir votre première base de données SQL Azure](sql-database-design-first-database.md)
- [Documentation .NET](https://docs.microsoft.com/dotnet/).
- [Se connecter et lancer des requêtes avec SSMS](sql-database-connect-query-ssms.md)
- [Azure SQL Database : utilisez Visual Studio Code pour vous connecter et interroger des données](sql-database-connect-query-vscode.md).


