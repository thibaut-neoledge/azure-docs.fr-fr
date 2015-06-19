<properties 
	pageTitle="Limites dans la version préliminaire de DocumentDB | Azure" 
	description="Découvrez les limites et l'application des quotas de DocumentDB pour la version préliminaire." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2015" 
	ms.author="mimig"/>


#Limites dans la version préliminaire de DocumentDB
Le tableau suivant décrit les limites et applications de quota dans la version préliminaire de DocumentDB. Dans la plupart des cas, les limites sont appliquées de manière à obtenir un retour ou sont basées sur les contraintes du moment en termes de capacité. Si vous souhaitez assouplir les limites pour répondre aux besoins de votre activité, appelez-nous. Nous ferons de notre mieux pour vous proposer une solution respectant les contraintes de l'offre publique.    

|Entité |Quota (offre standard pour la version préliminaire)|
|-------|--------|
|Comptes de base de données     |5
|Nombre de bases de données par compte de base de données     |100
|Nombre d'utilisateurs par compte de base de données (pour l'ensemble des bases de données) |500 000
|Nombre d'autorisations par compte de base de données (pour l'ensemble des bases de données)   |2 000 000
|Stockage de pièces jointes par compte de base de données      |2 To
|Nombre maximal d'unités de capacité par compte de base de données       |50
|Nombre de collections par unité de capacité      |3
|Stockage minimal alloué par collection avec au moins 1 document    |3,3 Go
|Débit minimal alloué par collection avec au moins 1 document |667 unités de demande
|Élasticité d'une collection    |De 0 à 10 Go
|Nombre maximal d'unités de demande par seconde par collection   |2000
|Nombre de procédures stockées, de déclencheurs et de fonctions définies par l'utilisateur par collection       |25 de chaque
|Délai maximal d'exécution pour les procédures stockées et déclencheurs     |5 seconds
|Stockage de documents configuré par unité de capacité |10 Go
|Unités de demande configurées par seconde par unité de capacité     |2000
|Stockage de documents maximal par base de données (5 unités de capacité)    |500 To
|Longueur maximale de la propriété d'ID    |255 caractères
|Nombre par défaut d'éléments par page     |100
|Nombre maximal d'éléments par page        |1000
|Taille de demande maximale des documents et pièces jointes       |512 Go
|Taille de demande maximale des procédures stockées, déclencheurs et fonctions définies par l'utilisateur        |256 Ko
|Taille de réponse maximale |1 Go
|Nombre maximal de chemins d'accès uniques par collection       |100
|String |Toutes les chaînes doivent être au format UTF-8. Étant donné qu'UTF-8 est un codage de largeur variable, les tailles de chaîne sont déterminées à l'aide d'octets UTF-8.
|Longueur maximale de la propriété ou valeur  |Aucune limite pratique
|Nombre maximal de fonctions définies par l'utilisateur par requête     |1
|Nombre maximal de clauses JOIN par requête    |2
|Nombre maximal de clauses AND par requête      |5
|Nombre maximal de clauses OR par requête       |5

<!--HONumber=49--> 