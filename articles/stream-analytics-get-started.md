<properties linkid="manage-services-Stream-Analytics-get-started" urlDisplayName="Get Started" pageTitle="Prise en main d'Azure Stream Analytics | Azure" metaKeywords="" description="Prise en main de Azure Stream Analytics pour traiter et transformer les événements dans le concentrateur d'événements Azure Service Bus et stocker les résultats dans la base de données SQL Azure." metaCanonical="" services="stream analytics" documentationCenter="" title="Get started with Azure Stream Analytics" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="stream analytics" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/28/2014" ms.author="jgao" />


# Prise en main d'Azure Stream Analytics

Azure Stream Analytics est un service entièrement géré permettant de traiter des événements avec une latence faible, une haute disponibilité et de façon évolutive via des données de diffusion dans le cloud. Pour plus d'informations, consultez [Présentation d'Azure Stream Analytics][stream.analytics.introduction] et [Documentation d'Azure Stream Analytics][stream.analytics.documentation].

Pour vous aider à prendre rapidement en main Stream Analytics, ce didacticiel va vous expliquer comment utiliser des données de température en lisant des données à partir d'un [Concentrateur d'événements Azure Service Bus][azure.event.hubs.documentation] avant de traiter les données provenant des résultats d'une [base de données SQL Azure][azure.sql.database.documentation].  Le schéma suivant illustre le flux d'événements de l'entrée vers le traitement en sortie :
  
![Azure Stream Analytics get started flow][img.get.started.flowchart]

##Génération de données d'exemple de concentrateur d'événements
Ce didacticiel repose sur l'application du didacticiel Prise en main du concentrateur d'événements Service Bus, un exemple de code situé dans la galerie de codes MSDN, pour créer un concentrateur d'événements, générer des exemples de lectures de température de périphérique et envoyer ces données vers le concentrateur d'événements.

###Création d'un espace de noms Service Bus
L'exemple d'application va créer un concentrateur d'événements dans un espace de noms Service Bus préexistant.  Vous pouvez utiliser un espace de noms Service Bus que vous avez déjà configuré ou suivre la procédure ci-dessous pour en créer un :

1.	Connectez-vous au [portail de gestion Azure][azure.management.portal].
2.	Cliquez sur **CRÉER** en bas de la page Service Bus, puis suivez les instructions pour créer un espace de noms. Utilisez le type **MESSAGERIE**.
3.	Cliquez sur le nouvel espace de noms, puis sur **INFORMATIONS DE CONNEXION** en bas de la page.
4.	Copiez la chaîne de connexion. Vous en aurez besoin plus loin dans le didacticiel.

###Création d'un compte Azure Storage

Cet exemple d'application requiert un compte Azure Storage ou un émulateur de stockage pour la conservation de l'état de l'application. Vous pouvez utiliser un compte Storage existant ou suivre la procédure ci-dessous pour en créer un : 

1.	Dans le portail, créez un compte Storage en cliquant sur **NOUVEAU**, **SERVICES DE DONNÉES**, **STOCKAGE**, **CRÉATION RAPIDE**, puis suivez les instructions.
2.	Sélectionnez le nouveau compte de stockage, puis cliquez sur **GÉRER LES CLÉS D'ACCÈS** en bas de la page.
3.	Copiez le nom du compte de stockage et l'une des clés d'accès.

###Génération de données d'exemple de concentrateur d'événements

1.	Téléchargez et décompressez le fichier [Service Bus Event Hubs Getting Started.zip](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Event-Hub-286fd097) dans votre station de travail.
2.	Ouvrez le fichier de solution **EventHubSample.sln** dans Visual Studio.
3.	Ouvrez le fichier **app.config**.
4.	Indiquez les chaînes de connexion au compte Storage et à Service Bus. Les noms de clé sont **Microsoft.ServiceBus.ConnectionString** et **AzureStorageConnectionString**. Le format de la chaîne de connexion au compte de stockage est le suivant : 	

		DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<yourAccountKey>;
5.	Générez la solution.
6.	Exécutez l'application à partir du dossier Bin.  Procédez comme suit : 

		BasicEventHubSample <eventhubname> <NumberOfMessagesToSend> <NumberOfPartitions> 

	L'exemple suivant crée un concentrateur d'événements nommé **devicereadings** avec **16** partitions, puis envoie **200** événements vers le concentrateur d'événements : 

		BasicEventHubSample devicereadings 200 16

 	![insert image here][img.stream.analytics.event.hub.client.output] 
 	

###Création d'une stratégie d'accès partagé au concentrateur d'événements
Même s'il existe déjà une stratégie d'accès partagé sur l'espace de noms Service Bus et qu'elle permet de se connecter à tous les éléments dans l'espace de noms, pour plus de sécurité, nous allons créer une stratégie distincte pour le concentrateur d'événements.

1.	Dans l'espace de travail Service Bus du portail, cliquez sur le nom de l'espace de noms Service Bus.
2.	Cliquez sur **CONCENTRATEURS D'ÉVÉNEMENTS** en haut de la page.
3.	Cliquez sur **devicereadings**, le concentrateur d'événements de ce didacticiel.
4.	Cliquez sur **CONFIGURER** en haut de la page.
5.	Sous Stratégies d'accès partagé, créez une stratégie ayant les autorisations **Gérer**.

	![][img.stream.analytics.event.hub.shared.access.policy.config]
6.	Cliquez sur **ENREGISTRER** en bas de la page.
7.	Si le concentrateur d'événements est dans un autre abonnement que celui du travail Stream Analytics, vous devez copier et enregistrer les informations de connexion pour les utiliser plus tard.  Pour ce faire, cliquez sur **﻿﻿TABLEAU DE BORD**, puis sur **﻿﻿INFORMATIONS DE CONNEXION** en bas de la page, puis enregistrez la chaîne de connexion.


##Préparation d'une base de données SQL Azure pour stocker des données de sortie
Azure Stream Analytics peut sortir des données vers une base de données SQL Azure, un stockage d'objets blob Azure et un concentrateur d'événements Azure. Dans ce didacticiel, vous allez définir un travail qui génère une sortie vers une base de données SQL Azure. Pour plus d'informations, consultez la rubrique Prise en main de Base de données SQL Microsoft SQL.

###Création d'une base de données SQL Azure
Si vous disposez déjà d'une base de données SQL Azure pour ce didacticiel, ignorez cette section.

1.	Dans le portail de gestion, cliquez sur **NOUVEAU**, **SERVICES DE DONNÉES**, **BASE DE DONNÉES SQL**, **CRÉATION RAPIDE**.  Spécifiez un nom de base de données sur un serveur, nouveau ou existant, de base de données SQL.
2.	Sélectionnez la base de données nouvellement créée
3.	Cliquez sur **TABLEAU DE BORD**, sur **Afficher les chaînes de connexion** sur le volet droit de la page, puis copiez la chaîne de connexion **ADO.NET**. Vous en aurez besoin plus loin dans le didacticiel.  
4.	Assurez-vous que les paramètres de pare-feu au niveau du serveur permettent de vous connecter à la base de données.  Pour ce faire, ajoutez une nouvelle règle IP sous l'onglet Configuration du serveur. Pour plus d'informations, notamment sur la gestion d'adresses IP dynamiques, consultez [http://msdn.microsoft.com/fr-fr/library/azure/ee621782.aspx](http://msdn.microsoft.com/fr-fr/library/azure/ee621782.aspx).

###Création de tables de sortie
1.	Ouvrez Visual Studio ou SQL Server Management Studio.
2.	Connectez-vous à la base de données SQL Azure.
3.	Utilisez les instructions T-SQL suivantes pour créer deux tables pour votre base de données :

		CREATE TABLE [dbo].[PassthroughReadings] (
		    [DeviceId]      BIGINT NULL,
			[Temperature] BIGINT    NULL
		);

		GO
		CREATE CLUSTERED INDEX [PassthroughReadings]
		    ON [dbo].[PassthroughReadings]([DeviceId] ASC);
		GO

		CREATE TABLE [dbo].[AvgReadings] (
		    [WinStartTime]   DATETIME2 (6) NULL,
		    [WinEndTime]     DATETIME2 (6) NULL,
		    [DeviceId]      BIGINT NULL,
			[AvgTemperature] FLOAT (53)    NULL,
			[EventCount] BIGINT null
		);
		
		GO
		CREATE CLUSTERED INDEX [AvgReadings]
		    ON [dbo].[AvgReadings]([DeviceId] ASC);

	>[WACOM.NOTE] Pour insérer des données, les tables de base de données SQL doivent avoir des index cluster.
	   
##Création d'un travail d'analyse de flux

Après avoir créé le concentrateur d'événements Azure Service Bus, la base de données SQL Azure et les tables de sortie, vous êtes prêt à créer un travail Stream Analytics.

###Configuration d'un travail Stream Analytics
1.	Dans le portail de gestion, cliquez sur **NOUVEAU**, **SERVICES DE DONNÉES**, **﻿STREAM ANALYTICS**, **CRÉATION RAPIDE**. 
2.	Spécifiez les valeurs suivantes, puis cliquez sur **CRÉER UN TRAVAIL STREAM ANALYTICS** :

	- **NOM DU TRAVAIL** : entrez un nom pour le travail. Par exemple, **DeviceTemperatures**.
	- **RÉGION** : sélectionnez la région où vous souhaitez exécuter le travail. La version d'évaluation d'Azure Stream Analytics est actuellement disponible uniquement dans 2 régions. Pour plus d'informations, consultez la page [Limitations et problèmes connus d'Azure Stream Analytics][stream.analytics.limitations]. Envisagez de placer le travail et le concentrateur d'événements dans la même région afin d'améliorer les performances et d'éviter de payer pour un transfert de données entre différentes régions.
	- **COMPTE DE STOCKAGE** : choisissez le compte de stockage que vous souhaitez utiliser pour stocker les données de surveillance de tous les travaux Stream Analytics en cours d'exécution dans cette région. Vous pouvez choisir un compte de stockage existant ou en créer un.
	
3.	Dans le volet gauche, cliquez sur **STREAM ANALYTICS** pour afficher une liste des travaux Stream Analytics.

	![][img.stream.analytics.portal.button]
 
	Le nouveau travail sera affiché avec l'état **NON DÉMARRÉ**.  Notez que le bouton **DÉMARRER** situé en bas de la page est désactivé. Avant de pouvoir démarrer le travail, vous devez configurer son entrée, sa sortie, sa requête, etc. 

###Spécification d'une entrée de travail

1.	Cliquez sur le nom du travail.
2.	En haut de la page, cliquez sur **ENTRÉES**, puis sur **AJOUTER UNE ENTRÉE**. La boîte de dialogue qui s'ouvre vous guidera le long d'une procédure de configuration de votre entrée.
3.	Sélectionnez **FLUX DE DONNÉES**, puis cliquez avec le bouton droit.
4.	Sélectionnez **CONCENTRATEUR D'ÉVÉNEMENTS**, puis cliquez avec le bouton droit.
5.	Saisissez ou sélectionnez les valeurs suivantes sur la troisième page : 

	- **ALIAS D'ENTRÉE** : entrez un nom convivial pour cette entrée de travail. Notez que vous utiliserez ce nom dans la requête par la suite.
	- **CONCENTRATEUR D'﻿﻿﻿ÉVÉNEMENTS** : si le concentrateur d'événements que vous avez créé est situé dans le même abonnement que le travail Stream Analytics, sélectionnez l'espace de noms dans lequel est situé le concentrateur d'événements.  

		Si votre concentrateur d'événements est situé dans un autre abonnement, sélectionnez **Utiliser le concentrateur d'événements à partir d'un autre abonnement** et entrez l'**ESPACE DE NOMS SERVICE BUS**, le **NOM ﻿﻿DU CONCENTRATEUR D'ÉVÉNEMENTS**, le **﻿﻿NOM DE LA STRATÉGIE DU CONCENTRATEUR D'ÉVÉNEMENTS**, la **﻿﻿CLÉ DE STRATÉGIE DU CONCENTRATEUR D'ÉVÉNEMENTS** et le **﻿﻿NOMBRE DE PARTITIONS DU CONCENTRATEUR D'ÉVÉNEMENTS**.  

		>[WACOM.NOTE] Cet exemple utilise le nombre de partitions par défaut : 16.
		
	- **NOM DU CONCENTRATEUR D'﻿﻿﻿ÉVÉNEMENTS** : sélectionnez le nom du concentrateur d'événements Azure que vous avez créé. Pour ce didacticiel, utilisez **devicereadings**.
	- **NOM DE LA STRATÉGIE DU CONCENTRATEUR D'﻿﻿﻿ÉVÉNEMENTS** : sélectionnez la stratégie de concentrateur d'événements créée précédemment dans ce didacticiel.
 
	![][img.stream.analytics.config.input]

6.	Cliquez avec le bouton droit.
7.	Spécifiez les valeurs suivantes :

	- **FORMAT ﻿﻿﻿DU SÉRIALISEUR D'ÉVÉNEMENT** : JSON
	- **ENCODAGE** : UTF8

8.	Cliquez sur le bouton de vérification pour ajouter cette source et vérifier que Stream Analytics peut se connecter au concentrateur d'événements.

###Spécification de la sortie du travail
1.	En haut de la page, cliquez sur **SORTIE**, puis sur **AJOUTER UNE SORTIE**.
2.	Sélectionnez **BASE DE DONNÉES SQL**, puis cliquez avec le bouton droit.
3.	Saisissez ou sélectionnez les valeurs suivantes :  utilisez la chaîne de connexion ADO.NET à partir de votre base de données pour renseigner les champs suivants :

	- **BASE ﻿﻿﻿DE DONNÉES SQL** : sélectionnez la base de données SQL créée précédemment dans ce didacticiel.  Si elle est située dans le même abonnement, sélectionnez la base de données dans le menu déroulant.   Si ce n'est pas le cas, remplissez manuellement les champs Nom du serveur et Base de données. 
	- **NOM D'UTILISATEUR** : entrez le nom de connexion à la base de données SQL Azure.
	- **﻿MOT DE PASSE** : entrez le mot de passe de connexion à la base de données SQL Azure.
	- **﻿TABLE** : spécifiez la table vers laquelle vous souhaitez envoyer la sortie.  Pour le moment, utilisez **PassthroughReadings**.

	![][img.stream.analytics.config.output]

4.	Cliquez sur le bouton en forme de coche pour créer votre sortie et vérifiez que Stream Analytics peut se connecter à la base de données SQL, comme indiqué.

###Spécification de la requête du travail
Stream Analytics prend en charge un modèle de requête simple et déclaratif pour la description des transformations.  Pour plus d'informations sur ce langage, consultez les références sur le langage des requêtes d'Azure Stream Analytics.  

Ce didacticiel commence par une simple requête directe qui génère des relevés de température du périphérique transmis vers une table de base de données SQL.

1.	En haut de la page, cliquez sur **Requête**.
2.	Ajoutez le code suivant dans l'éditeur de code :

		SELECT DeviceId, Temperature FROM input
Assurez-vous que le nom de la source d'entrée correspond à celui que vous avez spécifié précédemment.
3.	En bas de la page, cliquez sur **﻿ENREGISTRER**, puis sur **OUI** pour confirmer.

##Démarrage du travail
Par défaut, les travaux Stream Analytics commencent à lire les événements entrants à partir de l'heure à laquelle le travail commence.  Étant donné que le concentrateur d'événements contient des données à traiter, nous devons configurer le travail pour qu'il traite ces données d'historique.  

1.	En haut de la page, cliquez sur **CONFIGURER**.
2.	Remplacez la valeur de **﻿DÉMARRER LA SORTIE** par **HORAIRE PERSONNALISÉ**, puis spécifiez un horaire personnalisé.  Assurez-vous que l'horaire de démarrage est antérieur à l'horaire d'exécution de BasicEventHubSample.  
3.	En bas de la page, cliquez sur **﻿ENREGISTRER**, puis sur **OUI** pour confirmer.
3.	En haut de la page, cliquez sur **TABLEAU DE BORD**, puis en bas de la page, cliquez sur **DÉMARRER** et sur **OUI** pour confirmer.  Dans le volet **Aperçu rapide**, l'**ÉTAT** passe à **Démarrage**. Le processus de démarrage peut prendre quelques minutes avant que l'état ne passe à **En cours d'exécution**.   


##Affichage de la sortie du travail

1.	Dans Visual Studio ou SQL Server Management Studio, connectez-vous à votre base de données SQL et exécutez la requête suivante : 

		SELECT * FROM PassthroughReadings

2.	Vous verrez les enregistrements correspondant aux événements de lecture à partir du concentrateur d'événements.   

	![][img.stream.analytics.job.output1]

	Vous pouvez réexécuter l'application BasicEventHubSample pour générer de nouveaux événements et les voir se propager vers la sortie en temps réel en réexécutant la requête SELECT *.
	
	Si vous rencontrez des problèmes avec une sortie manquante ou inattendue, affichez les journaux des opérations du travail, liés dans le volet droit de la page du tableau de bord.

##Arrêt, mise à jour et redémarrage du travail
﻿À présent, nous allons exécuter une requête plus intéressante sur ces données.
1.	Dans la page **TABLEAU DE BORD** ou **SURVEILLER**, cliquez sur **ARRÊTER**.
2.	Dans la page **REQUÊTE**, remplacez la requête existante par celle affichée plus bas, puis cliquez sur **ENREGISTRER** :

		SELECT DateAdd(second,-5,System.TimeStamp) as WinStartTime, system.TimeStamp as WinEndTime, DeviceId, Avg(Temperature) as AvgTemperature, Count(*) as EventCount 
		FROM input
		GROUP BY TumblingWindow(second, 5), DeviceId

	Cette requête utilise l'horaire d'envoi de l'événement au concentrateur d'événements défini par l'horodatage, recherche et projette la température moyenne lue toutes les 5 secondes et le nombre d'événements situés sous cette durée de 5 secondes.
3.	Dans la page **SORTIE**, cliquez sur **MODIFIER**.  Remplacez la table de sortie PassthroughReadings par AvgReadings, puis cliquez sur l'icône en forme de coche.

4.	Dans la page **TABLEAU DE BORD**, cliquez sur **DÉMARRER**.

##Affichage de la sortie du travail

1.	Dans Visual Studio ou SQL Server Management Studio, connectez-vous à votre base de données SQL et exécutez la requête suivante :

		SELECT * from AvgReadings

2.	Vous verrez les enregistrements correspondant à des intervalles de 5 secondes, qui indiquent la température moyenne et le nombre d'événements pour chaque périphérique : 

	![][img.stream.analytics.job.output2]
 
3.	 Pour continuer à afficher des événements traités par le travail en cours d'exécution, réexécutez l'application BasicEventHubSample.







##<a name="nextsteps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à utiliser Stream Analytics pour traiter les données météorologiques. Pour en savoir plus, consultez les articles suivants :


- [Présentation d'Azure Stream Analytics][stream.analytics.introduction]
- [Guide de développement pour Azure Stream Analytics][stream.analytics.developer.guide]
- [Mise à l'échelle des travaux Azure Stream Analytics][stream.analytics.scale]
- [Limitations et problèmes connus d'Azure Stream Analytics][stream.analytics.limitations]
- [Références sur le langage des requêtes d'Azure Stream Analytics][stream.analytics.query.language.reference]
- [Références sur l'API REST de gestion d'Azure Stream Analytics][stream.analytics.rest.api.reference]




[img.stream.analytics.event.hub.client.output]: .\media\stream-analytics-get-started\AzureStreamAnalyticsEHClientOuput.png
[img.stream.analytics.event.hub.shared.access.policy.config]: .\media\stream-analytics-get-started\AzureStreamAnalyticsEHSharedAccessPolicyConfig.png
[img.stream.analytics.job.output2]: .\media\stream-analytics-get-started\AzureStreamAnalyticsSQLOutput2.png
[img.stream.analytics.job.output1]: .\media\stream-analytics-get-started\AzureStreamAnalyticsSQLOutput1.png
[img.stream.analytics.config.output]: .\media\stream-analytics-get-started\AzureStreamAnalyticsConfigureOutput.png
[img.stream.analytics.config.input]: .\media\stream-analytics-get-started\AzureStreamAnalyticsConfigureInput.png



[img.get.started.flowchart]: ./media/stream-analytics-get-started/StreamAnalytics.get.started.flowchart.png
[img.job.quick.create]: ./media/stream-analytics-get-started/StreamAnalytics.quick.create.png
[img.stream.analytics.portal.button]: ./media/stream-analytics-get-started/StreamAnalyticsPortalButton.png
[img.event.hub.policy.configure]: ./media/stream-analytics-get-started/StreamAnalytics.Event.Hub.policy.png
[img.create.table]: ./media/stream-analytics-get-started/StreamAnalytics.create.table.png
[img.stream.analytics.job.output]: ./media/stream-analytics-get-started/StreamAnalytics.job.output.png
[img.stream.analytics.operation.logs]: ./media/stream-analytics-get-started/StreamAnalytics.operation.log.png
[img.stream.analytics.operation.log.details]: ./media/stream-analytics-get-started/StreamAnalytics.operation.log.details.png


[azure.sql.database.firewall]: http://msdn.microsoft.com/fr-fr/library/azure/ee621782.aspx
[azure.event.hubs.documentation]: http://azure.microsoft.com/fr-fr/services/event-hubs/
[azure.sql.database.documentation]: http://azure.microsoft.com/fr-fr/services/sql-database/

[sql.database.introduction]: http://azure.microsoft.com/fr-fr/services/sql-database/
[event.hubs.introduction]: http://azure.microsoft.com/fr-fr/services/event-hubs/
[azure.blob.storage]: http://azure.microsoft.com/fr-fr/documentation/services/storage/
[azure.sdk.net]: ../dotnet-sdk/

[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.scale]: ../stream-analytics-scale-jobs/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093




[azure.management.portal]: https://manage.windowsazure.com

