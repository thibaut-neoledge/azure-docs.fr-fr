---
title: "Se connecter à la base de données Azure pour PostgreSQL à partir de Node.js | Microsoft Docs"
description: "Ce guide de démarrage rapide fournit un exemple de code Node.js, que vous pouvez utiliser pour vous connecter et interroger des données de la base de données Azure pour PostgreSQL."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 06/23/2017
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: ddc364f2a0b8a8bb0a4a2c3a563c007470415991
ms.contentlocale: fr-fr
ms.lasthandoff: 08/10/2017

---

# <a name="azure-database-for-postgresql-use-nodejs-to-connect-and-query-data"></a>Base de données Azure pour PostgreSQL : Utilisation de Node.js pour se connecter et interroger des données
Ce guide de démarrage rapide vous explique comment vous connecter à une base de données Azure pour PostgreSQL via [Node.js](https://nodejs.org/), à partir de plates-formes Windows, Ubuntu Linux et Mac. Il détaille l’utilisation d’instructions SQL pour interroger la base de données, la mettre à jour, y insérer des données ou en supprimer. Cet article suppose que vous connaissez les bases du développement via Node.js, et que vous ne savez pas utiliser la base de données Azure pour PostgreSQL.

## <a name="prerequisites"></a>Composants requis
Ce guide de démarrage rapide s’appuie sur les ressources créées dans l’un de ces guides :
- [Créer une base de données - Portail](quickstart-create-server-database-portal.md)
- [Créer une base de données - CLI](quickstart-create-server-database-azure-cli.md)

Il vous faudra également :
- Installez [Node.js](https://nodejs.org)

## <a name="install-pg-client"></a>Installer le client pg
Installez [pg](https://www.npmjs.com/package/pg), qui correspond à un client non bloquant JavaScript pur pour node.js, utile pour vous connecter à PostgreSQL et l’interroger.

Pour ce faire, exécutez le gestionnaire de package de nœud (npm) pour JavaScript depuis la ligne de commande, afin d’installer le client pg.
```bash
npm install pg
```

Vérifiez l’installation en répertoriant les packages installés.
```bash
npm list
```
La sortie de la commande liste confirme la version de chaque composant. 
```
`-- pg@6.2.3
  +-- buffer-writer@1.0.1
  +-- packet-reader@0.3.1
etc...
```

## <a name="get-connection-information"></a>Obtenir des informations de connexion
Obtenez les informations de connexion requises pour vous connecter à la base de données Azure pour PostgreSQL. Vous devez disposer du nom de serveur complet et des informations d’identification.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis recherchez le serveur **mypgserver-20170401** que vous venez de créer.
3. Cliquez sur le nom du serveur **mypgserver-20170401**.
4. Sélectionnez la page **Présentation** du serveur. Prenez note du **nom du serveur** et du **nom de connexion d’administrateur du serveur**.
 ![Base de données Azure pour PostgreSQL - Connexion d’administrateur du serveur](./media/connect-nodejs/1-connection-string.png)
5. Si vous avez oublié vos informations de connexion au serveur, accédez à la page **Vue d’ensemble** pour afficher le nom de connexion de l’administrateur du serveur et, si nécessaire, réinitialiser le mot de passe.

## <a name="running-the-javascript-code-in-nodejs"></a>Exécution du code JavaScript dans Node.js
Vous pouvez lancer Node.js à partir de l’interpréteur de commandes Bash ou de l’invite de commandes Windows, en saisissant `node`, puis exécutez l’exemple de code JavaScript de manière interactive, en le copiant et en le collant dans l’invite. Vous pouvez également enregistrer le code JavaScript dans un fichier texte, puis lancez `node filename.js` en indiquant le nom de fichier en tant que paramètre pour l’exécuter.

## <a name="connect-create-table-and-insert-data"></a>Se connecter, créer des tables et insérer des données
Utilisez le code suivant pour vous connecter et charger les données à l’aide d’instructions SQL **CREATE TABLE** et **INSERT INTO**.
L’objet [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) est utilisé pour créer une interface avec le serveur PostgreSQL. La fonction [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) est utilisée pour établir la connexion avec le serveur. La fonction [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) est utilisée pour exécuter la requête SQL sur la base de données PostgreSQL. 

Remplacez les paramètres host, dbname, user et password par les valeurs spécifiées lors de la création du serveur et de la base de données.

```javascript
const pg = require('pg');

const config = {
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: '<name_of_database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DROP TABLE IF EXISTS inventory;
        CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
        INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
        INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
        INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    `;

    client
        .query(query)
        .then(() => {
            console.log('Table created successfully!');
            client.end(console.log('Closed client connection'));
        })
        .catch(err => console.log(err))
        .then(() => {
            console.log('Finished execution, exiting now');
            process.exit();
        });
}
```

## <a name="read-data"></a>Lire les données
Utilisez le code suivant pour vous connecter et lire des données à l’aide d’une instruction SQL **SELECT**. L’objet [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) est utilisé pour créer une interface avec le serveur PostgreSQL. La fonction [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) est utilisée pour établir la connexion avec le serveur. La fonction [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) est utilisée pour exécuter la requête SQL sur la base de données PostgreSQL. 

Remplacez les paramètres host, dbname, user et password par les valeurs spécifiées lors de la création du serveur et de la base de données. 

```javascript
const pg = require('pg');

const config = {
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: '<name_of_database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else { queryDatabase(); }
});

function queryDatabase() {
  
    console.log(`Running query to PostgreSQL server: ${config.host}`);

    const query = 'SELECT * FROM inventory;';

    client.query(query)
        .then(res => {
            const rows = res.rows;

            rows.map(row => {
                console.log(`Read: ${JSON.stringify(row)}`);
            });

            process.exit();
        })
        .catch(err => {
            console.log(err);
        });
}
```

## <a name="update-data"></a>Mettre à jour des données
Utilisez le code suivant pour vous connecter et lire des données à l’aide d’une instruction SQL **UPDATE**. L’objet [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) est utilisé pour créer une interface avec le serveur PostgreSQL. La fonction [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) est utilisée pour établir la connexion avec le serveur. La fonction [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) est utilisée pour exécuter la requête SQL sur la base de données PostgreSQL. 

Remplacez les paramètres host, dbname, user et password par les valeurs spécifiées lors de la création du serveur et de la base de données. 

```javascript
const pg = require('pg');

const config = {
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: '<name_of_database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `UPDATE inventory 
                   SET quantity= 1000 WHERE name='banana';`;

    client
        .query(query)
        .then(() => {
            console.log('Update completed succesfully!');
            client.end(console.log('Closed client connection'));
        })
        .catch(err => console.log(err))
        .then(() => {
            console.log('Finished execution, exiting now');
            process.exit();
        });
}
```

## <a name="delete-data"></a>Suppression de données
Utilisez le code suivant pour vous connecter et lire des données à l’aide d’une instruction SQL **DELETE**. L’objet [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) est utilisé pour créer une interface avec le serveur PostgreSQL. La fonction [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) est utilisée pour établir la connexion avec le serveur. La fonction [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) est utilisée pour exécuter la requête SQL sur la base de données PostgreSQL. 

Remplacez les paramètres host, dbname, user et password par les valeurs spécifiées lors de la création du serveur et de la base de données. 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }   
});

function queryDatabase()
{
    client.query('DELETE FROM inventory WHERE name=\'apple\';', function (err, result)
    {
        console.log("Connection established");
        
        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;
                
                // Else closing connection finished without error
                console.log("Closed client connection");
            });

            console.log("Finished execution, exiting now");
            process.exit()          
        }
    }); 
}
```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Migration de votre base de données PostgreSQL par exportation et importation](./howto-migrate-using-export-and-import.md)

