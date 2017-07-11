---
title: "Se connecter à la base de données Azure pour PostgreSQL à partir de C# | Microsoft Docs"
description: "Ce guide de démarrage rapide fournit un exemple de code C# (.Net), que vous pouvez utiliser pour vous connecter et interroger des données de la base de données Azure pour PostgreSQL."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: csharp
ms.topic: hero-article
ms.date: 06/23/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 87854637471fa15a76ae216cb57c549962810b7b
ms.contentlocale: fr-fr
ms.lasthandoff: 06/28/2017

---

<a id="azure-database-for-postgresql-use-net-c-to-connect-and-query-data" class="xliff"></a>

# Base de données Azure pour PostgreSQL : Utilisation de .NET (C#) pour se connecter et interroger des données
Ce guide de démarrage rapide vous explique comment vous connecter à une base de données Azure pour PostgreSQL en utilisant une application C#. Il détaille l’utilisation d’instructions SQL pour interroger la base de données, la mettre à jour, y insérer des données ou en supprimer. Les étapes de cet article supposent que vous connaissez le développement avec C#, mais que vous ne savez pas utiliser la base de données Azure pour PostgreSQL.

<a id="prerequisites" class="xliff"></a>

## Composants requis
Ce guide de démarrage rapide s’appuie sur les ressources créées dans l’un de ces guides :
- [Créer une base de données - Portail](quickstart-create-server-database-portal.md)
- [Créer une base de données - CLI](quickstart-create-server-database-azure-cli.md)

Il vous faudra également :
- Installer [.NET Framework](https://www.microsoft.com/net/download)
- Installez [Visual Studio](https://www.visualstudio.com/downloads/)
- Installer [Npgsql](http://www.npgsql.org/doc/index.html) 

<a id="install-visual-studio-and-net" class="xliff"></a>

## Installer Visual Studio et .NET
Les étapes de cette section supposent que vous êtes familiarisé avec l’utilisation de .NET.

<a id="windows-net-framework-and-net-core" class="xliff"></a>

### **Windows .NET Framework et .NET Core**
Visual Studio 2017 Community est un IDE complet, extensible et gratuit qui permet de créer des applications modernes pour Android, iOS et Windows, ainsi que des applications web et de base de données et des services cloud. Vous pouvez soit installer le composant .NET Framework complet, soit installer uniquement .NET Core. Les extraits de code du guide de démarrage rapide fonctionnent dans les deux cas. Si vous disposez déjà de Visual Studio sur votre ordinateur, ignorez les étapes suivantes.

1. Téléchargez le [programme d’installation de Visual Studio 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15). 
2. Exécutez le programme d’installation et suivez que les invites.

<a id="mac-os" class="xliff"></a>

### **Mac OS**
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

<a id="linux-ubuntu" class="xliff"></a>

### **Linux (Ubuntu)**
Ouvrez votre terminal et accédez au répertoire dans lequel vous envisagez de créer votre projet .NET Core. Entrez les commandes suivantes pour installer **.NET Core**.

```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
sudo apt-get update
sudo apt-get install dotnet-dev-1.0.1
```

<a id="install-npgsql-references-into-your-visual-studio-solution" class="xliff"></a>

## Installer les références Npgsql dans votre solution Visual Studio
Pour vous connecter à PostgreSQL à partir de l’application C#, utilisez la bibliothèque ADO.Net open source appelée Npgsql. NuGet vous aide à télécharger et gérer facilement les références.

1. Créez une solution C# ou ouvrez-en une existante : 
   - Dans Visual Studio, créez une solution en cliquant sur le menu Fichier **Nouveau** > **Projet**.
   - Dans la boîte de dialogue Nouveau projet, développez **Modèles** > **Visual C#**. 
   - Choisissez un modèle approprié tel que **Application console (.NET Core)**.

2. Utilisez le gestionnaire de package NuGet pour installer Npgsql :
   - Cliquez sur **Outils** > **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**.
   - Dans la **Console du Gestionnaire de package**, entrez `Install-Package Npgsql`
   - La commande d’installation télécharge le fichier Npgsql.dll et les assemblys associés, et elle les ajoute comme dépendances dans la solution.

<a id="get-connection-information" class="xliff"></a>

## Obtenir des informations de connexion
Obtenez les informations de connexion requises pour vous connecter à la base de données Azure pour PostgreSQL. Vous devez disposer du nom de serveur complet et des informations d’identification.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis recherchez le serveur que vous venez de créer, par exemple **mypgserver-20170401**.
3. Cliquez sur le nom du serveur **mypgserver-20170401**.
4. Sélectionnez la page **Présentation** du serveur. Prenez note du **nom du serveur** et du **nom de connexion d’administrateur du serveur**.
 ![Base de données Azure pour PostgreSQL - Connexion d’administrateur du serveur](./media/connect-csharp/1-connection-string.png)
5. Si vous avez oublié vos informations de connexion au serveur, accédez à la page **Vue d’ensemble** pour afficher le nom de connexion de l’administrateur du serveur et, si nécessaire, réinitialiser le mot de passe.

<a id="connect-create-table-and-insert-data" class="xliff"></a>

## Se connecter, créer des tables et insérer des données
Utilisez le code suivant pour vous connecter et charger les données à l’aide d’instructions SQL **CREATE TABLE** et **INSERT INTO**. Le code utilise la classe NpgsqlCommand avec la méthode [Open()](http://www.npgsql.org/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_Open) pour établir une connexion à PostgreSQL. Le code utilise ensuite la méthode [CreateCommand()](http://www.npgsql.org/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_CreateCommand), définit la propriété CommandText et appelle la méthode [ExecuteNonQuery()](http://www.npgsql.org/api/Npgsql.NpgsqlCommand.html#Npgsql_NpgsqlCommand_ExecuteNonQuery) pour exécuter les commandes de la base de données. 

Remplacez les paramètres Host, DBName, User et Password par les valeurs spécifiées lors de la création du serveur et de la base de données. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Npgsql;

namespace Driver
{
    public class AzurePostgresCreate
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "mypgserver-20170401.postgres.database.azure.com";
        private static string User = "mylogin@mypgserver-20170401";
        private static string DBname = "mypgsqldb";
        private static string Password = "<server_admin_password>";
        private static string Port = "5432";

        static void Main(string[] args)
        {
            // Build connection string using parameters from portal
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};",
                    Host,
                    User,
                    DBname,
                    Port,
                    Password);

            var conn = new NpgsqlConnection(connString);

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText = "DROP TABLE IF EXISTS inventory;";
            command.ExecuteNonQuery();
            Console.Out.WriteLine("Finished dropping table (if existed)");

            command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
            command.ExecuteNonQuery();
            Console.Out.WriteLine("Finished creating table");

            command.CommandText =
                String.Format(
                    @"
                                INSERT INTO inventory (name, quantity) VALUES ({0}, {1});
                                INSERT INTO inventory (name, quantity) VALUES ({2}, {3});
                                INSERT INTO inventory (name, quantity) VALUES ({4}, {5});
                            ",
                    "\'banana\'", 150,
                    "\'orange\'", 154,
                    "\'apple\'", 100
                    );

            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows inserted={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

<a id="read-data" class="xliff"></a>

## Lire les données
Utilisez le code suivant pour vous connecter et lire des données à l’aide d’une instruction SQL **SELECT**. Le code utilise la classe NpgsqlCommand avec la méthode [Open()](http://www.npgsql.org/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_Open) pour établir une connexion à PostgreSQL. Le code utilise ensuite la méthode [CreateCommand()](http://www.npgsql.org/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_CreateCommand) et la méthode [ExecuteReader()](http://www.npgsql.org/api/Npgsql.NpgsqlCommand.html#Npgsql_NpgsqlCommand_ExecuteReader) pour exécuter les commandes de la base de données. Puis le code utilise [Read()](http://www.npgsql.org/api/Npgsql.NpgsqlDataReader.html#Npgsql_NpgsqlDataReader_Read) pour accéder à des enregistrements dans les résultats. Ensuite, le code utilise [GetInt32()](http://www.npgsql.org/api/Npgsql.NpgsqlDataReader.html#Npgsql_NpgsqlDataReader_GetInt32_System_Int32_) et [GetString()](http://www.npgsql.org/api/Npgsql.NpgsqlDataReader.html#Npgsql_NpgsqlDataReader_GetString_System_Int32_) pour analyser les valeurs de l’enregistrement.

Remplacez les paramètres Host, DBName, User et Password par les valeurs spécifiées lors de la création du serveur et de la base de données. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Npgsql;

namespace Driver
{
    public class AzurePostgresRead
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "mypgserver-20170401.postgres.database.azure.com";
        private static string User = "mylogin@mypgserver-20170401";
        private static string DBname = "mypgsqldb";
        private static string Password = "<server_admin_password>";
        private static string Port = "5432";

        static void Main(string[] args)
        {
            // Build connection string using parameters from portal
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};",
                    Host,
                    User,
                    DBname,
                    Port,
                    Password);

            var conn = new NpgsqlConnection(connString);

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText = "SELECT * FROM inventory;";

            var reader = command.ExecuteReader();
            while (reader.Read())
            {
                Console.WriteLine(
                    string.Format(
                        "Reading from table=({0}, {1}, {2})",
                        reader.GetInt32(0).ToString(),
                        reader.GetString(1),
                        reader.GetInt32(2).ToString()
                        )
                    );
            }

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```


<a id="update-data" class="xliff"></a>

## Mettre à jour des données
Utilisez le code suivant pour vous connecter et lire des données à l’aide d’une instruction SQL **UPDATE**. Le code utilise la classe NpgsqlCommand avec la méthode [Open()](http://www.npgsql.org/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_Open) pour établir une connexion à PostgreSQL. Le code utilise ensuite la méthode [CreateCommand()](http://www.npgsql.org/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_CreateCommand), définit la propriété CommandText et appelle la méthode [ExecuteNonQuery()](http://www.npgsql.org/api/Npgsql.NpgsqlCommand.html#Npgsql_NpgsqlCommand_ExecuteNonQuery) pour exécuter les commandes de la base de données.

Remplacez les paramètres Host, DBName, User et Password par les valeurs spécifiées lors de la création du serveur et de la base de données. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Npgsql;

namespace Driver
{
    public class AzurePostgresUpdate
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "mypgserver-20170401.postgres.database.azure.com";
        private static string User = "mylogin@mypgserver-20170401";
        private static string DBname = "mypgsqldb";
        private static string Password = "<server_admin_password>";
        private static string Port = "5432";

        static void Main(string[] args)
        {
            // Build connection string using parameters from portal
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};",
                    Host,
                    User,
                    DBname,
                    Port,
                    Password);

            var conn = new NpgsqlConnection(connString);

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText =
            String.Format("UPDATE inventory SET quantity = {0} WHERE name = {1};",
                200,
                "\'banana\'"
                );

            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows updated={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```


<a id="delete-data" class="xliff"></a>

## Suppression de données
Utilisez le code suivant pour vous connecter et lire des données à l’aide d’une instruction SQL **DELETE**. 

 Le code utilise la classe NpgsqlCommand avec la méthode [Open()](http://www.npgsql.org/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_Open) pour établir une connexion à PostgreSQL. Le code utilise ensuite la méthode [CreateCommand()](http://www.npgsql.org/api/Npgsql.NpgsqlConnection.html#Npgsql_NpgsqlConnection_CreateCommand), définit la propriété CommandText et appelle la méthode [ExecuteNonQuery()](http://www.npgsql.org/api/Npgsql.NpgsqlCommand.html#Npgsql_NpgsqlCommand_ExecuteNonQuery) pour exécuter les commandes de la base de données.

Remplacez les paramètres Host, DBName, User et Password par les valeurs spécifiées lors de la création du serveur et de la base de données. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Npgsql;

namespace Driver
{
    public class AzurePostgresDelete
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "mypgserver-20170401.postgres.database.azure.com";
        private static string User = "mylogin@mypgserver-20170401";
        private static string DBname = "mypgsqldb";
        private static string Password = "<server_admin_password>";
        private static string Port = "5432";

        static void Main(string[] args)
        {
            // Build connection string using parameters from portal
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};",
                    Host,
                    User,
                    DBname,
                    Port,
                    Password);

            var conn = new NpgsqlConnection(connString);

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText =
            String.Format("DELETE FROM inventory WHERE name = {0};",
                "\'orange\'");
            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows deleted={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
> [!div class="nextstepaction"]
> [Migration de votre base de données PostgreSQL par exportation et importation](./howto-migrate-using-export-and-import.md)

