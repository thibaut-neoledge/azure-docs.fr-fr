<properties
	pageTitle="Liste des API gérées par Microsoft | Microsoft Azure App Service"
	description="Obtenez une liste complète des API gérées par Microsoft que vous pouvez utiliser pour créer des applications logiques dans Azure App Service"
	services="app-service\logic"
	documentationCenter=""
	authors="MSFTMAN"
	manager="erikre"
	editor=""
    tags="connectors"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/02/2016"
	ms.author="deonhe"/>

# Liste des API gérées

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques. Pour la version de schéma 2014-12-01-preview, cliquez sur [Liste des connecteurs](../app-service-logic/app-service-logic-connectors-list.md).

Sélectionnez une icône afin d’apprendre rapidement comment tirer parti de ces API pour créer des applications qui appellent ces services. Ces API peuvent être utilisées pour créer des applications logiques, des applications PowerApps, ou les deux.

Pour plus d’informations sur la tarification et une liste de ce qui est inclus dans chaque niveau de service, consultez la page [Tarification Azure App Service](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Si vous souhaitez commencer à utiliser Azure Logic Apps avant d’ouvrir un compte Azure, accédez à [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic). Vous pouvez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

|API||||
|-----------|-----------|-----------|-----------|
|[![Icône API][blobicon]<br/>**Objets blob Azure**][azureblobdoc]|[![Icône API][bingsearchicon]<br/>**Recherche Bing**][bingsearchdoc]|[![Icône API][boxicon]<br/>**Zone**][boxDoc]|[![Icône API][crmonlineicon]<br/>**CRM Online**][crmonlinedoc]|
|[![Icône API][dropboxicon]<br/>**Dropbox**][dropboxdoc]|[![Icône API][facebookicon]<br/>**Facebook**][facebookdoc]|[![Icône API][ftpicon]<br/>**FTP**][ftpdoc]|[![Icône API][googledriveicon]<br/>**Google Drive**][googledrivedoc]|
|[![Icône API][microsofttranslatoricon]<br/>**Translator**][microsofttranslatordoc]|[![Icône API][office365icon]<br/>**Office 365**<br/>**Outlook**][office365outlookdoc]|[![Icône API][office365icon]<br/>**Office 365**<br/>**Utilisateurs**][office365usersdoc]|[![Icône API][office365icon]<br/>**Office 365**<br/>**Vidéo**][office365videodoc]|
|[![Icône API][onedriveicon]<br/>**OneDrive**][onedrivedoc]|[![Icône API][salesforceicon]<br/>**Salesforce**][salesforcedoc]|[![Icône API][servicebusicon]<br/>**Service Bus**][servicebusdoc]|[![Icône API][sftpicon]<br/>**SFTP**][sftpdoc]|
|[![Icône API][sharepointicon]<br/>**SharePoint**<br/>**Online**][sharepointdoc]|[![Icône API][slackicon]<br/>**Slack**<br/>][slackdoc]|[![Icône API][smtpicon]<br/>**SMTP**][smtpdoc]|[![Icône API][sqlicon]<br/>**SQL Azure**][sqldoc]|
|[![Icône API][twilioicon]<br/>**Twilio**][twiliodoc]|[![Icône API][twittericon]<br/>**Twitter**][twitterdoc]|[![Icône API][yammericon]<br/>**Yammer**][yammerdoc] | |


### Les API peuvent être des déclencheurs
Plusieurs API fournissent des déclencheurs qui préviennent votre application lorsque des événements spécifiques se produisent. Par exemple, l'API FTP comporte le déclencheur OnUpdatedFile. Vous pouvez créer une application logique ou une application PowerApp qui écoute ce déclencheur et effectue une action chaque fois que le déclencheur est activé.

Il existe deux types de déclencheurs :

* Déclencheurs d'interrogation : ces déclencheurs interrogent votre service selon une fréquence spécifiée pour vérifier l'existence de nouvelles données. Quand de nouvelles données sont disponibles, une nouvelle instance de votre application s'exécute avec les données comme entrée. Pour empêcher que les mêmes données soient consommées plusieurs fois, le déclencheur peut nettoyer les données qui ont été lues et transmises à votre application.
* Déclencheurs d'interrogation : ces déclencheurs écoutent les données sur un point de terminaison ou attendent qu'un événement se produise. Ensuite, ils déclenchent une nouvelle instance de votre application. L'API twitter en est un exemple.


### Les API peuvent être des actions
Vous pouvez aussi utiliser des API comme actions au sein de vos applications. Les actions sont utiles pour rechercher des données qui peuvent ensuite être utilisées lors de l'exécution de votre application. Par exemple, vous devrez peut-être rechercher des données client à partir d'une base de données SQL lors du traitement d'une commande. Ou bien, vous devrez peut-être écrire, mettre à jour ou supprimer des données d'une table de destination. Pour cela, vous pouvez utiliser les actions fournies par les API. Les actions correspondent aux opérations définies dans les métadonnées Swagger.


[Nouveautés](../app-service-logic/app-service-logic-schema-2015-08-01.md) [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md) [Prise en main des applications PowerApps](../power-apps/powerapps-get-started-azure-portal.md)

<!--API Documentation-->
[azureblobdoc]: ./create-api-azureblobstorage.md "Connectez-vous à un objet blob Azure pour gérer les fichiers de votre conteneur d'objets blob."
[bingsearchDoc]: ./create-api-bingsearch.md "Effectuez des recherches web, d’images, d’actualités et de vidéos dans Bing."
[boxDoc]: ./create-api-box.md "Se connecte à Box et permet notamment de télécharger, d’obtenir, de supprimer et de répertorier des fichiers."
[crmonlinedoc]: ./create-api-crmonline.md "Connectez-vous à Dynamics CRM Online et faites en plus avec vos données CRM Online."
[dropboxdoc]: ./create-api-dropbox.md "Se connecte à Box et permet notamment d’obtenir, de supprimer et de répertorier des fichiers."
[exceldoc]: ./create-api-excel.md "Connectez-vous à Excel."
[facebookdoc]: ./create-api-facebook.md "Connectez-vous à Facebook pour publier dans un journal, obtenir un flux de page et bien plus encore."
[ftpdoc]: ./create-api-ftp.md "Se connecte à un serveur FTP/FTPS et permet notamment de télécharger; d’obtenir et de supprimer des fichiers."
[googledrivedoc]: ./create-api-googledrive.md "Connectez-vous à Google Drive et interagissez avec vos données."
[microsofttranslatordoc]: ./create-api-microsofttranslator.md
[office365outlookdoc]: ./create-api-office365-outlook.md "Le connecteur Office 365 permet d'envoyer et recevoir des courriers électroniques, de gérer votre calendrier et de gérer vos contacts à l'aide de votre compte Office 365."
[officeunifieddoc]: ./create-api-bingsearch.md
[office365usersdoc]: ./create-api-office365-users.md
[office365videodoc]: ./create-api-office365-video.md
[onedrivedoc]: ./create-api-onedrive.md "Se connecte à votre Microsoft OneDrive personnel et permet notamment de télécharger, de supprimer et de répertorier des fichiers."
[salesforcedoc]: ./create-api-salesforce.md "Se connecte à votre compte Salesforce et permet notamment de gérer des comptes, des prospects et des opportunités."
[servicebusdoc]: ./create-api-servicebus.md "Permet d’envoyer des messages à partir de files d’attente et de rubriques Service Bus, et de recevoir des messages de files d’attente et d’abonnements Service Bus."
[sharepointdoc]: ./create-api-sharepointonline.md "Connexion à SharePoint Online pour gérer des documents et des éléments de liste."
[slackdoc]: ./create-api-slack.md "Se connecte à Slack et publie des messages dans les canaux Slack."
[sftpdoc]: ./create-api-sftp.md "Se connecte à SFTP et permet notamment de télécharger, d’obtenir et de supprimer des fichiers."
[smtpdoc]: ./create-api-smtp.md "Se connecte à un serveur SMTP et permet d'envoyer du courrier électronique avec des pièces jointes."
[sqldoc]: ./create-api-sqlazure.md "Se connecte à une base de données SQL Azure. Vous pouvez créer, mettre à jour, obtenir et supprimer des entrées dans une table de base de données SQL."
[twiliodoc]: ./create-api-twilio.md "Se connecte à Twilio et permet notamment d’envoyer et d’obtenir des messages, d’obtenir des numéros disponibles et de gérer des numéros de téléphone entrants."
[twitterdoc]: ./create-api-twitter.md "Se connecte à Twitter et permet notamment de consulter des fils d’actualité et de publier des tweets."
[yammerdoc]: ./create-api-yammer.md "Se connecte à Yammer pour publier des messages et obtenir de nouveaux messages."

<!--Icon references-->
[blobicon]: ./media/apis-list/blobicon.png
[bingsearchicon]: ./media/apis-list/bingsearchicon.png
[boxicon]: ./media/apis-list/boxicon.png
[ftpicon]: ./media/apis-list/ftpicon.png
[crmonlineicon]: ./media/apis-list/dynamicscrmicon.png
[dropboxicon]: ./media/apis-list/dropboxicon.png
[excelicon]: ./media/apis-list/excelicon.png
[facebookicon]: ./media/apis-list/facebookicon.png
[googledriveicon]: ./media/apis-list/googledriveicon.png
[microsofttranslatoricon]: ./media/apis-list/translatoricon.png
[office365icon]: ./media/apis-list/office365icon.png
[onedriveicon]: ./media/apis-list/onedriveicon.png
[salesforceicon]: ./media/apis-list/salesforceicon.png
[servicebusicon]: ./media/apis-list/servicebusicon.png
[sftpicon]: ./media/apis-list/sftpicon.png
[sharepointicon]: ./media/apis-list/sharepointicon.png
[slackicon]: ./media/apis-list/slackicon.png
[smtpicon]: ./media/apis-list/smtpicon.png
[sqlicon]: ./media/apis-list/sqlicon.png
[twilioicon]: ./media/apis-list/twilioicon.png
[twittericon]: ./media/apis-list/twittericon.png
[yammericon]: ./media/apis-list/yammericon.png

<!---HONumber=AcomDC_0302_2016-->