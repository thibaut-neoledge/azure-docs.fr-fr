<properties 
	pageTitle="Connexion à la base de données SQL Azure : recommandations principales" 
	description="Une rubrique centrale qui fournit des liens vers des rubriques plus spécifiques sur différents pilotes, tels qu'ADO.NET et PHP, pour la connexion à la base de données SQL Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/19/2015" 
	ms.author="genemi"/>


#Connexion à la base de données SQL : recommandations principales


<!--
GeneMi , 2015-March-19 Thursday 15:41pm
sql-database-connect-central-recommendations.md
sql-database-connect-*.md

Re SqlException, not .HResult, rather .Number.
-->


Cette rubrique fournit des liens vers des exemples de code pour diverses technologies que vous pouvez utiliser pour la connexion à la base de données SQL Azure et les interactions avec celle-ci. Les technologies incluent Enterprise Library, JDBC et PHP. Les recommandations données s'appliquent de manière générale, indépendamment de la technologie de connexion spécifique utilisée.


##Recommandations indépendantes des technologies


Les informations de cette section s'appliquent indépendamment de la technologie spécifique utilisée pour la connexion à la base de données SQL.


- [Recommandations pour la connexion à la base de données SQL Azure par programme](http://msdn.microsoft.com/library/azure/ee336282.aspx) - les considérations incluent les éléments suivants :
 - Ports
 - Pare-feu
 - Chaînes de connexion
- [Gestion des ressources de la base de données SQL Azure](https://msdn.microsoft.com/library/azure/dn338083.aspx) - les considérations incluent les éléments suivants :
 - Gouvernance des ressources
 - Application de limites
 - Limitation


Quelle que soit la technologie de connexion utilisée, certains paramètres de pare-feu pour le serveur de base de données SQL et même des bases de données individuelles sont importants :


- [Pare-feu de la base de données SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx)


###Recommandations rapides


####Connexion


- Dans votre logique de connexion client, définissez le délai d'expiration sur 30 secondes.
 - La valeur par défaut de 15 secondes est trop courte pour les connexions qui reposent sur Internet.
- Si vous utilisez un [pool de connexions](http://msdn.microsoft.com/library/8xx3tyca.aspx), fermez la connexion lorsque votre programme ne l'utilise plus activement et non lorsqu'il se prépare simplement à la réutiliser.
 - À moins que votre programme réutilise immédiatement la connexion pour une autre opération, sans marquer de pause, nous vous recommandons de procéder comme suit :
<br/><br/>Ouvrez une connexion.
<br/>Exécutez une opération via la connexion.
<br/>Fermez la connexion.<br/><br/>
- Utilisez la logique de nouvelle tentative de votre logique de connexion uniquement pour les erreurs temporaires. Lors de l'utilisation de la base de données SQL, vos tentatives d'ouverture de connexion ou d'émission de requête peuvent échouer pour diverses raisons.
 - Le format incorrect de votre chaîne de connexion est une raison permanente d'échec.
 - La nécessité pour le système de base de données SQL Azure d'équilibrer la charge globale est une raison temporaire d'échec. La raison temporaire disparaît d'elle-même, ce qui signifie que votre programme doit effectuer une nouvelle tentative.
 - Pour renouveler une requête, commencez par fermer la connexion, puis ouvrez une autre connexion.
- Vérifiez que votre [pare-feu de base de données SQL Azure](http://msdn.microsoft.com/library/ee621782.aspx) autorise les communications TCP sortantes sur le port 1433.
 - Vous pouvez configurer les paramètres du [pare-feu](http://msdn.microsoft.com/library/azure/ee621782.aspx) sur un serveur de base de données SQL ou pour une base de données individuelle.


####Authentification


- Utilisez l'authentification de base de données SQL et non l'authentification Windows.
- Définissez une base de données spécifique au lieu de la base de données *master* par défaut.
- Dans certains cas, le nom d'utilisateur doit être suivi du suffixe *@nomdevotreserveur*, mais dans d'autres, le suffixe doit être omis. Cela dépend du code de votre outil ou API.
 - Vérifiez les détails de chaque technologie.
- Connectez-vous en spécifiant un utilisateur dans une [base de données à relation contenant-contenu](http://msdn.microsoft.com/library/ff929071.aspx).
 - Cette approche assure des performances et une extensibilité accrues en éliminant la nécessité d'une connexion à la base de données MASTER.
 - Vous ne pouvez pas utiliser l'instruction **USE myDatabaseName;** Transact-SQL avec la base de données SQL.


###Erreurs temporaires


Certaines erreurs de connexion à la base de données SQL sont permanentes ; il est donc inutile de réessayer immédiatement de se connecter. D'autres erreurs sont temporaires. Avec ce type d'erreurs, il est recommandé d'effectuer quelques nouvelles tentatives de connexion à l'aide de votre programme. Exemples :


- *Erreur permanente :* Si vous orthographiez mal le nom de votre serveur de base de données SQL lorsque vous essayez de vous connecter, cela ne sert à rien de réessayer.
- *Erreur temporaire :* Si vous êtes déconnecté ultérieurement de la base de données SQL par les systèmes de limitation ou d'équilibrage de charge d'Azure, il est recommandé d'essayer de se reconnecter.


L'exception [SqlException](https://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx) levée par l'appel à la base de données SQL contient un code d'erreur numérique dans sa propriété **Numéro**. Si le code d'erreur correspond à une erreur répertoriée comme temporaire, votre programme doit renouveler l'appel.


- [Messages d'erreur (Base de données SQL Azure)](http://msdn.microsoft.com/library/azure/ff394106.aspx) : sa section **Erreurs de perte de connexion** répertorie les erreurs temporaires qui justifient une nouvelle tentative.
 - Par exemple, une nouvelle tentative doit être effectuée si l'erreur numéro 40613 se produit. Celle-ci indique un message du type :<br/>*La base de données 'mydatabase' sur le serveur 'theserver' n'est pas disponible actuellement.*


Pour obtenir une aide supplémentaire lorsque vous rencontrez une erreur de connexion permanente ou temporaire, consultez la page :


- [Résolution des problèmes de connexion à la base de données SQL Azure](http://support.microsoft.com/fr-fr/kb/2980233/fr-fr)


##Technologies


La rubrique suivante contient des liens vers des exemples de code pour plusieurs technologies de connexion :


- [Développement de base de données SQL Azure : procédures](http://msdn.microsoft.com/library/azure/ee621787.aspx)


Pour ADO.NET avec Enterprise Library et les classes de gestion des erreurs temporaires, consultez la page :


- [Établissement d'une connexion fiable à la base de données SQL Azure](http://msdn.microsoft.com/library/azure/dn864744.aspx)


Pour l'infrastructure élastique, consultez la page :


- [Prise en main de l'infrastructure élastique de Base de données SQL Azure (version préliminaire)](sql-database-elastic-scale-get-started.md)
- [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md)


##Publications incomplètes ou obsolètes


Cette section contient des liens vers des billets de blog ou des sources similaires susceptibles par nature d'être incomplets ou obsolètes. Ils peuvent toutefois présenter des informations générales utiles.


- [Logique des nouvelles tentatives pour les erreurs temporaires dans la base de données SQL Microsoft Azure](http://social.technet.microsoft.com/wiki/contents/articles/4235.retry-logic-for-transient-failures-in-windows-azure-sql-database.aspx)

<!-- -->


<!--HONumber=49-->