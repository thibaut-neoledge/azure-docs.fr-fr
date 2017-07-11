---
title: "Se connecter à la base de données Azure pour PostgreSQL à l’aide du langage Go | Microsoft Docs"
description: "Ce guide de démarrage rapide fournit un exemple de langage de programmation Go, que vous pouvez utiliser pour vous connecter et interroger des données de la base de données Azure pour PostgreSQL."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: go
ms.topic: hero-article
ms.date: 06/29/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: f3e7e57993b5253a895640854672da431c00854b
ms.contentlocale: fr-fr
ms.lasthandoff: 07/04/2017

---

<a id="azure-database-for-postgresql-use-go-language-to-connect-and-query-data" class="xliff"></a>

# Base de données Azure pour PostgreSQL : Utilisation du langage Go pour se connecter et interroger des données
Ce guide de démarrage rapide vous explique comment vous connecter à une base de données Azure pour PostgreSQL en utilisant du code écrit dans le langage [Go](https://golang.org/). Il détaille l’utilisation d’instructions SQL pour interroger la base de données, la mettre à jour, y insérer des données ou en supprimer. Cet article suppose que vous connaissez les bases du développement à l’aide de Go, mais que vous ne connaissez pas la base de données Azure pour PostgreSQL.

<a id="prerequisites" class="xliff"></a>

## Composants requis
Ce guide de démarrage rapide s’appuie sur les ressources créées dans l’un de ces guides :
- [Créer une base de données - Portail](quickstart-create-server-database-portal.md)
- [Créer une base de données - Interface de ligne de commande Azure](quickstart-create-server-database-azure-cli.md)

<a id="install-go-and-pq" class="xliff"></a>

## Installer Go et pq
- Téléchargez et installez Go en fonction des [instructions d’installation](https://golang.org/doc/install) correspondant à votre plateforme.
- Créez un dossier pour votre projet, comme C:\Postgresql\. À l’aide de la ligne de commande, placez-vous dans le dossier du projet, par exemple `cd C:\Postgres\`.
- Téléchargez le [pilote Pure Go Postgres (pq)](https://github.com/lib/pq) dans votre dossier de projet en tapant la commande `go get github.com/lib/pq` tout en restant dans le même répertoire.

<a id="build-and-run-go-code" class="xliff"></a>

## Générer et exécuter du code Go 
- Enregistrez le code dans un fichier texte portant l’extension *.go, puis enregistrez-le dans votre dossier de projet, par exemple `C:\postgres\read.go`.
- Pour exécuter le code, remplacez le répertoire dans votre dossier de projet `cd postgres`, puis saisissez la commande `go run read.go` pour compiler l’application et l’exécuter.
- Pour générer le code dans une application native, `go build read.go`, lancez read.exe afin d’exécuter l’application.

<a id="get-connection-information" class="xliff"></a>

## Obtenir des informations de connexion
Obtenez les informations de connexion requises pour vous connecter à la base de données Azure pour PostgreSQL. Vous devez disposer du nom de serveur complet et des informations d’identification.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis recherchez le serveur que vous venez de créer, par exemple **mypgserver-20170401**.
3. Cliquez sur le nom du serveur **mypgserver-20170401**.
4. Sélectionnez la page **Présentation** du serveur. Prenez note du **nom du serveur** et du **nom de connexion d’administrateur du serveur**.
 ![Base de données Azure pour PostgreSQL - Connexion d’administrateur du serveur](./media/connect-go/1-connection-string.png)
5. Si vous avez oublié vos informations de connexion au serveur, accédez à la page **Vue d’ensemble** et affichez le nom de connexion de l’administrateur du serveur. Si nécessaire, réinitialisez le mot de passe.

<a id="connect-and-create-a-table" class="xliff"></a>

## Se connecter et créer une table
Utilisez le code suivant pour vous connecter et créer une table à l’aide de l’instruction **CREATE TABLE**, suivie des instructions SQL **INSERT INTO** pour ajouter des lignes à la table.

Le code importe trois packages : le [package sql](https://golang.org/pkg/database/sql/), le [package pq](http://godoc.org/github.com/lib/pq) qui sert de pilote pour communiquer avec le serveur Postgres, et le [package fmt](https://golang.org/pkg/fmt/) pour les entrées et sorties imprimées sur la ligne de commande.

Le code appelle la méthode [sql.Open()](http://godoc.org/github.com/lib/pq#Open) afin de se connecter à la base de données Azure pour PostgreSQL et vérifie la connexion à l’aide de la méthode [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Un [descripteur de la base de données](https://golang.org/pkg/database/sql/#DB) est utilisé partout : il contient le pool de connexions pour le serveur de base de données. Le code appelle la méthode [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) plusieurs fois pour exécuter plusieurs commandes SQL. À chaque fois, utilisez une méthode checkError() personnalisée pour vérifier si une erreur s’est produite. Si tel est le cas, dépêchez-vous de quitter l’application.


Remplacez les paramètres `HOST`, `DATABASE`, `USER` et `PASSWORD` par vos propres valeurs. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed)")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table")

    // Insert some data into table.
    sql_statement := "INSERT INTO inventory (name, quantity) VALUES ($1, $2);"
    _, err = db.Exec(sql_statement, "banana", 150)
    checkError(err)
    _, err = db.Exec(sql_statement, "orange", 154)
    checkError(err)
    _, err = db.Exec(sql_statement, "apple", 100)
    checkError(err)
    fmt.Println("Inserted 3 rows of data")
}
```

<a id="read-data" class="xliff"></a>

## Lire les données
Utilisez le code suivant pour vous connecter et lire des données à l’aide d’une instruction SQL **SELECT**. 

Le code importe trois packages : le [package sql](https://golang.org/pkg/database/sql/), le [package pq](http://godoc.org/github.com/lib/pq) qui sert de pilote pour communiquer avec le serveur Postgres, et le [package fmt](https://golang.org/pkg/fmt/) pour les entrées et sorties imprimées sur la ligne de commande.

Le code appelle la méthode [sql.Open()](http://godoc.org/github.com/lib/pq#Open) afin de se connecter à la base de données Azure pour PostgreSQL et vérifie la connexion à l’aide de la méthode [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Un [descripteur de la base de données](https://golang.org/pkg/database/sql/#DB) est utilisé partout : il contient le pool de connexions pour le serveur de base de données. La requête Select est exécutée par le biais d’un appel à la méthode [db.Query()](https://golang.org/pkg/database/sql/#DB.Query), et les lignes obtenues sont conservées dans une variable de type [lignes](https://golang.org/pkg/database/sql/#Rows). Le code lit les valeurs de données de la colonne dans la ligne actuelle à l’aide de la méthode [rows.Scan()](https://golang.org/pkg/database/sql/#Rows.Scan) et effectue une boucle sur les lignes à l’aide de l’itérateur [rows.Next()](https://golang.org/pkg/database/sql/#Rows.Next) jusqu’à la suppression de toutes les lignes. Les valeurs de colonne de chaque ligne sont imprimées en sortie sur la console. À chaque fois, utilisez une méthode checkError() personnalisée pour vérifier si une erreur s’est produite. Si tel est le cas, dépêchez-vous de quitter l’application.

Remplacez les paramètres `HOST`, `DATABASE`, `USER` et `PASSWORD` par vos propres valeurs. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Read rows from table.
    var id int
    var name string
    var quantity int

    sql_statement := "SELECT * from inventory;"
    rows, err := db.Query(sql_statement)
    checkError(err)

    for rows.Next() {
        switch err := rows.Scan(&id, &name, &quantity); err {
        case sql.ErrNoRows:
            fmt.Println("No rows were returned")
        case nil:
            fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
        default:
            checkError(err)
        }
    }
}
```

<a id="update-data" class="xliff"></a>

## Mettre à jour des données
Utilisez le code suivant pour vous connecter et mettre à jour les données à l’aide d’une instruction SQL **UPDATE**.

Le code importe trois packages : le [package sql](https://golang.org/pkg/database/sql/), le [package pq](http://godoc.org/github.com/lib/pq) qui sert de pilote pour communiquer avec le serveur Postgres, et le [package fmt](https://golang.org/pkg/fmt/) pour les entrées et sorties imprimées sur la ligne de commande.

Le code appelle la méthode [sql.Open()](http://godoc.org/github.com/lib/pq#Open) afin de se connecter à la base de données Azure pour PostgreSQL et vérifie la connexion à l’aide de la méthode [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Un [descripteur de la base de données](https://golang.org/pkg/database/sql/#DB) est utilisé partout : il contient le pool de connexions pour le serveur de base de données. Le code appelle la méthode [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) pour exécuter l’instruction SQL qui met à jour la table. Utilisez une méthode checkError() personnalisée pour vérifier si une erreur s’est produite. Si tel est le cas, dépêchez-vous de quitter l’application.

Remplacez les paramètres `HOST`, `DATABASE`, `USER` et `PASSWORD` par vos propres valeurs. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Modify some data in table.
    sql_statement := "UPDATE inventory SET quantity = $2 WHERE name = $1;"
    _, err = db.Exec(sql_statement, "banana", 200)
    checkError(err)
    fmt.Println("Updated 1 row of data")
}
```

<a id="delete-data" class="xliff"></a>

## Suppression de données
Utilisez le code suivant pour vous connecter et lire des données à l’aide d’une instruction SQL **DELETE**. 

Le code importe trois packages : le [package sql](https://golang.org/pkg/database/sql/), le [package pq](http://godoc.org/github.com/lib/pq) qui sert de pilote pour communiquer avec le serveur Postgres, et le [package fmt](https://golang.org/pkg/fmt/) pour les entrées et sorties imprimées sur la ligne de commande.

Le code appelle la méthode [sql.Open()](http://godoc.org/github.com/lib/pq#Open) afin de se connecter à la base de données Azure pour PostgreSQL et vérifie la connexion à l’aide de la méthode [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Un [descripteur de la base de données](https://golang.org/pkg/database/sql/#DB) est utilisé partout : il contient le pool de connexions pour le serveur de base de données. Le code appelle la méthode [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) pour exécuter l’instruction SQL qui met à jour la table. Utilisez une méthode checkError() personnalisée pour vérifier si une erreur s’est produite. Si tel est le cas, dépêchez-vous de quitter l’application.

Remplacez les paramètres `HOST`, `DATABASE`, `USER` et `PASSWORD` par vos propres valeurs. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Delete some data from table.
    sql_statement := "DELETE FROM inventory WHERE name = $1;"
    _, err = db.Exec(sql_statement, "orange")
    checkError(err)
    fmt.Println("Deleted 1 row of data")
}
```

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
> [!div class="nextstepaction"]
> [Migration de votre base de données PostgreSQL par exportation et importation](./howto-migrate-using-export-and-import.md)

