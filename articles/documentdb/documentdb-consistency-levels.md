<properties 
	pageTitle="Niveaux de cohérence dans DocumentDB | Microsoft Azure" 
	description="DocumentDB offre quatre niveaux de cohérence, avec les niveaux de performances associés, pour aider les développeurs d'applications à faire des compromis prévisibles entre cohérence, disponibilité et latence." 
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
	ms.date="06/25/2015" 
	ms.author="mimig"/>

# Utilisation des niveaux de cohérence pour optimiser la disponibilité et les performances dans DocumentDB

Les développeurs doivent souvent choisir entre les deux extrêmes que constituent une cohérence forte et une cohérence éventuelle. En réalité, il existe plusieurs niveaux intermédiaires entre ces deux extrêmes. Dans la plupart des scénarios réels, les applications bénéficient de compromis affinés entre cohérence, disponibilité et latence. DocumentDB offre quatre niveaux de cohérence bien définis et les niveaux de performances associés. Cela permet aux développeurs d'application de trouver des compromis prévisibles entre cohérence, disponibilité et latence.
 
Toutes les ressources système, notamment les comptes de base de données, les bases de données, les collections, les utilisateurs et les autorisations sont toujours extrêmement cohérentes en ce qui concerne les lectures et requêtes. Les niveaux de cohérence s'appliquent uniquement aux ressources définies par l'utilisateur. Pour les requêtes et les opérations de lecture sur les ressources définies par l'utilisateur, dont les documents, les pièces jointes, les procédures stockées, les déclencheurs et les fonctions définies par l'utilisateur, DocumentDB propose les quatre niveaux de cohérence suivants :

 - Strong (Fort)
 - Bounded staleness (En fonction de l'obsolescence) 
 - Session
 - Eventual (Éventuel) 

Ces niveaux de cohérence bien définis et granulaires vous permettent de trouver un bon compromis entre cohérence, disponibilité et performances. Ils sont soutenus par des niveaux de performances prévisibles garantissant des résultats cohérents pour votre application.

## Niveaux de cohérence des bases de données

Vous pouvez configurer le niveau de cohérence par défaut sur le compte de base de données qui s'applique à toutes les collections (parmi l'ensemble des bases de données) sous votre compte de base de données. Par défaut, toutes les lectures et requêtes émises vers les ressources définies par l'utilisateur utilisent le niveau de cohérence par défaut spécifié sur le compte de base de données. Vous pouvez néanmoins réduire le niveau de cohérence d’une demande de lecture/requête donnée en spécifiant l’en-tête de demande [x-ms-consistency-level]. Les quatre types de niveaux de cohérence pris en charge par le protocole de réplication de DocumentDB sont décrits brièvement ci-dessous.

>[AZURE.NOTE]Dans les prochaines versions, nous envisageons de prendre en charge le remplacement du niveau de cohérence par défaut, et ce collection par collection.

**Fort** : cette cohérence garantit qu’une écriture est visible uniquement après sa validation durable par le quorum majoritaire de réplicas. Une écriture est soit validée durablement de manière synchrone par les quorums principal et secondaire, soit abandonnée. Une lecture est toujours reconnue par le quorum de lecture majoritaire : un client ne voit jamais une écriture partielle ou non validée. Il est assuré de lire la toute dernière écriture reconnue.
 
La cohérence forte fournit des garanties absolues concernant la cohérence des données, mais offre un niveau de performances inférieur en termes de lecture et d'écriture.

**En fonction de l’obsolescence** : ce niveau de cohérence garantit l’ordre global de propagation des écritures avec la possibilité que des lectures soient retardées derrière les écritures par, au plus, des préfixes K. La lecture est toujours reconnue par un quorum majoritaire de réplicas. La réponse d'une demande de lecture spécifie son actualisation relative (en termes de K).

Le niveau En fonction de l'obsolescence fournit un comportement plus prévisible pour la cohérence des lectures, tout en offrant des écritures avec la latence la plus faible. Comme les lectures sont reconnues par un quorum majoritaire, la latence associée n'est pas la plus faible offerte par le système.

>[AZURE.NOTE]Le niveau En fonction de l’obsolescence garantit des lectures unitones uniquement sur les demandes de lecture explicites. La réponse que répercute le serveur aux demandes d’écriture n’offre aucune garantie liée à l’obsolescence.

**Par session** : contrairement aux modèles de cohérence globaux offerts par les niveaux de cohérence Fort et En fonction de l’obsolescence, le niveau Par session est adapté à une session client spécifique. Il est généralement suffisant, car il fournit des lectures unitones garanties, des écritures et la possibilité de lire vos propres écritures. Une demande de lecture pour le niveau de cohérence Session (Par session) est émise sur un réplica pouvant servir la version demandée par le client (portion du cookie de session).

Ce niveau fournit une cohérence des données de lecture prévisible pour une session, tout en offrant la latence d'écriture la plus basse. Les lectures ont également une faible latence, car sauf dans de rares cas, elles sont servies par un seul réplica.

**Éventuel** : ce niveau de cohérence est la forme de cohérence la plus faible au moyen de laquelle un client peut obtenir des valeurs plus anciennes que celles qu’il a pu voir précédemment, au fil du temps. En l'absence d'autres écritures, les réplicas du groupe finiront par converger. La demande de lecture est servie par un index secondaire.

Le niveau Éventuel fournit la cohérence la plus faible en matière de lecture, tout en offrant la latence la moins élevée pour les lectures et les écritures.

### Modification du niveau de cohérence des bases de données

1.  Sur le [portail Azure en version préliminaire](https://portal.azure.com/), cliquez sur **Parcourir tout**.

2.  Dans le panneau **Parcourir tout**, cliquez sur **Comptes DocumentDB**.

3. Dans le panneau **Comptes DocumentDB**, sélectionnez le compte de base de données à modifier.

4. Dans le panneau du compte, dans l’objectif **Configuration**, cliquez sur la vignette **Cohérence par défaut**.

5. Sélectionnez le nouveau niveau de cohérence, puis cliquez sur **Enregistrer**.

	![Capture d’écran mettant en avant la vignette Cohérence par défaut, les paramètres de la cohérence et le bouton Enregistrer](./media/documentdb-consistency-levels/database-consistency-level.png)

## Niveaux de cohérence des requêtes

Par défaut, pour les ressources définies par l'utilisateur, le niveau de cohérence des requêtes est identique à celui des lectures. Par défaut, l'index est mis à jour de manière synchrone lors de chaque insertion, remplacement ou suppression d'un document au niveau de la collection. Cela permet aux requêtes de fournir le même niveau de cohérence que celui des lectures de document. Alors que DocumentDB est optimisé pour les écritures et prend en charge des volumes soutenus d'écritures de documents, la maintenance d'index synchrone et les requêtes cohérentes, vous pouvez configurer certaines collections de manière à ce que la mise à jour de l'index soit effectuée en différé. Ce processus permet d'optimiser encore plus les performances des écritures. Il est idéal pour les scénarios d'ingestion en bloc lorsqu'une charge de travail implique principalement des lectures.

Mode d'indexation|	Lectures|	Requêtes  
-------------|-------|---------
Cohérence (par défaut)|	Choisir parmi Strong (Fort), Bounded staleness (En fonction de l'obsolescence), Session (Par session) et Eventual (Éventuel)|	Choisir parmi Strong (Fort), Bounded staleness (En fonction de l'obsolescence), Session (Par session) et Eventual (Éventuel)|
Différé|	Choisir parmi Strong (Fort), Bounded staleness (En fonction de l'obsolescence), Session (Par session) et Eventual (Éventuel)|	Eventual (Éventuel)  

À l’instar des demandes de lecture, vous pouvez réduire le niveau de cohérence d’une demande de requête donnée en spécifiant l’en-tête de demande [x-ms-consistency-level].

## Étapes suivantes

Si vous souhaitez en lire plus sur les niveaux de cohérence et les différents compromis, nous vous recommandons les ressources suivantes :

-	Doug Terry. La cohérence des données répliquées basée sur l’exemple du baseball. [http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
-	Doug Terry. Le niveau Par session garantit des données répliquées peu cohérentes. [http://dl.acm.org/citation.cfm?id=383631](http://dl.acm.org/citation.cfm?id=383631)
-	Daniel Abadi. Cohérence des compromis en termes de conception de systèmes de base de données distribuée moderne : CAP n’est qu’une partie de l’histoire. [http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html) 
-	Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph M. Hellerstein, Ion Stoica. Probabilités en fonction de l’obsolescence (PBS) pour les quorums partiels pratiques. [http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
-	Werner Vogels. Niveau de cohérence Éventuel repensé. [http://allthingsdistributed.com/2008/12/eventually_consistent.html](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
 

<!---HONumber=July15_HO4-->