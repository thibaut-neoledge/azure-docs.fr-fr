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
	ms.date="07/21/2015" 
	ms.author="genemi"/>


# Connexion à SQL Database : liens, meilleures pratiques et règles de conception


Cette rubrique est idéale pour commencer une connectivité client avec Azure SQL Database. Elle fournit des liens vers des exemples de code pour diverses technologies que vous pouvez utiliser pour vous connecter à et interagir avec SQL Database. Les technologies incluent Enterprise Library, JDBC, PHP et de nombreuses autres. Les recommandations données s’appliquent de manière générale, indépendamment de la technologie de connexion ou du langage de programmation spécifique utilisés.


## Recommandations indépendantes des technologies


Les informations de cette section s’appliquent indépendamment de la technologie spécifique utilisée pour la connexion à SQL Database.


- [Instructions pour la connexion à Azure SQL Database par programmation](http://msdn.microsoft.com/library/azure/ee336282.aspx) - les considérations incluent les éléments suivants :
 - Ports
 - Pare-feu
 - Chaînes de connexion
- [Gestion des ressources d’Azure SQL Database](https://msdn.microsoft.com/library/azure/dn338083.aspx) - les considérations incluent les éléments suivants :
 - Gouvernance des ressources
 - Application de limites
 - Limitation


Quelle que soit la technologie de connexion utilisée, certains paramètres de pare-feu pour le serveur de base de données SQL et même des bases de données individuelles sont importants :


- [Pare-feu Azure SQL Database](https://msdn.microsoft.com/library/azure/ee621782.aspx)


## Recommandation : authentification


- Utilisez l’authentification SQL Database et non l’authentification Windows.
- Définissez une base de données spécifique, au lieu de la base de données *master* par défaut.
- Dans certains cas, le nom d’utilisateur doit être suivi du suffixe *@nom_votre_serveur*, mais dans d’autres, le suffixe doit être omis. Cela dépend du code de votre outil ou API.
 - Vérifiez les détails de chaque technologie.
- Connectez-vous en spécifiant un utilisateur d’une [base de données à relation contenant-contenu](http://msdn.microsoft.com/library/ff929071.aspx).
 - Cette approche assure des performances et une extensibilité accrues en éliminant la nécessité d’une connexion à la base de données MASTER.
 - Vous ne pouvez pas utiliser l’instruction **USE myDatabaseName;** de Transact-SQL sur une base de données SQL.


## Recommandations : connexion


- Dans votre logique de connexion client, définissez le délai d’expiration sur 30 secondes.
 - La valeur par défaut de 15 secondes est trop courte pour les connexions qui reposent sur Internet.
- Vérifiez que votre [pare-feu Azure SQL Database](http://msdn.microsoft.com/library/ee621782.aspx) autorise les communications TCP sortantes sur le port 1433.
 - Vous pouvez configurer les paramètres du [pare-feu](http://msdn.microsoft.com/library/azure/ee621782.aspx) sur un serveur de base de données SQL ou pour une base de données individuelle.
- Si vous utilisez un [pool de connexions](http://msdn.microsoft.com/library/8xx3tyca.aspx), fermez la connexion dès que votre programme ne l’utilise pas activement et qu’il ne se prépare pas simplement à la réutiliser.
 - Sauf si votre programme réutilise immédiatement la connexion pour une autre opération, sans marquer de pause, nous vous recommandons de procéder comme suit : <br/><br/>Ouvrez une connexion. <br/>Exécutez une opération via la connexion. <br/>Fermez la connexion.<br/><br/>
- Utilisez la logique de nouvelle tentative de votre logique de connexion uniquement pour les erreurs temporaires. Lors de l’utilisation de SQL Database, vos tentatives d’ouverture de connexion ou d’émission de requête peuvent échouer pour diverses raisons.
 - Le format incorrect de votre chaîne de connexion est une raison permanente d’échec.
 - La nécessité pour le système Azure SQL Database d’équilibrer la charge globale est une raison temporaire d’échec. La raison temporaire disparaît d’elle-même, ce qui signifie que votre programme doit effectuer une nouvelle tentative.
 - Pour renouveler une requête, commencez par fermer la connexion, puis ouvrez une autre connexion.


La section suivante contient plus d’informations sur la logique de nouvelle tentative et la gestion des erreurs temporaires.


## Gestion des erreurs temporaires et logique de nouvelle tentative


Les services cloud comme Azure et son service SQL Database doivent en permanence équilibrer les charges de travail et gérer les ressources. Si deux bases de données servies à partir du même ordinateur sont impliquées dans un traitement particulièrement lourd, sur des périodes qui se chevauchent, le système de gestion peut décider qu’il est nécessaire de déplacer la charge de travail de l’une des bases de données vers une autre ressource dont les capacités sont plus importantes.


Lors du changement, la base de données peut être temporairement indisponible. Cela peut bloquer les nouvelles connexions ou entraîner la perte de connexion de votre programme client. Mais le changement de ressources est temporaire et le problème peut se résoudre de lui-même en quelques minutes, voire quelques secondes. Une fois le changement terminé, votre programme client peut rétablir la connexion et reprendre son activité. Il est préférable de faire une pause dans le traitement plutôt que de créer une défaillance inutile de votre programme client.


Lorsqu’une erreur se produit en rapport avec SQL Database, une exception [SqlException](https://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx) est levée. La `SqlException` contient un code d’erreur numérique dans sa propriété **Numéro**. Si le code d’erreur correspond à une erreur temporaire, votre programme doit renouveler l’appel.


- [Messages d’erreur (Azure SQL Database)](http://msdn.microsoft.com/library/azure/ff394106.aspx)
 - Sa section **Erreurs temporaires, erreurs de perte de connexion** répertorie les erreurs temporaires qui justifient une nouvelle tentative automatique.
 - Par exemple, réessayez si vous obtenez le numéro d’erreur 40613, qui correspond à peu près à <br/>* La base de données « ma_base_de_données » sur le serveur « le_serveur » n’est pas disponible actuellement.*


Les *erreurs* temporaires sont parfois appelées des *défaillances* temporaires. Cette rubrique considère ces deux termes comme des synonymes.


Pour obtenir une aide supplémentaire en cas d’erreur de connexion, temporaire ou non, consultez la page :


- [Résolution des problèmes de connexion à Azure SQL Database](http://support.microsoft.com/kb/2980233/)


Pour obtenir les liens vers les rubriques d’exemples de code qui illustrent la logique de nouvelle tentative, consultez :


- [Exemples de code de démarrage rapide client pour SQL Database](sql-database-develop-quick-start-client-code-samples.md)


<a id="gatewaynoretry" name="gatewaynoretry">&nbsp;</a>


## La passerelle ne fournit plus de logique de nouvelle tentative dans V12


Avant la version V12, Azure SQL Database avait une passerelle qui agissait en tant que proxy pour mettre en mémoire tampon toutes les interactions entre la base de données et votre programme client. La passerelle était un tronçon réseau supplémentaires qui augmentait parfois la latence des accès à la base de données.


V12 a supprimé la passerelle. À présent :


- Votre programme client interagit *directement* avec la base de données, ce qui est plus efficace.
- Les distorsions mineures de la passerelle dans les messages d’erreur et autres communications avec votre programme ont été supprimées.
 - SQL Database et SQL Server semblent plus identiques à votre programme.


#### Disparition de la logique de nouvelle tentative


La passerelle gérait automatiquement la nouvelle tentative lors de certaines erreurs transitoires. Désormais, votre programme doit gérer plus en détail les erreurs temporaires. Pour obtenir des exemples de code sur la logique de nouvelle tentative, consultez :


- [Exemples de code de développement de client et de démarrage rapide pour SQL Database](sql-database-develop-quick-start-client-code-samples.md)
 - Propose des liens vers des exemples de code qui contiennent la logique de nouvelle tentative et vers des exemples plus simples de connexion et de requête.
- [Procédure : connexion fiable à Azure SQL Database](http://msdn.microsoft.com/library/azure/dn864744.aspx)
- [Procédure : connexion à Azure SQL Database en utilisant ADO.NET avec Enterprise Library](http://msdn.microsoft.com/library/azure/dn961167.aspx)
- [Procédure : connexion à Azure SQL Database à l’aide d’ADO.NET](http://msdn.microsoft.com/library/azure/ee336243.aspx)


## Technologies


Les rubriques suivantes contiennent des liens vers des exemples de code pour plusieurs langages et technologies de pilote que vous pouvez utiliser pour vous connecter à Azure SQL Database à partir de votre programme client.


Divers exemples de codes sont fournis pour les clients qui s’exécutent sur Windows, Linux et Mac OS X.


**Exemples généraux :** il existe des exemples de code pour un grand nombre de langages de programmation, tels que PHP, Python, Node.js et CSharp .NET. De même, des exemples sont fournis pour les clients qui s’exécutent sur Windows, Linux et Mac OS X.


- [Exemples de code de développement de client et de démarrage rapide pour SQL Database](sql-database-develop-quick-start-client-code-samples.md)
- [Développement Azure SQL Database : rubriques Procédures](http://msdn.microsoft.com/library/azure/ee621787.aspx)


**Mise à l’échelle élastique :** pour plus d’informations sur la connectivité aux bases de données de mise à l’échelle élastique, consultez :


- [Prise en main de l'infrastructure élastique d’Azure SQL Database (version préliminaire)](sql-database-elastic-scale-get-started.md)
- [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md)


**Bibliothèques de pilotes :** pour plus d’informations sur les bibliothèques de pilotes de connexion, ainsi que sur les versions recommandées, consultez :


- [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md)


## Voir aussi


- [Créer votre première base de données SQL Azure](sql-database-get-started.md)

 

<!---HONumber=July15_HO4-->