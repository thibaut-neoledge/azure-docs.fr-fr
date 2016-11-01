<properties
   pageTitle="Prise en charge des clients de niveau inférieur de SQL Data Warehouse pour l’audit des données | Microsoft Azure"
   description="En savoir plus sur la prise en charge des clients de niveau inférieur de SQL Data Warehouse pour l’audit des données"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/31/2016" 
   ms.author="rortloff;barbkess"/>


# <a name="sql-data-warehouse-downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>SQL Data Warehouse : prise en charge des clients de niveau inférieur pour l’audit et le masquage dynamique des données (Dynamic Data Masking)

[L’audit](sql-data-warehouse-auditing-overview.md) fonctionne avec les clients SQL qui prennent en charge la redirection TDS.

Tout client qui implémente TDS 7.4 doit également prendre en charge la redirection. Cependant, cette règle comporte deux exceptions : JDBC 4.0, qui ne prend pas complètement en charge la fonctionnalité de redirection et Tedious pour Node.JS, où la redirection n’a pas été implémentée.

Pour les « clients de niveau inférieur », c’est-à-dire ceux qui prennent en charge la version 7.3 de TDS et les versions antérieures, le nom de domaine complet du serveur dans la chaîne de connexion doit être modifié :

Nom de domaine complet du serveur d’origine dans la chaîne de connexion : <*nom du serveur*>.database.windows.net

Nom de domaine complet du serveur modifié dans la chaîne de connexion : <*nom du serveur*>.database.**secure**.windows.net

Voici une liste non exhaustive de « clients de niveau inférieur » :

- .NET 4.0 et versions antérieures
- ODBC 10.0 et versions antérieures
- JDBC (bien que JDBC prenne en charge la version 7.4 de TDS, la fonctionnalité de redirection TDS n’est pas entièrement prise en charge)
- Tedious (pour Node.JS)

**Remarque :** la modification des noms de domaines complets de serveur précédents peut aussi être utile pour appliquer une stratégie d’audit au niveau de SQL Server sans avoir à configurer chaque base de données (atténuation temporaire).     



<!--HONumber=Oct16_HO2-->


