---
title: "Bibliothèques de connexions pour Azure Database pour MySQL | Microsoft Docs"
description: "Cet article répertorie chaque bibliothèque ou pilote que les programmes clients peuvent utiliser lors de la connexion à Azure Database pour MySQL."
services: mysql
author: mswutao
ms.author: wutao
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 06/08/2017
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: e746f28829063f8437ad408c4681f90aec6c0e0a
ms.contentlocale: fr-fr
ms.lasthandoff: 07/13/2017

---

# <a name="connection-libraries-for-azure-database-for-mysql"></a>Bibliothèques de connexions pour Azure Database pour MySQL
Cette rubrique répertorie chaque bibliothèque ou pilote que les programmes clients peuvent utiliser lors de la connexion à Azure Database pour MySQL.

## <a name="client-interfaces"></a>Interfaces client
MySQL offre une connectivité de pilote de base de données standard pour l’utilisation de MySQL avec des applications et outils qui sont compatibles avec les normes ODBC et JDBC du secteur. Tout système qui fonctionne avec ODBC ou JDBC peut utiliser MySQL.

| **Langage** | **Plateforme** | **Ressource supplémentaire** | **Télécharger** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Pilote natif MySQL pour PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Télécharger](http://php.net/downloads.php) |
| ODBC | Plateformes Windows, Linux, Mac OS X et Unix | [Guide de développement pour MySQL Connector/ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Télécharger](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Guide de développement pour MySQL Connector/Net](https://dev.mysql.com/doc/connector-net/en/) | [Télécharger](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Indépendant de la plateforme | [Guide de développement pour MySQL Connector/J 5.1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Télécharger](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Télécharger](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Guide de développement pour MySQL Connector/Python](https://dev.mysql.com/doc/connector-python/en/) | [Télécharger](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Guide de développement pour MySQL Connector/C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Télécharger](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Guide de développement pour MySQL Connector/C](https://dev.mysql.com/doc/connector-c/en/) | [Télécharger](https://dev.mysql.com/downloads/connector/c/)
| Perl | Plateformes Windows, Linux, Mac OS X et Unix | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Télécharger](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Étapes suivantes
[Vue d’ensemble d’Azure Database pour MySQL](./overview.md)
[Concepts serveur dans Azure Database pour MySQL](./concepts-servers.md)

