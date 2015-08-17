<properties 
	pageTitle="Ports au-delà de 1433 pour ADO.NET 4.5, ODBC 11 et SQL Database V12 | Microsoft Azure"
	description="Parfois, les connexions clientes à Azure SQL Database V12 ignorent le proxy et interagissent directement avec la base de données. Les ports autres que le port 1433 deviennent importants."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor="" />


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/05/2015" 
	ms.author="genemi"/>


# Ports au-delà de 1433 pour ADO.NET 4.5, ODBC 11 et SQL Database V12


Cette rubrique décrit les modifications qu’apporte Azure SQL Database V12 au comportement de connexion des clients qui utilisent ADO.NET version 4.5 ou ultérieure.


## Précisions concernant les versions


#### ADO.NET


- ADO.NET 4.0 prend en charge le protocole TDS 7.3, mais pas la version 7.4.
- Les versions d’ADO.NET 4.5 et ultérieures prennent en charge le protocole TDS 7.4.
- En interne, ADO.NET 4.5 utilise ODBC 11.
 - Les informations indiquées ici qui s’appliquent à ADO.NET 4.5 concernent également ODBC 11.


#### SQL Database V11 et SQL Database V12


Les différences entre SQL Database V11 et SQL Database V12 pour la connexion cliente sont soulignées dans cette rubrique.


*Remarque :* l’instruction Transact-SQL `SELECT @@version;` retourne une valeur qui commence par un nombre, tel que « 11. » ou «12.», qui correspond aux noms de version V11 et V12 pour SQL Database.


## V11 de SQL Database : port 1433


Quand votre programme client utilise ADO.NET 4.5 pour se connecter à SQL Database V11 et l’utiliser pour exécuter des requêtes, la séquence interne est la suivante :


1. ADO.NET tente de se connecter à SQL Database.

2. ADO.NET utilise le port 1433 pour appeler un module d’intergiciel (middleware), puis l’intergiciel se connecte à SQL Database.

3. SQL Database envoie sa réponse à l’intergiciel, qui la transmet à ADO.NET sur le port 1433.


**Terminologie :** dans la séquence précédente, ADO.NET interagit avec SQL Database en utilisant l’*itinéraire de proxy*. Si aucun intergiciel n’était impliqué, c’est l’*itinéraire direct* qui serait utilisé.


## V12 de SQL Database : exécution externe ou exécution interne


Pour les connexions à V12, nous devons déterminer si le programme client s’exécute *à l’extérieur* ou *à l’intérieur* de la limite du cloud Azure. Les sous-sections suivantes abordent deux scénarios courants.


#### *À l’extérieur :* le client s’exécute sur votre ordinateur de bureau


Le port 1433 est le seul port qui doit être ouvert sur votre ordinateur de bureau qui héberge votre application cliente SQL Database.


#### *À l’intérieur :* le client s’exécute sur une machine virtuelle Azure


Quand votre client s’exécute à l’intérieur de la limite du cloud Azure, il utilise ce que nous pouvons appeler un *itinéraire direct* pour interagir avec le serveur SQL Database. Une fois une connexion établie, les interactions suivantes entre le client et la base de données n’impliquent aucun proxy d’intergiciel.


La séquence est la suivante :


1. ADO.NET 4.5 (ou version ultérieure) initie une brève interaction avec le cloud Azure et reçoit un numéro de port identifié de manière dynamique.
 - Le numéro de port identifié de manière dynamique appartient à la plage 11000-11999.

2. ADO.NET se connecte ensuite au serveur SQL Database directement, sans passer par un intergiciel.

3. Les requêtes sont envoyées directement à la base de données et les résultats sont retournés directement au client.


Assurez-vous que la plage de ports 11000-11999 sur votre ordinateur client Azure reste disponible pour les interactions client ADO.NET 4.5 avec SQL Database V12.

- En particulier, les ports dans la plage doivent être libres de tout autre bloqueur sortant.
- Le pare-feu Windows sur votre machine virtuelle Azure contrôle les paramètres de port.


## Logique implicite de nouvelle tentative contenue dans l’itinéraire de proxy


Dans un environnement de production, il est préférable que les clients qui se connectent à Azure SQL Database V11 ou V12 implémentent la logique de nouvelle tentative dans leur code. Cela peut être un code personnalisé ou un code qui s’appuie sur une API comme Enterprise Library.


L’itinéraire de proxy abordé précédemment dans cette rubrique s’applique à la question de la logique des nouvelles tentatives :


- Dans V11, le module d’intergiciel qui servait de proxy fournissait également une certaine logique de nouvelle tentative de façon à gérer en douceur certaines erreurs transitoires.

- Dans V12, le proxy ne fournit pas de logique de nouvelle tentative.


Dans les deux scénarios, nous conseillons que les clients implémentent la logique de nouvelle tentative dans leur propre code. La nécessité d’une logique de nouvelle tentative dans le client est sans doute accrue avec le dernier itinéraire de proxy qui ne fournit aucune logique de nouvelle tentative.


Pour obtenir des exemples de code qui illustrent une logique de nouvelle tentative, consultez [Exemples de code de démarrage rapide client pour SQL Database](sql-database-develop-quick-start-client-code-samples.md).


## Liens connexes


- [Nouveautés de la base de données SQL V12](sql-database-v12-whats-new.md)

- Considérations relatives à la logique de nouvelle tentative : [section « La passerelle ne fournit plus de logique de nouvelle tentative dans V12 », dans la rubrique « Connexion à SQL Database : liens, meilleures pratiques et règles de conception »](sql-database-connect-central-recommendations.md#gatewaynoretry)

- ADO.NET 4.6 a été publié le 20 juillet 2015. Une annonce dans un billet de blog de l’équipe .NET est disponible [ici](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).

- ADO.NET 4.5 a été publié le 15 août 2012. Une annonce dans un billet de blog de l’équipe .NET est disponible [ici](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
 - Un billet de blog sur ADO.NET 4.5.1 est disponible [ici](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).

- [Liste des versions du protocole TDS](http://www.freetds.org/userguide/tdshistory.htm)

<!---HONumber=August15_HO6-->