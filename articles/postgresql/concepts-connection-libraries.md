---
title: "Bibliothèques de connexions de la base de données Azure pour PostgreSQL | Microsoft Docs"
description: "Cet article décrit plusieurs bibliothèques et pilotes que les développeurs peuvent utiliser lorsqu’ils codent des applications pour se connecter à une base de données Azure pour PostgreSQL et l’interroger."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.topic: article
ms.date: 06/15/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 7099e26fd749820ab6869c0262e1f97d993ab7b2
ms.contentlocale: fr-fr
ms.lasthandoff: 06/17/2017

---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Bibliothèques de connexions de la base de données Azure pour PostgreSQL
Cette rubrique répertorie les bibliothèques et pilotes que les développeurs peuvent utiliser lorsqu’ils programment des applications pour se connecter à une base de données Azure pour PostgreSQL et l’interroger.

## <a name="client-interfaces"></a>Interfaces clients
La plupart des bibliothèques clientes de langages qui se connectent au serveur PostgreSQL sont des projets externes, distribués de manière indépendante. Ils sont pris en charge sur les plateformes Windows, Linux et Mac. Voici quelques-uns des pilotes clients les plus courants :

| **Langage** | **Interface client** | **Informations supplémentaires** | **Télécharger** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Compatible DB API 2.0 | [Télécharger](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://php.net/manual/en/book.pgsql.php) | Extension de base de données | [Installer](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Package pg npm](https://www.npmjs.com/package/pg) | Client non bloquant JavaScript pur | [Installer](https://www.npmjs.com/package/pg) |
| Java | [JDBC](http://jdbc.postgresql.org/) | Pilote JDBC de type 4 | [Télécharger](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Interface Ruby | [Télécharger](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Package pq](https://godoc.org/github.com/lib/pq) | Pilote Go Postgres pur | [Installer](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](http://www.npgsql.org/) | Fournisseur de données ADO.NET | [Télécharger](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Pilote ODBC | [Télécharger](http://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Interface principale du langage C | Inclus |
| C++ | [libpqxx](http://pqxx.org/) | Interface C++ remaniée | [Télécharger](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Étapes suivantes
Lisez ces guides de démarrage rapide expliquant comment vous connecter à une base de données Azure pour PostgreSQL et l’interroger à l’aide du langage de votre choix :

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [.NET (C#)](./connect-csharp.md)

