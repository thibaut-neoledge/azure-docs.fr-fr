<properties 
	pageTitle="Connexion à des entrées et sorties | Azure" 
	description="Apprenez à vous connecter aux sources d’entrée et cibles de sortie pour les solutions Stream Analytics et à les configurer." 
	documentationCenter="" 
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="04/24/2015" 
	ms.author="jeffstok"/>

#Connexion à des entrées et sorties
Ce document présente les différentes méthodes de configuration des sources d’entrée et des cibles de sortie pour les solutions Stream Analytics.

##Utilisation de SQL en tant que sortie

Vous pouvez utiliser les bases de données SQL Azure comme sorties de données relationnelles ou pour les applications qui dépendent de contenus hébergés dans une base de données.

[http://azure.microsoft.com/services/sql-database/](http://azure.microsoft.com/services/sql-database/)

Pour utiliser une base de données SQL Azure, vous devez avoir les informations suivantes sur votre base de données :

1. Nom du serveur.
2. Nom de la base de données.
3. Une combinaison nom d’utilisateur/mot de passe valide.
4. Nom de la table de sortie.

![graphic1][graphic1]

Accédez à l’onglet Sorties de votre travail, cliquez sur la commande « AJOUTER UNE SORTIE » et cliquez sur Suivant.

![graphic2][graphic2]


Choisissez « Base de données SQL » comme sortie.

![graphic3][graphic3]

Sur la page suivante, entrez les informations de la base de données. L’alias de sortie est le nom que vous pouvez utiliser dans votre requête pour diriger la sortie de la requête vers cette base de données. L’alias par défaut si vous disposez d’une sortie est « sortie ».

![graphic4][graphic4]

Si vous utilisez une base de données qui existe dans l’abonnement que vous utilisez, sélectionnez « Utiliser la base de données SQL de l’abonnement actuel » et sélectionnez la base de données dans la liste déroulante.

![graphic5][graphic5]

Cliquez sur Suivant pour ajouter cette sortie. Deux opérations doivent démarrer, la première étant l’ajout de la sortie.

![graphic6][graphic6]

La seconde est un test de la connexion. Azure Stream Analytics tente de se connecter à cette base de données SQL et de vérifier que les informations d’identification saisies sont correctes et que la table est accessible. Quand cette opération est terminée, l’un des messages suivants doit s’afficher.

![graphic7][graphic7]

![graphic8][graphic8]



Si le second message s’affiche, cliquez sur « DÉTAILS » pour afficher les détails exacts de l’erreur.

![graphic9][graphic9]

Dans cet exemple, les informations d’identification fournies étaient incorrectes. Vous pouvez corriger les informations d’identification et réessayer en cliquant sur le bouton « TESTER LA CONNEXION ».

![graphic10][graphic10]

##Utilisation d’Event Hubs

###Vue d'ensemble
Event Hubs est un service de réception d’événements hautement évolutif qui constitue le chemin d’entrée le plus répandu pour l’absorption des données Stream Analytics. Il est conçu pour recevoir des flux d’événements à partir d’appareils et de services divers. Event Hubs et Stream Analytics fournissent au client une solution complète pour des analyses en direct : Event Hubs permet aux clients d’alimenter Azure en événements en temps réel et les travaux de Stream Analytics peuvent les traiter en temps réel. Par exemple, les clients peuvent publier des clics web, des lectures de capteurs ou des journaux d’événements en ligne sur Event Hubs et créer des travaux Stream Analytics pour utiliser Event Hubs comme flux de données d’entrée pour les opérations de filtrage, d’agrégation et de jointure en temps réel. Event Hubs peut également être utilisé pour la sortie des données. L’utilisation la plus courante d’Event Hubs en tant que sortie est le cas où la sortie d’un travail Stream Analytics est l’entrée d’un autre travail de diffusion.

###Groupes de consommateurs
Chaque entrée d’un travail Stream Analytics doit être configurée pour disposer de son propre groupe de consommateurs d’un concentrateur d’événements. Lorsqu’un travail contient une jointure réflexive ou plusieurs sorties, des entrées peuvent être lues par plus d’un lecteur et le nombre de lecteurs d’un même groupe de consommateurs peut excéder la limite du concentrateur d’événements de 5 lecteurs par groupe de consommateurs. Dans ce cas, la requête doit être divisée en plusieurs requêtes différentes et les résultats intermédiaires routés via des concentrateurs d’événements supplémentaires. Notez qu’il existe également une limite de 20 groupes de consommateurs par concentrateur d’événements. Pour en savoir plus, consultez le guide de développement de Concentrateurs d'événements.

 
###Paramètres
Quelques paramètres doivent être configurés pour les flux de données Event Hubs. Ces paramètres s’appliquent à la fois aux flux de données d’entrée et de sortie du concentrateur d’événements, sauf indication contraire.

1. Espace de noms Service Bus : espace de noms Service Bus du concentrateur d’événements. Un espace de noms Service Bus est un conteneur pour un jeu d’entités de messagerie. En créant un concentrateur d’événements, vous avez également créé un espace de noms Service Bus. 
2. Nom du concentrateur d’événements : nom donné au concentrateur d’événements. C’est le nom que vous avez spécifié lors de la création d’un concentrateur d’événements. 
3. Nom de la stratégie du concentrateur d’événements : nom de la stratégie d’accès partagé au concentrateur d’événements. La stratégie d’accès partagé peut être configurée pour un concentrateur d’événements sous l’onglet Configurer. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès.
4. Nom de la clé de la stratégie du concentrateur d’événements : clé principale ou secondaire de la stratégie d’accès partagé au concentrateur d’événements. 
5. Groupe de consommateurs du concentrateur d’événements : paramètres facultatifs pour les entrées du concentrateur d’événements. Groupe de consommateurs qui absorbe les données du concentrateur d’événements. En l’absence de spécification, les travaux Stream Analytics utilisent le groupe de consommateurs par défaut pour recevoir les données à partir du concentrateur d’événements. Nous recommandons d’utiliser un groupe de consommateurs distinct pour chaque travail Stream Analytics.

Colonne de clé de partition : paramètre facultatif pour les sorties du concentrateur d’événements. Colonne d’attributs des données utilisée comme clé de partition pour la sortie du concentrateur d’événements.

##Utilisation de la sortie de tables Azure

Les tables Azure peuvent être utilisées pour stocker des données structurées avec moins de contraintes sur le schéma. Le stockage des données sur les tables Azure permet d’assurer leur persistance et une récupération efficace. Pour plus d’informations, consultez la page [Présentation d’Azure Storage](http://azure.microsoft.com/storage-introduction/)
 
Pour utiliser un stockage de tables Azure, vous devez avoir les informations suivantes sur votre table :

1. Nom de compte de stockage \(si ce système de stockage est dans un autre abonnement que votre travail de diffusion\).
2. Clé de compte de stockage \(si ce système de stockage est dans un autre abonnement que votre travail de diffusion\).
3. Nom de table de sortie \(sera créé s’il n’existe pas\).
4. Clé de partition \(obligatoire\).
5.   Clé de ligne \(actuellement ce paramètre est obligatoire, mais nous projetons de le rendre facultatif suite aux commentaires des clients\)

Pour une meilleure conception de la clé de partition et de la clé de ligne, veuillez vous référer à l’article ci-dessous [Conception d’une stratégie de partitionnement évolutive pour Azure Table Storage](https://msdn.microsoft.com/library/azure/hh508997.aspx).


![graphic11][graphic11]


Accédez à l’onglet Sorties de votre travail, cliquez sur la commande « AJOUTER UNE SORTIE » et cliquez sur Suivant.


![graphic12][graphic12]


Choisissez « Stockage de tables » comme sortie.


![graphic13][graphic13]


Dans la page suivante, entrez les informations de la table Azure. L’alias de sortie est le nom que vous pouvez utiliser dans votre requête pour diriger la sortie de cette requête vers cette base de données.


![graphic14][graphic14] ![graphic15][graphic15]

La taille du lot est le nombre d’enregistrements pour une opération par lot, laissez la valeur par défaut si vous n’êtes pas familier avec cela, ou consultez la page [https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) pour plus de détails


Si vous utilisez un stockage Azure qui existe dans votre abonnement, sélectionnez « Utiliser le compte de stockage de l’abonnement actuel » et choisissez le compte de stockage dans la liste déroulante.

Cliquez sur Suivant pour ajouter cette sortie. Deux opérations doivent démarrer, la première étant l’ajout de la sortie.

![graphic16][graphic16]

La seconde est un test de la connexion. Azure Stream Analytics tente de se connecter à ce compte de stockage et de vérifier que les informations d’identification saisies sont correctes. Quand cette opération est terminée, l’un des messages suivants doit s’afficher.

![graphic17][graphic17]

Si le second message s’affiche, cliquez sur « DÉTAILS » pour afficher les détails exacts de l’erreur.

![graphic18][graphic18]

Dans cet exemple, les informations d’identification fournies étaient incorrectes. Vous pouvez corriger les informations d’identification et réessayer en cliquant sur le bouton « TESTER LA CONNEXION ».

![graphic19][graphic19]

## Obtenir de l'aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## Étapes suivantes

- [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d’Azure Stream Analytics](stream-analytics-get-started.md)
- [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)




[graphic1]: ./media/stream-analytics-connect-data-event-input-output/1-stream-analytics-connect-data-event-input-output.png
[graphic2]: ./media/stream-analytics-connect-data-event-input-output/2-stream-analytics-connect-data-event-input-output.png
[graphic3]: ./media/stream-analytics-connect-data-event-input-output/3-stream-analytics-connect-data-event-input-output.png
[graphic4]: ./media/stream-analytics-connect-data-event-input-output/4-stream-analytics-connect-data-event-input-output.png
[graphic5]: ./media/stream-analytics-connect-data-event-input-output/5-stream-analytics-connect-data-event-input-output.png
[graphic6]: ./media/stream-analytics-connect-data-event-input-output/6-stream-analytics-connect-data-event-input-output.png
[graphic7]: ./media/stream-analytics-connect-data-event-input-output/7-stream-analytics-connect-data-event-input-output.png
[graphic8]: ./media/stream-analytics-connect-data-event-input-output/8-stream-analytics-connect-data-event-input-output.png
[graphic9]: ./media/stream-analytics-connect-data-event-input-output/9-stream-analytics-connect-data-event-input-output.png
[graphic10]: ./media/stream-analytics-connect-data-event-input-output/10-stream-analytics-connect-data-event-input-output.png
[graphic11]: ./media/stream-analytics-connect-data-event-input-output/11-stream-analytics-connect-data-event-input-output.png
[graphic12]: ./media/stream-analytics-connect-data-event-input-output/12-stream-analytics-connect-data-event-input-output.png
[graphic13]: ./media/stream-analytics-connect-data-event-input-output/13-stream-analytics-connect-data-event-input-output.png
[graphic14]: ./media/stream-analytics-connect-data-event-input-output/14-stream-analytics-connect-data-event-input-output.png
[graphic15]: ./media/stream-analytics-connect-data-event-input-output/15-stream-analytics-connect-data-event-input-output.png
[graphic16]: ./media/stream-analytics-connect-data-event-input-output/16-stream-analytics-connect-data-event-input-output.png
[graphic17]: ./media/stream-analytics-connect-data-event-input-output/17-stream-analytics-connect-data-event-input-output.png
[graphic18]: ./media/stream-analytics-connect-data-event-input-output/18-stream-analytics-connect-data-event-input-output.png
[graphic19]: ./media/stream-analytics-connect-data-event-input-output/19-stream-analytics-connect-data-event-input-output.png

<!--HONumber=52-->
