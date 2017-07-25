---
title: "Utilisation de .NET Core pour interroger Azure SQL Database | Microsoft Docs"
description: "Cette rubrique vous explique comment utiliser .NET Core pour créer un programme qui se connecte à une base de données SQL Azure et l’interroger à l’aide d’instructions Transact-SQL."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/05/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 4b44064f05a24a25c3ac8157e4aa68a8a8d9856a
ms.contentlocale: fr-fr
ms.lasthandoff: 07/13/2017

---
# <a name="use-net-core-c-to-query-an-azure-sql-database"></a>Utilisation de .NET Core (C#) pour interroger une base de données SQL Azure

Ce didacticiel de démarrage rapide indique comment utiliser [.NET Core](https://www.microsoft.com/net/) sur Windows/Linux/macOS pour créer un programme C# qui se connecte à une base de données SQL Azure et utiliser des instructions Transact-SQL pour interroger des données.

## <a name="prerequisites"></a>Composants requis

Pour suivre ce didacticiel de démarrage rapide, vérifiez que vous disposez des éléments suivants :

- base de données SQL Azure. Ce guide de démarrage rapide utilise les ressources créées dans l’une de ces instructions de démarrage rapide : 

   - [Créer une base de données - Portail](sql-database-get-started-portal.md)
   - [Créer une base de données - CLI](sql-database-get-started-cli.md)
   - [Créer une base de données - PowerShell](sql-database-get-started-powershell.md)

- Une [règle de pare-feu au niveau du serveur](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) pour l’adresse IP publique de l’ordinateur que vous utilisez pour ce didacticiel de démarrage rapide.
- Vous avez installé [.NET Core pour votre système d’exploitation](https://www.microsoft.com/net/core). 

## <a name="sql-server-connection-information"></a>Informations de connexion SQL Server

Obtenez les informations de connexion requises pour la connexion à la base de données SQL Azure. Vous aurez besoin du nom du serveur complet, du nom de la base de données et des informations de connexion dans les procédures suivantes.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis cliquez sur votre base de données dans la page **Bases de données SQL**. 
3. Sur la page **Vue d’ensemble** de votre base de données, vérifiez le nom complet du serveur, comme indiqué dans l’image suivante. Vous pouvez pointer sur le nom du serveur pour afficher l’option **Cliquez pour copier**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Si vous avez oublié vos informations de connexion au serveur Azure SQL Database, accédez à la page du serveur SQL Database pour afficher le nom de l’administrateur du serveur. Vous pouvez réinitialiser le mot de passe si nécessaire.

5. Cliquez sur **Afficher les chaînes de connexion de la base de données**.

6. Passez en revue la chaîne de connexion **ADO.NET** complète.

    ![Chaîne de connexion ADO.NET](./media/sql-database-connect-query-dotnet/adonet-connection-string.png)

> [!IMPORTANT]
> Une règle de pare-feu doit être en place pour l’adresse IP publique de l’ordinateur sur lequel vous effectuez ce didacticiel. Si vous êtes sur un autre ordinateur ou si vous avez une autre adresse IP publique, créez une [règle de pare-feu au niveau du serveur à l’aide du portail Azure](sql-database-get-started-portal.md#create-a-server-level-firewall-rule). 
>
  
## <a name="create-a-new-net-project"></a>Création d'un nouveau projet .NET

1. Ouvrez une invite de commandes et créez un dossier nommé *sqltest*. Accédez au dossier que vous avez créé et exécutez la commande suivante :

    ```
    dotnet new console
    ```

2. Ouvrez ***sqltest.csproj*** avec votre éditeur de texte favori et ajoutez System.Data.SqlClient en tant que dépendance en utilisant le code suivant :

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.3.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-sql-database"></a>Insertion du code pour interroger la base de données SQL

1. Dans votre environnement de développement ou votre éditeur de texte favori, ouvrez **Program.cs**

2. Remplacez le contenu par le code suivant et ajoutez les valeurs appropriées pour votre serveur, base de données, utilisateur et mot de passe.

```csharp
using System;
using System.Data.SqlClient;
using System.Text;

namespace sqltest
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
            Console.ReadLine();
        }
    }
}
```

## <a name="run-the-code"></a>Exécution du code

1. Exécutez ensuite les commandes suivantes dans l’invite de commandes :

   ```csharp
   dotnet restore
   dotnet run
   ```

2. Vérifiez que les 20 premières lignes sont renvoyées, puis fermez la fenêtre d’application.


## <a name="next-steps"></a>Étapes suivantes

- [Prise en main de .NET Core sur Windows/Linux/macOS à l’aide de la ligne de commande](/dotnet/core/tutorials/using-with-xplat-cli.md).
- Découvrez comment [connecter et interroger une base de données SQL Azure à l’aide de .NET Framework et Visual Studio](sql-database-connect-query-dotnet-visual-studio.md).  
- Découvrez comment [concevoir votre première base de données SQL Azure à l’aide de SSMS](sql-database-design-first-database.md) ou [concevoir votre première base de données SQL Azure à l’aide de .NET](sql-database-design-first-database-csharp.md).
- Pour plus d’informations sur .NET, consultez la [documentation .NET](https://docs.microsoft.com/dotnet/).

