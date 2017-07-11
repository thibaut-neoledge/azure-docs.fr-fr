---
title: "Se connecter à la base de données Azure pour PostgreSQL à partir de Python | Microsoft Docs"
description: "Ce guide de démarrage rapide fournit un exemple de code Python, que vous pouvez utiliser pour vous connecter et interroger des données de la base de données Azure pour PostgreSQL."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: python
ms.topic: hero-article
ms.date: 06/23/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 0027d25bcaa3376c5a29299f3ec88809ebf1d2d8
ms.contentlocale: fr-fr
ms.lasthandoff: 06/26/2017

---
<a id="azure-database-for-postgresql-use-python-to-connect-and-query-data" class="xliff"></a>

# Base de données Azure pour PostgreSQL : Utilisation de Python pour se connecter et interroger des données
Ce guide de démarrage rapide indique comment utiliser [Python](https://python.org) pour se connecter à une base de données SQL Azure pour PostgreSQL, puis utiliser des instructions SQL pour interroger, insérer, mettre à jour et supprimer des données dans la base de données à partir des plateformes Mac OS, Ubuntu Linux et Windows. Cet article suppose que vous connaissez les bases du développement via Python, et que vous ne savez pas utiliser la base de données Azure pour PostgreSQL.

<a id="prerequisites" class="xliff"></a>

## Composants requis
Ce guide de démarrage rapide s’appuie sur les ressources créées dans l’un de ces guides :
- [Créer une base de données - Portail](quickstart-create-server-database-portal.md)
- [Créer une base de données - CLI](quickstart-create-server-database-azure-cli.md)

Vous aurez également besoin des éléments suivants :
- Installation de [python](https://www.python.org/downloads/)
- Installation du package [pip](https://pip.pypa.io/en/stable/installing/) (déjà installé si vous utilisez des binaires Python 2 >=2.7.9 ou Python 3 >=3.4 téléchargés depuis [python.org](https://python.org), mais vous devez mettre pip à niveau)

<a id="install-the-python-connection-libraries-for-postgresql" class="xliff"></a>

## Installer des bibliothèques de connexions Python pour PostgreSQL
Installez le package [psycopg2](http://initd.org/psycopg/docs/install.html), qui vous permet de vous connecter et d’interroger la base de données. Le package psycopg2 est [disponible sur PyPI](https://pypi.python.org/pypi/psycopg2/), sous la forme de packages [wheel](http://pythonwheels.com/) pour les plates-formes les plus courantes (Linux, OSX ou Windows). Vous pouvez donc utiliser l’installation de pip pour obtenir la version du binaire du module, avec toutes les dépendances :

```cmd
pip install psycopg2
```
Vérifiez que vous utilisez une version actualisée de pip (vous pouvez la mettre à niveau à l’aide d’un élément tel que `pip install -U pip`)

<a id="get-connection-information" class="xliff"></a>

## Obtenir des informations de connexion
Obtenez les informations de connexion requises pour vous connecter à la base de données Azure pour PostgreSQL. Vous devez disposer du nom de serveur complet et des informations d’identification.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis recherchez le serveur **mypgserver-20170401** que vous venez de créer.
3. Cliquez sur le nom du serveur **mypgserver-20170401**.
4. Sélectionnez la page **Présentation** du serveur. Prenez note du **nom du serveur** et du **nom de connexion d’administrateur du serveur**.
 ![Base de données Azure pour PostgreSQL - Connexion d’administrateur du serveur](./media/connect-python/1-connection-string.png)
5. Si vous avez oublié vos informations de connexion au serveur, accédez à la page **Vue d’ensemble** pour afficher le nom de connexion de l’administrateur du serveur et, si nécessaire, réinitialiser le mot de passe.
   
<a id="connect-create-table-and-insert-data" class="xliff"></a>

## Se connecter, créer des tables et insérer des données
Utilisez le code suivant pour vous connecter et charger les données à l’aide de la fonction [psycopg2.connect](http://initd.org/psycopg/docs/connection.html), avec l’instruction SQL **INSERT**. La fonction [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) est utilisée pour exécuter la requête SQL sur la base de données PostgreSQL. Remplacez les paramètres host, dbname, user et password par les valeurs spécifiées lors de la création du serveur et de la base de données.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print "Finished dropping table (if existed)"

# Create table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print "Finished creating table"

# Insert some data into table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print "Inserted 3 rows of data"

conn.commit()
```

<a id="read-data" class="xliff"></a>

## Lire les données
Utilisez le code suivant pour lire les données insérées à l’aide de la fonction [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute), avec l’instruction SQL **SELECT**. Cette fonction accepte une requête et renvoie un jeu de résultats qui peut être itéré à l’aide de [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall). Remplacez les paramètres host, dbname, user et password par les valeurs spécifiées lors de la création du serveur et de la base de données.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print "Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2]))

conn.commit()
```

<a id="update-data" class="xliff"></a>

## Mettre à jour des données
Utilisez le code suivant pour mettre à jour la ligne d’inventaire que vous avez insérée précédemment à l’aide de la fonction [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) et de l’instruction SQL **UPDATE**. Remplacez les paramètres host, dbname, user et password par les valeurs spécifiées lors de la création du serveur et de la base de données.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print "Updated 1 row of data"

conn.commit()
```

<a id="delete-data" class="xliff"></a>

## Suppression de données
Utilisez le code suivant pour supprimer une ligne d’inventaire que vous avez insérée précédemment à l’aide de la fonction [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) et de l’instruction SQL **DELETE**. Remplacez les paramètres host, dbname, user et password par les valeurs spécifiées lors de la création du serveur et de la base de données.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mypgserver-20170401.postgres.database.azure.com"
user = "mylogin@mypgserver-20170401"
dbname = "mypgsqldb"
password = "<server_admin_password>"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3}".format(host, user, dbname, password)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print ("Deleted 1 row of data")

conn.commit()
```

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
> [!div class="nextstepaction"]
> [Migration de votre base de données PostgreSQL par exportation et importation](./howto-migrate-using-export-and-import.md)

