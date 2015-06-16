<properties 
   pageTitle="Utilisation de connecteurs" 
   description="Utilisation de connecteurs" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="prkumar"/>

#Connecteurs#
Les connecteurs sont des applications API qui vous permettent de vous connecter à des services et données qui s’exécutent dans le cloud ou localement. Les connecteurs permettent entre autre d’accéder facilement aux données avec différents déclencheurs et actions intégrés facilement accessibles dans Logic Apps.

Azure App Service fournit un certain nombre de connecteurs prêts à l’emploi :

##Connecteurs standard##
* [Connecteur Azure Service Bus]
* [Connecteur d’objet blob de stockage Azure]
* Connecteur Azure Webjobs
* [Connecteur Box]
* [Connecteur Chatter]
* [Connecteur Dropbox]
* [Connecteur Facebook]
* [Connecteur File]
* [Connecteur FTP]
* [Connecteur HDInsight]
* [Connecteur HTTP]
* [Connecteur Office 365]
* [Connecteur OneDrive]
* [Connecteur Oracle]
* [Connecteur POP3]
* [Connecteur QuickBooks]
* [Connecteur Salesforce]
* [Connecteur SFTP]
* [Connecteur Sharepoint]
* [Connecteur Slack]
* [Connecteur SMTP]
* [Connecteur SQL]
* [Connecteur SugarCRM]
* [Connecteur Twilio]
* [Connecteur Twitter]
* Connecteur Wait
* [Connecteur Yammer]

##Connecteurs Premium et API Apps##
* Connecteur AS2
* BizTalk EDIFACT
* Encodeur de fichier plat BizTalk
* Service BizTalk Transform
* Règles BizTalk
* BizTalk X12
* Extracteur XPath BizTalk
* Validateur XML BizTalk
* Connecteur DB2
* Connecteur Informix
* Connecteur de base de données Oracle
* Connecteur MQ
* [Connecteur SAP]
 
Pour plus d’informations, consultez la référence d’API de connecteur [http://aka.ms/appservicesconnectorreference](http://aka.ms/appservicesconnectorreference).

##Connecteurs et applications logiques##
Les applications logiques sont composées de déclencheurs et d'actions. Certains connecteurs peuvent servir de déclencheurs pour instancier un flux de travail basé sur un événement ou la disponibilité de certaines données. Ils sont également utilisés comme actions pour lire et écrire des données ou effectuer d’autres actions prises en charge.

###Connecteurs en tant que déclencheurs###
Plusieurs connecteurs fournissent des déclencheurs pour les applications logiques. Ces déclencheurs sont de deux types :

1. Déclencheurs d’interrogation : ces déclencheurs interrogent le service de votre choix selon une fréquence spécifiée pour vérifier l’existence de nouvelles données. Quand de nouvelles données sont disponibles, une nouvelle instance de votre application logique s'exécute avec les données comme entrée. Le déclencheur peut effectuer des tâches supplémentaires comme le nettoyage des données qui ont été lues et transmises à l'application logique, pour empêcher que les mêmes données soient consommées plusieurs fois. Fichier, SQL et Azure Storage sont des exemples de ce type de connecteur.
2. Déclencheurs d’interrogation : ces déclencheurs écoutent les données sur un point de terminaison ou attendent qu’un événement se produise pour déclencher une nouvelle instance d’une application logique. L'Écouteur HTTP et Twitter sont des exemples de ce type de connecteur.

###Connecteurs en tant qu'actions###
Vous pouvez aussi utiliser des connecteurs comme actions dans le cadre de votre application logique. Ces actions peuvent être utiles pour rechercher des données dans l’application logique à utiliser lors de l’exécution, par exemple si vous devez rechercher des données dans une base de données SQL pour obtenir des informations supplémentaires sur un client lors du traitement d’une commande. Il se peut également que vous deviez écrire, mettre à jour ou supprimer des données dans une destination, auquel cas vous pouvez utiliser les actions fournies par les connecteurs. Les actions mappent aux opérations des applications API (comme défini par leurs métadonnées Swagger).


<!-- Links -->

[Connecteur Box]: app-service-logic-connector-box.md
[Connecteur Facebook]: app-service-logic-connector-facebook.md
[Connecteur Salesforce]: app-service-logic-connector-salesforce.md
[Connecteur Twitter]: app-service-logic-connector-twitter.md
[Connecteur SAP]: app-service-logic-connector-sap.md
[Connecteur FTP]: app-service-logic-connector-ftp.md
[Connecteur HTTP]: app-service-logic-connector-http.md
[Connecteur d’objet blob de stockage Azure]: app-service-logic-connector-azurestorageblob.md
[Connecteur Office 365]: app-service-logic-connector-office365.md
[Connecteur Sharepoint]: app-service-logic-connector-sharepoint.md
[Connecteur SugarCRM]: app-service-logic-connector-sugarcrm.md
[Connecteur QuickBooks]: app-service-logic-connector-quickbooks.md
[Connecteur Yammer]: app-service-logic-connector-yammer.md
[Connecteur Twilio]: app-service-logic-connector-twilio.md
[Connecteur SMTP]: app-service-logic-connector-smtp.md
[Connecteur SFTP]: app-service-logic-connector-sftp.md
[Connecteur POP3]: app-service-logic-connector-pop3.md
[Connecteur Dropbox]: app-service-logic-connector-dropbox.md
[Connecteur Chatter]: app-service-logic-connector-chatter.md
[Connecteur HDInsight]: app-service-logic-connector-hdinsight.md
[Connecteur Azure Service Bus]: app-service-logic-connector-azureservicebus.md
[Connecteur Oracle]: app-service-logic-connector-oracle.md
[Connecteur SQL]: app-service-logic-connector-sql.md
[Connecteur OneDrive]: app-service-logic-connector-onedrive.md
[Connecteur File]: app-service-logic-connector-file.md
[Connecteur Slack]: app-service-logic-connector-slack.md


<!--HONumber=54--> 