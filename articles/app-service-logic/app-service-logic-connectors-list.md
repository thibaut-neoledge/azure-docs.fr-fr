<properties 
	pageTitle="Liste des connecteurs et des applications API | Azure" 
	description="En savoir plus sur les connecteurs et les applications API dans Azure App Service ; architecture microservices" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/08/2015" 
	ms.author="mandia"/>


# Liste des connecteurs et applications API dans Microsoft Azure App Service
Cette rubrique répertorie tous les connecteurs et toutes les applications API disponibles créés par Microsoft.

Pour plus d’informations sur la tarification et une liste de ce qui est inclus dans chaque niveau de service, consultez la page [Tarification Azure App Service](http://azure.microsoft.com/pricing/details/app-service/).


## Connecteurs standard
Le tableau suivant répertorie tous les connecteurs et toutes les applications API créés par Microsoft et disponibles dans les connecteurs standard :

Nom | Description
--- | ---
[Azure HDInsight](app-service-logic-connector-hdinsight.md) | Utilisez ce connecteur pour créer un cluster Hadoop sur Azure et soumettre différents travaux Hadoop, notamment Hive, Pig, MapReduce et Streaming MapReduce. Grâce à ce connecteur, vous pouvez également faire tourner un cluster, soumettre un travail et attendre que le travail soit terminé.
[Azure Service Bus](app-service-logic-connector-azureservicebus.md) | À l’aide de ce connecteur, vous pouvez envoyer des messages à partir d’entités Service Bus comme les files d’attente et les rubriques et recevoir des messages d’entités Service Bus comme les files d’attente et les abonnements.
[Azure Storage Blob](app-service-logic-connector-azurestorageblob.md) | Se connecte au stockage d'objets blob et peut télécharger, obtenir, supprimer, copier un objet blob, en répertorier dans un conteneur, en créer un instantané et utiliser un déclencheur pour en récupérer.
Azure WebJobs | Se connecte à WebJobs.
[Box](app-service-logic-connector-box.md) | Se connecte à Box et permet de télécharger, d'obtenir, de supprimer un fichier, de répertorier des fichiers et utilise un déclencheur pour récupérer des fichiers.
[Chatter](app-service-logic-connector-chatter.md) | Se connecte à Chatter et permet de publier un message, de rechercher et d'utiliser un déclencheur pour récupérer les nouveaux messages.
[Dropbox](app-service-logic-connector-dropbox.md) | Se connecte à Dropbox et permet de télécharger, d'obtenir, de supprimer un fichier, de répertorier des fichiers et d'utiliser un déclencheur pour récupérer des fichiers.
[Facebook](app-service-logic-connector-facebook.md) | Se connecte à Facebook et permet de publier un message, une vidéo, des photos, des offres et ainsi de suite. Vous pouvez également récupérer des messages, commentaires, événements, flux d'utilisateur, informations utilisateur sur les différents « J'aime », comme les livres, jeux, films et ainsi de suite. Ce connecteur inclut également un déclencheur pour récupérer les nouveaux messages publiés sur une page.
[File](app-service-logic-connector-file.md) | Permet aux clients d'envoyer ou de télécharger des fichiers vers un système de fichiers ou un réseau, et de recevoir ou télécharger des fichiers à partir d'un système de fichiers ou un réseau. À l’aide de ce connecteur, vous pouvez vous connecter au serveur de fichiers local et télécharger, obtenir, supprimer un fichier, répertorier des fichiers et utiliser un déclencheur pour récupérer des fichiers.
[FTP<br/>FTPS](app-service-logic-connector-ftp.md) | Se connecte à un serveur FTP / FTPS et permet de télécharger, d'obtenir, de supprimer un fichier, de répertorier des fichiers et également d'utiliser un déclencheur pour récupérer des fichiers.
[HTTP](app-service-logic-connector-http.md) | L'écouteur HTTP ouvre un point de terminaison qui agit comme serveur HTTP et écoute les requêtes HTTP entrantes. Vous pouvez effectuer les opérations POST, GET, DELETE et PUT. L'action HTTP ne nécessite aucune application API et est prise en charge en mode natif au sein des applications logiques. Peut utiliser le protocole HTTP ou HTTPS.
[Microsoft Office 365](app-service-logic-connector-office365.md) | Le connecteur Office 365 permet d'envoyer et recevoir des courriers électroniques, de gérer votre calendrier et de gérer vos contacts à l'aide de votre compte Office 365. Vous pouvez également envoyer, recevoir et obtenir des messages électroniques, créer et supprimer des événements dans votre calendrier, ainsi que créer, mettre à jour, obtenir et supprimer vos contacts.
[Microsoft OneDrive](app-service-logic-connector-onedrive.md) | Microsoft OneDrive est un service d'hébergement de fichiers. Ce connecteur OneDrive se connecte à votre Microsoft OneDrive personnel et permet de télécharger, d’obtenir, de supprimer un fichier, de répertorier des fichiers et d’utiliser un déclencheur pour récupérer des fichiers.
[Microsoft SharePoint](app-service-logic-connector-sharepoint.md) | Se connecte au serveur Microsoft SharePoint Server local ou à SharePoint Online et permet de gérer des documents et éléments de liste. Vous pouvez également créer, mettre à jour, obtenir et supprimer des documents et éléments de liste. Différentes méthodes d'authentification comme les informations d'identification par défaut, OAuth 2.0, l'authentification Windows et l'authentification par formulaire sont prises en charge.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Se connecte au serveur SQL Server local ou à une base de données SQL Azure. Vous pouvez créer, mettre à jour, obtenir et supprimer des entrées dans une table de base de données SQL.
[Microsoft Yammer](app-service-logic-connector-yammer.md) | Se connecte à Yammer. Inclut l'action Publier le message et un déclencheur pour extraire les nouveaux messages.
[POP3](app-service-logic-connector-pop3.md) (Post Office Protocol)| Se connecte à un serveur POP3 et inclut un déclencheur pour récupérer les messages électroniques avec des pièces jointes.
[QuickBooks](app-service-logic-connector-quickbooks.md) | À l’aide de ce connecteur, vous pouvez créer, mettre à jour, lire, supprimer et interroger différentes entités d'Intuit QuickBooks, comme des clients, articles, factures, etc.
[SFTP](app-service-logic-connector-sftp.md) (SSH File Transfer Protocol)| Se connecte à SFTP et permet de télécharger, d'obtenir, de supprimer un fichier, de répertorier des fichiers et d'utiliser un déclencheur pour récupérer des fichiers.
[SMTP](app-service-logic-connector-smtp.md) (Simple Mail Transfer Protocol) | Se connecte à un serveur SMTP et permet d'envoyer du courrier électronique avec des pièces jointes.
[Slack](app-service-logic-connector-slack.md) | 
[Salesforce](app-service-logic-connector-salesforce.md) | Le connecteur Salesforce gère différentes entités, comme des comptes, des prospects, des opportunités, des cas, etc. dans votre compte Salesforce. Vous pouvez également créer, mettre à jour, insérer, interroger et supprimer diverses entités.
[SugarCRM](app-service-logic-connector-sugarcrm.md) | Se connecte à SugarCRM Online et permet de créer, mettre à jour, lire et supprimer différents types de modules comme des comptes, des contacts, des produits et ainsi de suite.
[Twilio](app-service-logic-connector-twilio.md) | Se connecte à Twilio et permet d'envoyer, d'obtenir, de répertorier des messages, de répertorier l'utilisation, d'obtenir des numéros verts disponibles, d'obtenir des numéros de portables disponibles, d'obtenir des numéros locaux disponibles, de répertorier les numéros de téléphone entrants, d'obtenir le numéro de téléphone entrant et d'ajouter un numéro de téléphone entrant.
[Twitter](app-service-logic-connector-twitter.md) | Se connecte à Twitter et permet d'obtenir le fil d'actualité de l'utilisateur, de rechercher des tweets, d'obtenir des abonnés, d'obtenir des amis, de rechercher un utilisateur, d'obtenir le fil d'actualité de la page d'accueil, celui des mentions, de publier un tweet, de publier un tweet pour un utilisateur et d'envoyer un message direct. Le connecteur Twitter utilise également des déclencheurs pour obtenir des tweets par mot clé, par descripteur d’utilisateur et par hashtag.
Wait | Utilisez ce connecteur pour retarder l'exécution de votre application. Vous pouvez retarder l'application pour une durée spécifique ou jusqu'à une occurrence à un moment donné.


## Connecteurs Premium
Le tableau suivant répertorie les connecteurs et les applications API créés par Microsoft et disponibles dans les connecteurs Premium :

Nom | Description
------------- | -------------
Connecteur AS2 | Le connecteur AS2 peut recevoir et envoyer des messages via le protocole de transport AS2 dans les communications entreprise-entreprise. Les données sont transportées de manière fiable et sécurisée sur Internet à l’aide de certificats numériques et du chiffrement.
BizTalk EDIFACT | L’application API EDIFACT reçoit et envoie des messages à l’aide du protocole EDIFACT dans les communications entreprise-entreprise.
BizTalk X12 | L’application API X12 reçoit et envoie des messages à l’aide du protocole X12 dans les communications entreprise-entreprise.
Gestion des partenaires commerciaux BizTalk | L'application API TPM (Trading Partner Management) de gestion des partenaires commerciaux définit et rend persistantes les relations entreprise-entreprise à l'aide de partenaires, contrats, schémas et certificats utilisés dans les contrats. Ces relations sont mises en œuvre à l’aide des applications API AS2, EDIFACT et X12.
Encodeur JSON BizTalk | Un encodeur et décodeur qui facilite l'interopérabilité de votre application entre les données JSON et XML. Il peut convertir une instance JSON donnée au format XML et vice versa.
[Règles BizTalk](app-service-logic-use-biztalk-rules.md) | Les règles BizTalk définissent et contrôlent la structure, le fonctionnement et la stratégie d'une organisation. Les stratégies d’entreprise peuvent être mises à jour sans recompilation et sans redéploiement des applications associées.
Connecteur DB2 | Se connecte à une base de données IBM DB2, localement et sur une machine virtuelle Azure exécutant un système d’exploitation Windows. Il peut mapper les opérations API web et API OData avec les commandes SQL Informix. <br/><br/>Aucun déclencheur. Les actions comprennent les instructions table select, insert, update, delete et les instructions personnalisées<br/><br/>Ce connecteur comprend également le client Microsoft pour DRDA pour se connecter à un serveur Informix via un réseau TCP/IP.
Informix | Le connecteur DB2 se connecte à une base de données IBM Informix, localement et sur une machine virtuelle Azure exécutant un système d'exploitation Windows. Il peut mapper les opérations API web et API OData avec les commandes SQL Informix.<br/><br/>Aucun déclencheur. Les actions comprennent les instructions table select, insert, update, delete et les instructions personnalisées.<br/><br/>En mode local, VPN ou Azure ExpressRoute peut être utilisé. Ce connecteur comprend également un client Microsoft pour DRDA pour se connecter à un serveur Informix via un réseau TCP/IP.
MQ | Se connecte à une base de données IBM WebSphere MQ Server version 8, sur site et sur une machine virtuelle Azure exécutant un système d'exploitation Windows. Lorsque vous l’utilisez en local, VPN ou ExpressRoute Azure peuvent être utilisés. Ce connecteur comprend également le client Microsoft pour MQ.<br/><br/>Aucun déclencheur. Aucune action.<br/><br/><strong>Remarque</strong> Ne peut actuellement pas être utilisé avec les applications logiques.
[Oracle Database](app-service-logic-connector-oracle.md) | Le connecteur de base de données Oracle se connecte à une base de données Oracle localement et peut créer, mettre à jour, obtenir et supprimer des entrées dans une table de la base de données.
[SAP](app-service-logic-connector-sap.md) | Le connecteur SAP se connecte à un serveur SAP local et appelle des RFC, BAPI et TRFC, et permet d'envoyer des IDOC.
[Encodeur de fichier plat BizTalk](app-service-logic-flatfile-encoder.md) | Fournit une interopérabilité entre des données de fichier plat (comme Excel et csv) et des données XML. Cette application API peut convertir une instance de fichier plat au format XML et vice versa.
[Service BizTalk Transform](app-service-logic-transform-xml-documents.md) | L'application API BizTalk Transform convertit des données d'un format à l'autre. Vous pouvez également télécharger un mappage existant (fichier .trfm), afficher la représentation graphique du mappage qui affiche les liens entre les schémas source et cible, et utiliser une fonctionnalité de test avec un exemple de contenu XML d'entrée. Différentes fonctions intégrées sont également disponibles, notamment les manipulations de chaînes, l’affectation conditionnelle, les expressions arithmétiques, les formateurs de date et d’heure, l’itération, etc.
[Validateur XML BizTalk](app-service-logic-xml-validator.md) | L'application API Validateur valide les données XML par rapport aux schémas XML prédéfinis. Vous pouvez utiliser les schémas existants ou en générer en fonction d’une instance de fichier plat, d’une instance JSON ou de connecteurs existants.
[Extracteur XPath BizTalk](app-service-logic-xpath-extract.md) | L’application API Extracteur XPath recherche et extrait des données du contenu XML en fonction d’une expression XPath donnée.

## Connecteurs en tant que déclencheurs
Plusieurs connecteurs fournissent des déclencheurs pour les applications logiques. Ces déclencheurs sont de deux types :

1. Déclencheurs d'interrogation : ces déclencheurs interrogent votre service selon une fréquence spécifiée pour vérifier l'existence de nouvelles données. Quand de nouvelles données sont disponibles, une nouvelle instance de votre application logique s'exécute avec les données comme entrée. Pour empêcher que les mêmes données soient consommées plusieurs fois, le déclencheur peut nettoyer les données qui ont été lues et transmises à l'application logique. File, SQL et Azure Storage sont des exemples de ce type de connecteur.
2. Déclencheurs d'interrogation : ces déclencheurs écoutent les données sur un point de terminaison ou attendent qu'un événement se produise. Ensuite, ils déclenchent une nouvelle instance d'une application logique. L'Écouteur HTTP et Twitter sont des exemples de ce type de connecteur.

## Connecteurs en tant qu'actions
Vous pouvez aussi utiliser des connecteurs comme actions au sein de votre application logique. Les actions sont utiles pour rechercher des données dans l'application logique qui peuvent ensuite être utilisées lors de l'exécution. Par exemple, vous devrez peut-être rechercher des données à partir d'une base de données SQL afin d'obtenir des informations complémentaires sur un client lors du traitement d'une commande. Ou bien, vous devrez peut-être écrire, mettre à jour ou supprimer des données d'une destination. Pour cela, vous pouvez utiliser les actions fournies par les connecteurs. Les actions mappent aux opérations des applications API (comme défini par leurs métadonnées Swagger).

## Créer vos propres connecteurs et applications API
[Référence de connecteurs et d'applications API](http://aka.ms/appservicesconnectorreference)<br/> [Déclencheurs Azure App Service et d'application API](../app-service-api/app-service-api-dotnet-triggers.md)


## Autres connecteurs

[Que sont les connecteurs et les applications API BizTalk](app-service-logic-what-are-biztalk-api-apps.md)<br/> [Utilisation du Gestionnaire des connexions hybrides dans Azure App Service](app-service-logic-hybrid-connection-manager.md)

<!---HONumber=62-->