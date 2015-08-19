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
	ms.date="08/05/2015"
	ms.author="mandia"/>



# Liste des connecteurs et applications API dans Microsoft Azure App Service
Cette rubrique répertorie tous les connecteurs et toutes les applications API disponibles créés par Microsoft.

Pour plus d’informations sur la tarification et une liste de ce qui est inclus dans chaque niveau de service, consultez la page [Tarification Azure App Service](http://azure.microsoft.com/pricing/details/app-service/).


## Connecteurs standard
Le tableau suivant répertorie tous les connecteurs et toutes les applications API créés par Microsoft et disponibles dans les connecteurs standard :

Nom | Description
--- | ---
[Azure HDInsight](app-service-logic-connector-hdinsight.md) | Utilisez ce connecteur pour créer un cluster Hadoop sur Azure, envoyer différentes tâches Hadoop, etc.
[Azure Service Bus](app-service-logic-connector-azureservicebus.md) | Permet d’envoyer des messages à partir de files d’attente et de rubriques Service Bus, et de recevoir des messages de files d’attente et d’abonnements Service Bus.
[Azure Storage Blob](app-service-logic-connector-azurestorageblob.md) | Se connecte au stockage d'objets blob et permet notamment d’obtenir, de supprimer et de répertorier des objets blob. 
[Box](app-service-logic-connector-box.md) | Se connecte à Box et permet notamment de télécharger, d’obtenir, de supprimer et de répertorier des fichiers.
[Chatter](app-service-logic-connector-chatter.md) | Se connecte à Chatter et permet de publier des messages, de rechercher et même de récupérer de nouveaux messages.
[Dropbox](app-service-logic-connector-dropbox.md) | Se connecte à Box et permet notamment d’obtenir, de supprimer et de répertorier des fichiers.
[Facebook](app-service-logic-connector-facebook.md) | Se connecte à Facebook et permet notamment de publier des messages et des images. Vous pouvez également obtenir des messages et des commentaires, obtenir des informations utilisateur sur les différents » J’aime », comme les livres et les films.
[File](app-service-logic-connector-file.md) | À l'aide de ce connecteur, vous pouvez vous connecter au réseau ou système de fichiers local, et notamment télécharger, supprimer et répertorier des fichiers.
[FTP<br/>FTPS](app-service-logic-connector-ftp.md) | Se connecte à un serveur FTP/FTPS et permet notamment de télécharger; d’obtenir et de supprimer des fichiers.
[HTTP](app-service-logic-connector-http.md) | L'écouteur HTTP ouvre un point de terminaison qui agit comme serveur HTTP et écoute les requêtes HTTP ou HTTPS entrantes. L'action HTTP ne nécessite aucune application API et est prise en charge en mode natif au sein des applications logiques.
[Microsoft Office 365](app-service-logic-connector-office365.md) | Le connecteur Office 365 permet d'envoyer et recevoir des courriers électroniques, de gérer votre calendrier et de gérer vos contacts à l'aide de votre compte Office 365.
[Microsoft OneDrive](app-service-logic-connector-onedrive.md) | Se connecte à votre Microsoft OneDrive personnel et permet notamment de télécharger, de supprimer et de répertorier des fichiers.
[Microsoft SharePoint](app-service-logic-connector-sharepoint.md) | Se connecte au serveur Microsoft SharePoint Server local ou à SharePoint Online et permet de gérer des documents et éléments de liste. Différentes méthodes d'authentification comme les informations d'identification par défaut, OAuth 2.0, l'authentification Windows et l'authentification par formulaire sont prises en charge.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Se connecte au serveur SQL Server local ou à une base de données SQL Azure. Vous pouvez créer, mettre à jour, obtenir et supprimer des entrées dans une table de base de données SQL.
[Microsoft Yammer](app-service-logic-connector-yammer.md) | Se connecte à Yammer pour publier des messages et obtenir de nouveaux messages.
[POP3](app-service-logic-connector-pop3.md) (Post Office Protocol)| Se connecte à un serveur POP3 pour récupérer les messages électroniques avec des pièces jointes.
[QuickBooks](app-service-logic-connector-quickbooks.md) | Vous pouvez notamment créer, mettre à jour, lire, supprimer et interroger différentes entités d'Intuit QuickBooks, comme des clients, articles ou des factures.
[SFTP](app-service-logic-connector-sftp.md) (SSH File Transfer Protocol)| Se connecte à SFTP et permet notamment de télécharger, d’obtenir et de supprimer des fichiers.
[SMTP](app-service-logic-connector-smtp.md) (Simple Mail Transfer Protocol) | Se connecte à un serveur SMTP et permet d'envoyer du courrier électronique avec des pièces jointes.
[Slack](app-service-logic-connector-slack.md) | Se connecte à Slack et publie des messages dans les canaux Slack.
[Salesforce](app-service-logic-connector-salesforce.md) | Se connecte à votre compte Salesforce et permet notamment de gérer des comptes, des prospects et des opportunités.
[SugarCRM](app-service-logic-connector-sugarcrm.md) | Se connecte à SugarCRM Online et permet notamment de créer, de mettre à jour, de lire et de supprimer différents types de modules comme des comptes ou des contacts.
[Twilio](app-service-logic-connector-twilio.md) | Se connecte à Twilio et permet notamment d’envoyer et d’obtenir des messages, d’obtenir des numéros disponibles et de gérer des numéros de téléphone entrants.
[Twitter](app-service-logic-connector-twitter.md) | Se connecte à Twitter et permet notamment de consulter des fils d’actualité et de publier des tweets.
[Wait](app-service-logic-connector-wait.md) | Utilisez ce connecteur pour retarder l'exécution de votre application. Vous pouvez retarder l'application pour une durée spécifique ou jusqu'à une occurrence à un moment donné.


## Connecteurs Premium
Le tableau suivant répertorie les connecteurs et les applications API créés par Microsoft et disponibles dans les connecteurs Premium :

Nom | Description
------------- | -------------
[Connecteur AS2](app-service-logic-connector-as2.md) | Permet de recevoir et d’envoyer des messages à l'aide du protocole de transfert AS2. Les données sont transportées de manière fiable et sécurisée à l’aide de certificats numériques et du chiffrement.
[BizTalk EDIFACT](app-service-logic-connector-edifact.md) | Permet de recevoir et d’envoyer des messages à l’aide du protocole EDIFACT dans les communications B2B (entreprise à entreprise).
[BizTalk X12](app-service-logic-connector-x12.md) | Permet de recevoir et d’envoyer des messages à l’aide du protocole X12 dans les communications B2B (entreprise à entreprise).
[Gestion des partenaires commerciaux BizTalk](app-service-logic-connector-tpm.md) | Permet de définir et de rendre persistantes les relations B2B (entreprise à entreprise) à l'aide de partenaires, contrats, schémas et certificats utilisés dans les contrats. Ces relations sont mises en œuvre à l’aide des applications API AS2, EDIFACT et X12.
[Encodeur JSON BizTalk](app-service-logic-connector-jsonencoder.md) | Un encodeur et décodeur qui facilite l'interopérabilité de votre application entre les données JSON et XML. Il peut convertir une instance JSON donnée au format XML et vice versa.
[Règles BizTalk](app-service-logic-use-biztalk-rules.md) | Utilisez les règles de BizTalk pour définir et contrôler la logique métier d'une organisation. Les stratégies d’entreprise peuvent être mises à jour sans recompilation ou redéploiement des applications associées.
Connecteur DB2 | Se connecte à une base de données IBM DB2, localement et sur une machine virtuelle Azure exécutant un système d’exploitation Windows. Il peut mapper les opérations API web et API OData avec les commandes SQL Informix. <br/><br/>Aucun déclencheur. Les actions comprennent les instructions table select, insert, update, delete et les instructions personnalisées<br/><br/>Ce connecteur comprend également le client Microsoft pour DRDA pour se connecter à un serveur Informix via un réseau TCP/IP.
Informix | Le connecteur DB2 se connecte à une base de données IBM Informix, localement et sur une machine virtuelle Azure exécutant un système d'exploitation Windows. Il peut mapper les opérations API web et API OData avec les commandes SQL Informix.<br/><br/>Aucun déclencheur. Les actions comprennent les instructions table select, insert, update, delete et les instructions personnalisées.<br/><br/>En mode local, VPN ou Azure ExpressRoute peut être utilisé. Ce connecteur comprend également un client Microsoft pour DRDA pour se connecter à un serveur Informix via un réseau TCP/IP.
MQ | Se connecte à une base de données IBM WebSphere MQ Server version 8, sur site et sur une machine virtuelle Azure exécutant un système d'exploitation Windows. Lorsque vous l’utilisez en local, VPN ou ExpressRoute Azure peuvent être utilisés. Ce connecteur comprend également le client Microsoft pour MQ.<br/><br/>Aucun déclencheur. Aucune action.<br/><br/>**Remarque** Ne peut actuellement pas être utilisé avec les applications logiques.
[Oracle Database](app-service-logic-connector-oracle.md) | Se connecte à une base de données Oracle localement et permet de créer, de mettre à jour, d’obtenir et de supprimer des entrées dans une table de la base de données.
[SAP](app-service-logic-connector-sap.md) | Se connecte à un serveur SAP local et appelle des RFC, BAPI et TRFC, et permet d'envoyer des IDOC.
[Encodeur de fichier plat BizTalk](app-service-logic-flatfile-encoder.md) | Fournit une interopérabilité entre des données de fichier plat (comme Excel et csv) et des données XML. Cette application API peut convertir une instance de fichier plat au format XML et vice versa.
[Service BizTalk Transform](app-service-logic-transform-xml-documents.md) | Convertit des données d'un format vers un autre. Vous pouvez également télécharger un mappage existant (fichier .trfm), afficher les liens entre les schémas source et cible, et utiliser une fonctionnalité de test avec un exemple de contenu XML d'entrée. Différentes fonctions intégrées sont également disponibles, notamment les manipulations de chaînes et l’affectation conditionnelle.
[Validateur XML BizTalk](app-service-logic-xml-validator.md) | Valide des données XML par rapport à des schémas XML prédéfinis. Vous pouvez utiliser les schémas existants ou en générer en fonction d’une instance de fichier plat, d’une instance JSON ou de connecteurs existants.
[Extracteur XPath BizTalk](app-service-logic-xpath-extract.md) | Permet de rechercher et d’extraire des données du contenu XML en fonction d’une expression XPath que vous avez choisie.

## Connecteurs en tant que déclencheurs
Plusieurs connecteurs fournissent des déclencheurs pour les applications logiques. Ces déclencheurs sont de deux types :

1. Déclencheurs d'interrogation : ces déclencheurs interrogent votre service selon une fréquence spécifiée pour vérifier l'existence de nouvelles données. Quand de nouvelles données sont disponibles, une nouvelle instance de votre application logique s'exécute avec les données comme entrée. Pour empêcher que les mêmes données soient consommées plusieurs fois, le déclencheur peut nettoyer les données qui ont été lues et transmises à l'application logique. File, SQL et Azure Storage sont des exemples de ce type de connecteur.
2. Déclencheurs d'interrogation : ces déclencheurs écoutent les données sur un point de terminaison ou attendent qu'un événement se produise. Ensuite, ils déclenchent une nouvelle instance d'une application logique. L'Écouteur HTTP et Twitter sont des exemples de ce type de connecteur.

## Connecteurs en tant qu'actions
Vous pouvez aussi utiliser des connecteurs comme actions au sein de votre application logique. Les actions sont utiles pour rechercher des données dans l'application logique qui peuvent ensuite être utilisées lors de l'exécution. Par exemple, vous devrez peut-être rechercher des données à partir d'une base de données SQL afin d'obtenir des informations complémentaires sur un client lors du traitement d'une commande. Ou bien, vous devrez peut-être écrire, mettre à jour ou supprimer des données d'une destination. Pour cela, vous pouvez utiliser les actions fournies par les connecteurs. Les actions mappent aux opérations des applications API (comme défini par leurs métadonnées Swagger).

## Créer vos propres connecteurs et applications API
[Référence de connecteurs et d’applications API](http://aka.ms/appservicesconnectorreference)<br/>
 [Gérer des applications Web dans Azure App Service](../app-service-api/app-service-api-dotnet-triggers.md)<br/>
 [Référence de l’application logique](https://msdn.microsoft.com/library/azure/dn948510.aspx)


## En savoir plus sur les connecteurs et les applications API

[Qu’est-ce qu’un connecteur et une application API BizTalk ?](app-service-logic-what-are-biztalk-api-apps.md)<br/>
 [Utilisation du Gestionnaire des connexions hybrides dans Azure App Service](app-service-logic-hybrid-connection-manager.md)<br/>
 [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md)

<!----HONumber=August15_HO6-->