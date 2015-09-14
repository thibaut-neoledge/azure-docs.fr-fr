<properties 
	pageTitle="Connexion à SQL Database : liens, meilleures pratiques et règles de conception"
	description="Rubrique servant de point de départ qui rassemble des liens et des recommandations concernant les programmes clients qui se connectent à Azure SQL Database à partir de technologies telles que ADO.NET et PHP."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags 
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/02/2015"
	ms.author="genemi"/>


# Connexion à SQL Database : liens, meilleures pratiques et règles de conception


Cette rubrique est idéale pour commencer une connectivité client avec Azure SQL Database. Elle fournit des liens vers des exemples de code pour diverses technologies que vous pouvez utiliser pour vous connecter à et interagir avec SQL Database. Les technologies incluent Enterprise Library, JDBC, PHP et de nombreuses autres. Les informations fournies s’appliquent indépendamment de la technologie spécifique utilisée pour la connexion à la base de données SQL.


## Recommandations indépendantes des technologies


- [Instructions pour se connecter à Azure SQL Database par programmation](http://msdn.microsoft.com/library/azure/ee336282.aspx) - Les éléments abordés sont les suivants :
 - [Ports et pare-feu](sql-database-configure-firewall-settings.md/)
 - Chaînes de connexion
- [Gestion des ressources d’Azure SQL Database](https://msdn.microsoft.com/library/azure/dn338083.aspx) - Les éléments abordés sont les suivants :
 - Gouvernance des ressources
 - Application de limites
 - Limitation


## Recommandations relatives à l’authentification


- Utilisez l’authentification d’Azure SQL Database, et pas l’authentification Windows qui n’est pas disponible dans Azure SQL Database.
- Définissez une base de données spécifique, au lieu de la base de données *master* par défaut.
 - Vous ne pouvez pas utiliser l’instruction **USE myDatabaseName;** Transact-SQL sur une base de données SQL pour basculer vers une autre base de données.


### Utilisateurs contenus


Vous avez plusieurs choix lors de l’ajout d’une personne en tant qu’utilisateur à votre base de données SQL :

- Ajouter une *connexion* avec un mot de passe à la base de données **master**, puis ajouter un *utilisateur* correspondant à une ou plusieurs autres bases de données sur le même serveur.

- Ajouter un *utilisateur contenu* avec un mot de passe à une ou plusieurs bases de données, et aucun lien vers une *connexion* dans la base de données **master**.


L’approche de l’utilisateur contenu présente des avantages et des inconvénients :

- L’avantage est qu’une base de données peut facilement être déplacée d’un serveur Azure SQL Database vers un autre quand tous les utilisateurs de la base de données sont des utilisateurs contenus.

- L’inconvénient est la difficulté plus grande de l’administration de routine. Par exemple :
 - Il est plus difficile de supprimer plusieurs utilisateurs contenus que de supprimer une connexion.
 - Une personne qui est un utilisateur contenu dans plusieurs bases de données peut avoir davantage de mots de passe à mémoriser ou à mettre à jour.


Pour plus d’informations, consultez [Bases de données contenues](http://msdn.microsoft.com/library/ff929071.aspx).


## Recommandations relatives à la connexion


- Dans votre logique de connexion client, définissez le délai d’expiration sur 30 secondes.
 - La valeur par défaut de 15 secondes est trop courte pour les connexions qui reposent sur Internet.


- Vérifiez que votre [pare-feu Azure SQL Database](sql-database-firewall-configure.md) autorise les communications TCP sortantes sur le port 1433.
 - Vous pouvez configurer les paramètres du pare-feu sur un serveur de base de données SQL ou pour une base de données individuelle.


- Pour gérer les *erreurs temporaires*, ajoutez une logique de [*nouvelle tentative*](#TransientFaultsAndRetryLogicGm) à vos programmes clients qui interagissent avec Azure SQL Database.


### Pool de connexions


Si vous utilisez un [pool de connexions](http://msdn.microsoft.com/library/8xx3tyca.aspx), fermez la connexion dès que votre programme ne l’utilise plus activement et s’il ne se prépare pas à la réutiliser.

Sauf si votre programme doit réutiliser immédiatement la connexion pour une autre opération et sans marquer de pause, nous vous recommandons de procéder comme suit :

- Ouvrez une connexion.
- Exécutez une opération via la connexion.
- Fermez la connexion.


#### Exception levée lors de l’utilisation d’un pool


Quand la mise en pool de connexions est activé et qu’une erreur de délai d’attente ou une autre erreur de connexion se produit, une exception est levée. Les tentatives de connexion suivantes échoueront au cours des 5 secondes suivantes, qui est appelée une *période de blocage*.

Si l’application tente de se connecter au cours de la période de blocage, la première exception est levée à nouveau. Après la fin de la période blocage, les échecs suivants entraînent une nouvelle période de blocage qui dure deux fois plus longtemps que la période de blocage précédente.

La durée maximale d’une période de blocage est de 60 secondes.


### Ports autres que simplement 1433 dans V12


Parfois, les connexions clientes à Azure SQL Database V12 ignorent le proxy et interagissent directement avec la base de données. Les ports autres que le port 1433 deviennent importants. Pour plus d’informations, consultez <br/> [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md)


La section suivante contient plus d’informations sur la logique de nouvelle tentative et la gestion des erreurs temporaires.



<a name="TransientFaultsAndRetryLogicGm" id="TransientFaultsAndRetryLogicGm"></a>

&nbsp;

## Erreurs temporaires et logique de nouvelle tentative


Le système Azure a la capacité de reconfigurer dynamiquement les serveurs quand des charges de travail importantes sont à traiter dans le service SQL Database.

Toutefois, une reconfiguration peut entraîner la perte par votre programme client de sa connexion à SQL Database. Cette erreur est appelée une *erreur temporaire*.

Votre programme client peut tenter de rétablir une connexion après avoir attendu entre par exemple 6 à 60 secondes entre deux tentatives. Vous devez fournir la logique de nouvelle tentative dans votre client.

Pour obtenir des exemples de code qui illustrent une logique de nouvelle tentative, consultez [Exemples de code de démarrage rapide du client pour SQL Database](sql-database-develop-quick-start-client-code-samples.md).


### Numéros d’erreur pour les erreurs temporaires


Quand une erreur se produit avec SQL Database, une exception [SqlException](http://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx) est levée. L’exception **SqlException** contient un code d’erreur numérique dans sa propriété **Number**. Si le code d’erreur correspond à une erreur temporaire, votre programme doit renouveler l’appel.


- [Messages d'erreur pour les programmes clients SQL Database](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - Sa section **Erreurs temporaires, erreurs de perte de connexion** est une liste des erreurs temporaires qui justifient une nouvelle tentative automatique.
 - Par exemple, faites une nouvelle tentative si vous obtenez le numéro d’erreur 40613, qui correspond à peu près à <br/>*La base de données « ma\_base\_de\_données » sur le serveur « serveur » n’est pas disponible actuellement.*


Pour plus d’informations, consultez [Développement Azure SQL Database : rubriques de procédures](http://msdn.microsoft.com/library/azure/ee621787.aspx) – [Résoudre les problèmes de connexion à Azure SQL Database](http://support.microsoft.com/kb/2980233/)


## Technologies


Les rubriques suivantes contiennent des liens vers des exemples de code pour plusieurs langages et technologies de pilote que vous pouvez utiliser pour vous connecter à Azure SQL Database à partir de votre programme client.


Divers exemples de codes sont fournis pour les clients qui s’exécutent sur Windows, Linux et Mac OS X.


**Exemples généraux :** il existe des [exemples de code](sql-database-develop-quick-start-client-code-samples.md) pour un grand nombre de langages de programmation, comme PHP, Python, Node.js et .NET CSharp. De même, des exemples sont fournis pour les clients qui s’exécutent sur Windows, Linux et Mac OS X.


**Mise à l’échelle élastique :** pour plus d’informations sur la connectivité aux bases de données de mise à l’échelle élastique, consultez :

- [Prise en main de l'infrastructure élastique d’Azure SQL Database (version préliminaire)](sql-database-elastic-scale-get-started.md)
- [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md)


**Bibliothèques de pilotes :** pour plus d’informations sur les bibliothèques de pilotes de connexion, y compris les versions recommandées, consultez :

- [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md)

<!---HONumber=September15_HO1-->