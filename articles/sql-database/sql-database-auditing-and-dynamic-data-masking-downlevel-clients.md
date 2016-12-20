---
title: "Prise en charge des clients de niveau inférieur de SQL Database et modification des points de terminaison IP à des fins d’audit | Microsoft Docs"
description: "En savoir plus sur la prise en charge des clients de niveau inférieur de la base de données SQL et sur la modification des points de terminaison IP à des fins d’audit."
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: 
ms.assetid: 4ef19ed1-e798-43a2-ad99-0e563f93ab53
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2016
ms.author: ronitr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 06c1dfbcfa365f6c34708021f63a756e295e2ab4


---
# <a name="sql-database---downlevel-clients-support-and-ip-endpoint-changes-for-auditing"></a>SQL Database : prise en charge des clients de niveau inférieur et modification des points de terminaison IP à des fins d’audit
[audit](sql-database-auditing-get-started.md) fonctionne automatiquement avec les clients SQL qui prennent en charge la redirection TDS.

## <a name="a-idsubheading-1adownlevel-clients-support"></a><a id="subheading-1"></a>Prise en charge des clients de niveau inférieur
Tout client qui implémente TDS 7.4 doit également prendre en charge la redirection. Cependant, cette règle comporte deux exceptions : JDBC 4.0, qui ne prend pas complètement en charge la fonctionnalité de redirection et Tedious pour Node.JS, où la redirection n’a pas été implémentée.

Pour les « clients de niveau inférieur », c’est-à-dire ceux qui prennent en charge la version 7.3 de TDS et les versions antérieures, le nom de domaine complet du serveur dans la chaîne de connexion doit être modifié :

Nom de domaine complet du serveur d’origine dans la chaîne de connexion : <*nom du serveur*>.database.windows.net

Nom de domaine complet du serveur modifié dans la chaîne de connexion : <*nom du serveur*>.database.**secure**.windows.net

Voici une liste non exhaustive de « clients de niveau inférieur » :

* .NET 4.0 et versions antérieures
* ODBC 10.0 et versions antérieures
* JDBC (bien que JDBC prenne en charge la version 7.4 de TDS, la fonctionnalité de redirection TDS n’est pas entièrement prise en charge)
* Tedious (pour Node.JS)

**Remarque :** la modification des noms de domaines complets de serveur précédents peut aussi être utile pour appliquer une stratégie d’audit au niveau de SQL Server sans avoir à configurer chaque base de données (atténuation temporaire).

## <a name="a-idsubheading-2aip-endpoint-changes-when-enabling-auditing"></a><a id="subheading-2"></a>Modification des points de terminaison IP lors de l’activation de l’audit
Notez que l’activation de l’audit a pour effet de modifier le point de terminaison IP de votre base de données. Si vous avez défini des paramètres de pare-feu stricts, mettez-les à jour en conséquence.

Le nouveau point de terminaison IP de la base de données dépend de la région de votre base de données :

| Région de base de données | Points de terminaison IP possibles |
| --- | --- |
| Chine du Nord |139.217.29.176, 139.217.28.254 |
| Chine orientale |42.159.245.65, 42.159.246.245 |
| Est de l’Australie |104.210.91.32, 40.126.244.159, 191.239.64.60, 40.126.255.94 |
| Sud-Est de l’Australie |191.239.184.223, 40.127.85.81, 191.239.161.83, 40.127.81.130 |
| Sud du Brésil |104.41.44.161, 104.41.62.230, 23.97.99.54, 104.41.59.191 |
| Centre des États-Unis |104.43.255.70, 40.83.14.7, 23.99.128.244, 40.83.15.176 |
| Est de l'Asie |23.99.125.133, 13.75.40.42, 23.97.71.138, 13.94.43.245 |
| Est des États-Unis 2 |104.209.141.31, 104.208.238.177, 191.237.131.51, 104.208.235.50 |
| Est des États-Unis |23.96.107.223, 104.41.150.122, 23.96.38.170, 104.41.146.44 |
| Inde centrale |104.211.98.219, 104.211.103.71 |
| Inde du Sud |104.211.227.102, 104.211.225.157 |
| Inde occidentale |104.211.161.152, 104.211.162.21 |
| Est du Japon |104.41.179.1, 40.115.253.81, 23.102.64.207, 40.115.250.196 |
| Ouest du Japon |104.214.140.140, 104.214.146.31, 191.233.32.34, 104.214.146.198 |
| États-Unis - partie centrale septentrionale |191.236.155.178, 23.96.192.130, 23.96.177.169, 23.96.193.231 |
| Europe du Nord |104.41.209.221, 40.85.139.245, 137.116.251.66, 40.85.142.176 |
| Centre-Sud des États-Unis |191.238.184.128, 40.84.190.84, 23.102.160.153, 40.84.186.66 |
| Asie du Sud-Est |104.215.198.156, 13.76.252.200, 23.97.51.109, 13.76.252.113 |
| Europe de l'Ouest |104.40.230.120, 13.80.23.64, 137.117.171.161, 13.80.8.37, 104.47.167.215, 40.118.56.193, 104.40.176.73, 40.118.56.20 |
| Ouest des États-Unis |191.236.123.146, 138.91.163.240, 168.62.194.148, 23.99.6.91 |
| Centre du Canada |13.88.248.106 |
| Est du Canada |40.86.227.82 |




<!--HONumber=Nov16_HO3-->


