---
title: "Bibliothèques de connexions de la base de données Azure pour PostgreSQL | Microsoft Docs"
description: "Liste chaque bibliothèque ou pilote que les programmes clients peuvent utiliser pour se connecter à la base de données Azure pour PostgreSQL."
keywords: azure cloud postgresql postgres
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql - database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6bb3f393a8e9533b86919e9a1ebe3490ab838714
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Bibliothèques de connexions de la base de données Azure pour PostgreSQL
Cette rubrique liste toutes les bibliothèques et tous les pilotes que les programmes clients peuvent utiliser pour se connecter à la base de données Azure pour PostgreSQL.

## <a name="client-interfaces"></a>Interfaces clients
La plupart des bibliothèques clientes de langages qui se connectent au serveur PostgreSQL sont des projets externes, distribués de manière indépendante. Ils sont pris en charge sur les plateformes Windows, Linux et Mac. Voici quelques-uns des pilotes clients les plus courants.
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
- Vous trouverez une vue d’ensemble du service à la page [Vue d’ensemble de la base de données Azure pour PostgreSQL](overview.md).
- Pour plus d’informations sur les serveurs, consultez la page [Base de données Azure pour les serveurs PostgreSQL](concepts-servers.md).
- Pour créer votre premier serveur PostgreSQL, consultez la page [Créer une base de données Azure pour PostgreSQL sur le Portail Azure](quickstart-create-server-database-portal.md).

