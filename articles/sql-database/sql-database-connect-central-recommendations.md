<properties 
	pageTitle="Connexion à la base de données SQL : liens, meilleures pratiques et règles de conception" 
	description="Une rubrique servant de point de départ qui rassemble des liens et des recommandations concernant les programmes clients qui se connectent à la base de données SQL Azure à partir de technologies telles que ADO.NET et PHP." 
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
	ms.date="05/01/2015" 
	ms.author="genemi"/>


# Connexion à la base de données SQL : liens, meilleures pratiques et règles de conception


Cette rubrique est idéale pour commencer avec une connectivité client à la base de données SQL Azure. Elle fournit des liens vers des exemples de code pour diverses technologies que vous pouvez utiliser pour vous connecter à et interagir avec la Base de données SQL. Les technologies incluent Enterprise Library, JDBC, PHP et de nombreuses autres. Les recommandations données s’appliquent de manière générale, indépendamment de la technologie de connexion ou du langage de programmation spécifique utilisés.


## Recommandations indépendantes des technologies


Les informations de cette section s’appliquent indépendamment de la technologie spécifique utilisée pour la connexion à la base de données SQL.


- [Recommandations pour la connexion à la base de données SQL Azure par programme](http://msdn.microsoft.com/library/azure/ee336282.aspx) - les considérations incluent les éléments suivants :
 - Ports
 - Pare-feu
 - Chaînes de connexion
- [Gestion des ressources de la base de données SQL Azure](https://msdn.microsoft.com/library/azure/dn338083.aspx) - les considérations incluent les éléments suivants :
 - Gouvernance des ressources
 - Application de limites
 - Limitation


Quelle que soit la technologie de connexion utilisée, certains paramètres de pare-feu pour le serveur de base de données SQL et même des bases de données individuelles sont importants :


- [Pare-feu de la base de données SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx)


## Recommandations : connexion


- Dans votre logique de connexion client, définissez le délai d’expiration sur 30 secondes.
 - La valeur par défaut de 15 secondes est trop courte pour les connexions qui reposent sur Internet.
- Si vous utilisez un [pool de connexions](http://msdn.microsoft.com/library/8xx3tyca.aspx), fermez la connexion dès que votre programme ne l’utilise plus activement et non lorsqu’il se prépare simplement à la réutiliser.
 - Sauf si votre programme réutilise immédiatement la connexion pour une autre opération, sans marquer de pause, nous vous recommandons de procéder comme suit : <br/><br/>Ouvrez une connexion. <br/>Exécutez une opération via la connexion. <br/>Fermez la connexion.<br/><br/>
- Utilisez la logique de nouvelle tentative de votre logique de connexion uniquement pour les erreurs temporaires. Lors de l’utilisation de la base de données SQL, vos tentatives d’ouverture de connexion ou d’émission de requête peuvent échouer pour diverses raisons.
 - Le format incorrect de votre chaîne de connexion est une raison permanente d’échec.
 - La nécessité pour le système de base de données SQL Azure d’équilibrer la charge globale est une raison temporaire d’échec. La raison temporaire disparaît d’elle-même, ce qui signifie que votre programme doit effectuer une nouvelle tentative.
 - Pour renouveler une requête, commencez par fermer la connexion, puis ouvrez une autre connexion.
- Vérifiez que votre [pare-feu de base de données SQL Azure](http://msdn.microsoft.com/library/ee621782.aspx) autorise les communications TCP sortantes sur le port 1433.
 - Vous pouvez configurer les paramètres du [pare-feu](http://msdn.microsoft.com/library/azure/ee621782.aspx) sur un serveur de base de données SQL ou pour une base de données individuelle.


## Recommandation : authentification


- Utilisez l’authentification de base de données SQL et non l’authentification Windows.
- Définissez une base de données spécifique au lieu de la base de données *maître* par défaut.
- Dans certains cas, le nom d’utilisateur doit être suivi du suffixe *@nomdevotreserveur*, mais dans d’autres, le suffixe doit être omis. Cela dépend du code de votre outil ou API.
 - Vérifiez les détails de chaque technologie.
- Connectez-vous en spécifiant un utilisateur dans une [base de données à relation contenant-contenu](http://msdn.microsoft.com/library/ff929071.aspx).
 - Cette approche assure des performances et une extensibilité accrues en éliminant la nécessité d’une connexion à la base de données MASTER.
 - Vous ne pouvez pas utiliser l’instruction **USE myDatabaseName;** Transact-SQL avec la base de données SQL.


## Erreurs temporaires


Les services cloud comme Azure et son service de base de données SQL doivent en permanence équilibrer les charges de travail et gérer les ressources. Si deux bases de données servies à partir du même ordinateur sont impliquées dans un traitement particulièrement lourd, sur des périodes qui se chevauchent, le système de gestion peut décider qu’il est nécessaire de déplacer la charge de travail de l’une des bases de données vers une autre ressource dont les capacités sont plus importantes.


Lors du changement, la base de données peut être temporairement indisponible. Cela peut bloquer les nouvelles connexions ou entraîner la perte de connexion de votre programme client. Mais le changement de ressources est temporaire et le problème peut se résoudre de lui-même en quelques minutes, voire quelques secondes. Une fois le changement terminé, votre programme client peut rétablir la connexion et reprendre son activité. Il est préférable de faire une pause dans le traitement plutôt que de créer une défaillance inutile de votre programme client.


Lorsqu’une erreur se produit en rapport avec la base de données SQL, une exception [SqlException](https://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx) est levée. L’exception SqlException contient un code d’erreur numérique dans sa propriété **Numéro**. Si le code d’erreur correspond à une erreur temporaire, votre programme doit renouveler l’appel.


- [Messages d’erreur (Base de données SQL Azure)](http://msdn.microsoft.com/library/azure/ff394106.aspx) : sa section **Erreurs de perte de connexion** répertorie les erreurs temporaires qui justifient une nouvelle tentative automatique.
 - Par exemple, réessayez si le numéro d’erreur 40613 se produit, ce qui indique que quelque chose de similaire à<br/>* la base de données « mabasededonnées » sur le serveur « leserveur » n’est pas disponible actuellement.*


Les *erreurs* temporaires sont parfois appelées des *défaillances* temporaires. Cette rubrique considère ces deux termes comme des synonymes.


Pour obtenir une aide supplémentaire en cas d’erreur de connexion, temporaire ou non, consultez la page :


- [Résolution des problèmes de connexion à la base de données SQL Azure](http://support.microsoft.com/kb/2980233/)


## Technologies


Les rubriques suivantes contiennent des liens vers des exemples de code pour plusieurs langages et technologies de pilote que vous pouvez utiliser pour vous connecter à la base de données SQL Azure à partir de votre programme client.


Divers exemples de codes sont fournis pour les clients exécutés sous Windows et Linux.


**Exemples généraux :** il existe des exemples de code pour un grand nombre de langages de programmation, notamment PHP, Python, Node.js et CSharp .NET. En outre, des exemples sont fournis pour les clients qui s’exécutent sous Windows ou Linux.


- [Développement de clients et exemples de code de démarrage rapide pour une base de données SQL](sql-database-develop-quick-start-client-code-samples.md)
- [Développement de base de données SQL Azure : rubriques Procédures](http://msdn.microsoft.com/library/azure/ee621787.aspx)


**Logique de nouvelle tentative :** il existe des exemples de code conçus avec le niveau de sophistication nécessaire pour gérer les erreurs temporaires avec une logique de nouvelle tentative automatique.


- [Connexion fiable à une base de données SQL Azure](http://msdn.microsoft.com/library/azure/dn864744.aspx)
- [Connexion à la base de données SQL Azure à l’aide d’ADO.NET avec Enterprise Library](http://msdn.microsoft.com/library/azure/dn961167.aspx)
- [Connexion à une base de données SQL Azure à l’aide d’ADO.NET](http://msdn.microsoft.com/library/azure/ee336243.aspx)


**Infrastructure élastique :** pour plus d’informations concernant la connectivité aux bases de données élastiques, consultez :


- [Prise en main de l’infrastructure élastique de Base de données SQL Azure (version préliminaire)](sql-database-elastic-scale-get-started.md)
- [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md)


**Bibliothèques de pilotes :** pour plus d’informations sur les bibliothèques de pilotes de connexion, ainsi que les versions recommandées, consultez :


- [Bibliothèques de connexions de la base de données SQL et de SQL Server](sql-database-libraries.md)


## Voir aussi


- [Créer votre première base de données SQL Azure](sql-database-get-started.md)

 

<!---HONumber=July15_HO2-->