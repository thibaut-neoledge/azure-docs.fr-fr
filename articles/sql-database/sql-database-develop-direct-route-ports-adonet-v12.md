---
title: Ports au-delà de 1433 pour SQL Database | Microsoft Docs
description: Parfois, les connexions clientes entre ADO.NET et Azure SQL Database V12 ignorent le proxy et interagissent directement avec la base de données. Les ports autres que le port 1433 deviennent importants.
services: sql-database
documentationcenter: ''
author: MightyPen
manager: jhubbard
editor: ''

ms.service: sql-database
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: annemill

---
# Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database V12
Cette rubrique décrit les modifications qu’apporte la base de données SQL Azure V12 au comportement de connexion des clients qui utilisent ADO.NET version 4.5 ou ultérieure.

## V11 de SQL Database : port 1433
Quand votre programme client utilise ADO.NET 4.5 pour se connecter à SQL Database V11 et l’utiliser pour exécuter des requêtes, la séquence interne est la suivante :

1. ADO.NET tente de se connecter à SQL Database.
2. ADO.NET utilise le port 1433 pour appeler un module d’intergiciel (middleware), puis l’intergiciel se connecte à SQL Database.
3. SQL Database envoie sa réponse à l’intergiciel, qui la transmet à ADO.NET sur le port 1433.

**Terminologie :** dans la séquence précédente, ADO.NET interagit avec la base de données SQL en utilisant l’*itinéraire de proxy*. Si aucun intergiciel n’était impliqué, c’est *l’itinéraire direct* qui serait utilisé.

## V12 de SQL Database : exécution externe ou exécution interne
Pour les connexions à V12, nous devons déterminer si le programme client s’exécute *à l’extérieur* ou *à l’intérieur* de la limite du cloud Azure. Les sous-sections suivantes abordent deux scénarios courants.

#### *À l’extérieur :* le client s’exécute sur votre ordinateur de bureau
Le port 1433 est le seul port qui doit être ouvert sur votre ordinateur de bureau qui héberge votre application cliente SQL Database.

#### *À l’intérieur :* le client s’exécute sur Azure
Quand votre client s’exécute à l’intérieur de la limite du cloud Azure, il utilise ce que nous pouvons appeler un *itinéraire direct* pour interagir avec le serveur de la base de données SQL. Une fois une connexion établie, les interactions suivantes entre le client et la base de données n’impliquent aucun proxy d’intergiciel.

La séquence est la suivante :

1. ADO.NET 4.5 (ou version ultérieure) initie une brève interaction avec le cloud Azure et reçoit un numéro de port identifié de manière dynamique.
   
   * Le numéro de port identifié de manière dynamique appartient à la plage 11000-11999 ou 14000-14999.
2. ADO.NET se connecte ensuite au serveur SQL Database directement, sans passer par un intergiciel.
3. Les requêtes sont envoyées directement à la base de données et les résultats sont retournés directement au client.

Assurez-vous que les plages de ports 11000-11999 et 14000-14999 sur votre ordinateur client Azure restent disponibles pour les interactions client ADO.NET 4.5 avec SQL Database V12.

* En particulier, les ports dans la plage doivent être libres de tout autre bloqueur sortant.
* Sur votre machine virtuelle Azure, le **Pare-feu Windows avec fonctions avancées de sécurité** contrôle les paramètres des ports.
  
  * Vous pouvez utiliser l’[interface utilisateur du Pare-feu](http://msdn.microsoft.com/library/cc646023.aspx) pour ajouter une règle dans laquelle vous spécifiez le protocole **TCP** et une plage de ports avec une syntaxe semblable à **11000-11999**.

## Précisions concernant les versions
Cette section clarifie les monikers qui font référence aux versions du produit. Elle répertorie également certaines paires de versions entre les produits.

#### ADO.NET
* ADO.NET 4.0 prend en charge le protocole TDS 7.3, mais pas la version 7.4.
* Les versions d’ADO.NET 4.5 et ultérieures prennent en charge le protocole TDS 7.4.

#### SQL Database V11 et SQL Database V12
Les différences entre les bases de données SQL V11 et V12 pour la connexion cliente sont soulignées dans cette rubrique.

*Remarque :* l’instruction Transact-SQL `SELECT @@version;` retourne une valeur qui commence par un nombre, tel que « 11. » ou « 12. », qui correspond aux noms de version V11 et V12 de SQL Database.

## Liens connexes
* ADO.NET 4.6 a été publié le 20 juillet 2015. Une annonce dans un billet de blog de l’équipe .NET est disponible [ici](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).
* ADO.NET 4.5 a été publié le 15 août 2012. Une annonce dans un billet de blog de l’équipe .NET est disponible [ici](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
  
  * Un billet de blog sur ADO.NET 4.5.1 est disponible [ici](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).
* [Liste des versions du protocole TDS](http://www.freetds.org/userguide/tdshistory.htm)
* [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md)
* [Pare-feu Azure SQL Database](sql-database-firewall-configure.md)
* [Configuration des paramètres du pare-feu sur une base de données SQL](sql-database-configure-firewall-settings.md)

<!---HONumber=AcomDC_0817_2016-->