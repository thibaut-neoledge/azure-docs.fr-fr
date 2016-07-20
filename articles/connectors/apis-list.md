<properties
	pageTitle="Liste des connecteurs gérés par Microsoft pour une utilisation dans Microsoft Azure Logic Apps | Microsoft Azure"
	description="Obtenez la liste complète des connecteurs gérés par Microsoft que vous pouvez utiliser pour créer des applications logiques dans Azure App Service"
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
	ms.topic="get-started-article"
	ms.date="07/07/2016"
	ms.author="deonhe"/>

# Liste de connecteurs

>[AZURE.NOTE] Cette version de l’article s’applique à la version de schéma 2015-08-01-preview des applications logiques. Pour la version de schéma 2014-12-01-preview, cliquez sur [Liste des connecteurs](../app-service-logic/app-service-logic-connectors-list.md).

Pour plus d’informations sur la tarification et une liste de ce qui est inclus dans chaque niveau de service, consultez la page [Tarification Azure App Service](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Si vous souhaitez commencer à utiliser Azure Logic Apps avant d’ouvrir un compte Azure, accédez à [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic). Vous pouvez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Connecteurs standard

Sélectionnez une icône afin d’apprendre rapidement à tirer parti de ces connecteurs pour créer des applications qui appellent ces services. Ces connecteurs peuvent servir à créer des applications logiques, des applications PowerApps et des flux.

|Connecteurs||||
|-----------|-----------|-----------|-----------|
|[![Icône API][blobicon]<br/>**Objets<br/>blob Azure**][azureblobdoc]|[![Icône API][boxicon]<br/>**Box**][boxDoc]|[![Icône API][crmonlineicon]<br/>**CRM Online**][crmonlinedoc]|[![Icône API][dropboxicon]<br/>**Dropbox**][dropboxdoc]|
|[![Icône API][facebookicon]<br/>**Facebook**][facebookdoc]|[![Icône API][ftpicon]<br/>**FTP**][ftpdoc]|[![Icône API][githubicon]<br/>**GitHub**][githubdoc]|[![Icône API][googledriveicon]<br/>**Google Drive**][googledrivedoc]|
|[![Icône API][mailchimpicon]<br/>**MailChimp**][mailchimpdoc]|[![Icône API][microsofttranslatoricon]<br/>**Traducteur**][microsofttranslatordoc]|[![Icône API][office365icon]<br/>**Office 365**<br/>**Outlook**][office365outlookdoc]|[![Icône API][office365icon]<br/>**Office 365**<br/>**Utilisateurs**][office365usersdoc]|
|[![Icône API][office365icon]<br/>**Office 365**<br/>**Vidéo**][office365videodoc]|[![Icône API][onedriveicon]<br/>**OneDrive**][onedrivedoc]|[![Icône API][onedriveicon]<br/>**OneDrive<br/>Entreprise**][onedriveforbusinessdoc]|[![Icône API][outlookicon]<br/>**Outlook**][outlookdoc]|
|[![Icône API][projectonlineicon]<br/>**Project<br/>Online**][projectonlinedoc]|[![Icône API][rssicon]<br/>**RSS**][rssdoc]|[![Icône API][salesforceicon]<br/>**Salesforce**][salesforcedoc]|[![Icône API][sendgridicon]<br/>**SendGrid**][sendgriddoc]|
|[![Icône API][servicebusicon]<br/>**Service Bus**][servicebusdoc]|[![Icône API][sftpicon]<br/>**SFTP**][sftpdoc]|[![Icône API][sharepointicon]<br/>**SharePoint**<br/>**Online**][sharepointdoc]|[![Icône API][slackicon]<br/>**Slack**<br/>][slackdoc]|
|[![Icône API][smtpicon]<br/>**SMTP**][smtpdoc]|[![Icône API][sqlicon]<br/>**SQL Azure**][sqldoc]|[![Icône API][trelloicon]<br/>**Trello**][trellodoc]|[![Icône API][twilioicon]<br/>**Twilio**][twiliodoc]|
|[![Icône API][twittericon]<br/>**Twitter**][twitterdoc]|[![Icône API][wunderlisticon]<br/>**Wunderlist**][wunderlistdoc]|[![Icône API][yammericon]<br/>**Yammer**][yammerdoc] | |

## Connecteurs Enterprise Integration Pack (EIP)
Les connecteurs EIP permettent de créer des applications logiques pour les scénarios B2B incluant EAI et EDI.
 
|Connecteurs EIP ||||
|-----------|-----------|-----------|-----------|
|[![Icône API][as2icon]<br/>**AS2</br>encodage/décodage**][as2doc]|[![Icône API][x12icon]<br/>**X12</br>encodage/décodage**][x12Doc]|[![Icône API][xmlvalidateicon]<br/>**XML <br/>validation**][xmlvalidatedoc]|[![Icône API][xmltransformicon]<br/>**XML<br/> transformation**][xmltransformdoc]|
|[![Icône API][flatfileicon]<br/>**Flat file</br>encodage**][flatfiledoc]|[![Icône API][flatfiledecodeicon]<br/>**Fichier plat</br>décodage**][flatfiledecodedoc]|||


### Les connecteurs peuvent être des déclencheurs
Plusieurs connecteurs fournissent des déclencheurs qui préviennent votre application lorsque des événements spécifiques se produisent. Par exemple, le connecteur FTP comporte le déclencheur OnUpdatedFile. Vous pouvez créer une application logique, une application PowerApps ou un flux qui écoute ce déclencheur et effectue une action chaque fois que ce dernier est activé.

Il existe deux types de déclencheurs :

* Déclencheurs d'interrogation : ces déclencheurs interrogent votre service selon une fréquence spécifiée pour vérifier l'existence de nouvelles données. Quand de nouvelles données sont disponibles, une nouvelle instance de votre application s'exécute avec les données comme entrée. Pour empêcher que les mêmes données soient consommées plusieurs fois, le déclencheur peut nettoyer les données qui ont été lues et transmises à votre application.
* Déclencheurs d'interrogation : ces déclencheurs écoutent les données sur un point de terminaison ou attendent qu'un événement se produise. Ensuite, ils déclenchent une nouvelle instance de votre application. Le connecteur Twitter en est un exemple.


### Les connecteurs peuvent être des actions
Vous pouvez aussi utiliser des connecteurs comme des actions dans vos applications. Les actions sont utiles pour rechercher des données qui peuvent ensuite être utilisées lors de l'exécution de votre application. Par exemple, vous devrez peut-être rechercher des données client à partir d'une base de données SQL lors du traitement d'une commande. Ou bien, vous devrez peut-être écrire, mettre à jour ou supprimer des données d'une table de destination. Pour cela, vous pouvez utiliser les actions fournies par les connecteurs. Les actions correspondent aux opérations définies dans les métadonnées Swagger.


[Nouveautés](../app-service-logic/app-service-logic-schema-2015-08-01.md)  
[Générer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)  
[Prise en main de PowerApps](../power-apps/powerapps-get-started-azure-portal.md)  
[Migrer les applications logiques existantes vers la dernière version du schéma](connectors-schema-migration.md) 

<!--Connectors Documentation-->
[azureblobdoc]: ./connectors-create-api-azureblobstorage.md "Connectez-vous à un objet blob Azure pour gérer les fichiers de votre conteneur d'objets blob."
[bingsearchDoc]: ./connectors-create-api-bingsearch.md "Effectuez des recherches web, d’images, d’actualités et de vidéos dans Bing."
[boxDoc]: ./connectors-create-api-box.md "Se connecte à Box et permet notamment de télécharger, d’obtenir, de supprimer et de répertorier des fichiers."
[crmonlinedoc]: ./connectors-create-api-crmonline.md "Connectez-vous à Dynamics CRM Online et faites en plus avec vos données CRM Online."
[dropboxdoc]: ./connectors-create-api-dropbox.md "Se connecte à Box et permet notamment d’obtenir, de supprimer et de répertorier des fichiers."
[exceldoc]: ./connectors-create-api-excel.md "Connectez-vous à Excel."
[facebookdoc]: ./connectors-create-api-facebook.md "Connectez-vous à Facebook pour publier dans un journal, obtenir un flux de page et bien plus encore."
[ftpdoc]: ./connectors-create-api-ftp.md "Se connecte à un serveur FTP/FTPS et permet notamment de télécharger; d’obtenir et de supprimer des fichiers."
[googledrivedoc]: ./connectors-create-api-googledrive.md "Connectez-vous à Google Drive et interagissez avec vos données."
[microsofttranslatordoc]: ./connectors-create-api-microsofttranslator.md
[office365outlookdoc]: ./connectors-create-api-office365-outlook.md "Le connecteur Office 365 permet d'envoyer et recevoir des courriers électroniques, de gérer votre calendrier et de gérer vos contacts à l'aide de votre compte Office 365."
[officeunifieddoc]: ./connectors-create-api-bingsearch.md
[office365usersdoc]: ./connectors-create-api-office365-users.md
[office365videodoc]: ./connectors-create-api-office365-video.md
[onedrivedoc]: ./connectors-create-api-onedrive.md "Se connecte à votre Microsoft OneDrive personnel et permet notamment de télécharger, de supprimer et de répertorier des fichiers."
[onedriveforbusinessdoc]: ./connectors-create-api-onedriveforbusiness.md "Se connecte au Microsoft OneDrive de votre entreprise et télécharge, supprime, répertorie vos fichiers et bien plus encore."
[outlookdoc]: ./connectors-create-api-outlook.md "Connectez-vous à votre boîte aux lettres Outlook, consultez vos messages et bien plus encore."
[projectonlinedoc]: ./connectors-create-api-projectonline.md "Se connecte à Microsoft Project Online."
[rssdoc]: ./connectors-create-api-rss.md "Le connecteur RSS permet aux utilisateurs de publier et d’extraire des éléments de flux. Il permet également aux utilisateurs de déclencher des opérations lorsqu’un nouvel élément est publié dans le flux."
[salesforcedoc]: ./connectors-create-api-salesforce.md "Se connecte à votre compte Salesforce et permet notamment de gérer des comptes, des prospects et des opportunités."
[sendgriddoc]: ./connectors-create-api-sendgrid.md "Se connecte à Microsoft Project Online."
[servicebusdoc]: ./connectors-create-api-servicebus.md "Permet d’envoyer des messages à partir de files d’attente et de rubriques Service Bus, et de recevoir des messages de files d’attente et d’abonnements Service Bus."
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "Connexion à SharePoint Online pour gérer des documents et des éléments de liste."
[slackdoc]: ./connectors-create-api-slack.md "Se connecte à Slack et publie des messages dans les canaux Slack."
[sftpdoc]: ./connectors-create-api-sftp.md "Se connecte à SFTP et permet notamment de télécharger, d’obtenir et de supprimer des fichiers."
[githubdoc]: ./connectors-create-api-github.md "Se connecte à GitHub et peut suivre les problèmes."
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "Envoyez de meilleurs e-mails."
[smtpdoc]: ./connectors-create-api-smtp.md "Se connecte à un serveur SMTP et permet d'envoyer du courrier électronique avec des pièces jointes."
[sqldoc]: ./connectors-create-api-sqlazure.md "Se connecte à une base de données SQL Azure. Vous pouvez créer, mettre à jour, obtenir et supprimer des entrées dans une table de base de données SQL."
[trellodoc]: ./connectors-create-api-trello.md "Trello est un outil gratuit, souple et visuel, qui vous permet d’organiser ce que vous souhaitez avec qui vous souhaitez."
[twiliodoc]: ./connectors-create-api-twilio.md "Se connecte à Twilio et permet notamment d’envoyer et d’obtenir des messages, d’obtenir des numéros disponibles et de gérer des numéros de téléphone entrants."
[twitterdoc]: ./connectors-create-api-twitter.md "Se connecte à Twitter et permet notamment de consulter des fils d’actualité et de publier des tweets."
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "Tenez votre vie à jour."
[yammerdoc]: ./connectors-create-api-yammer.md "Se connecte à Yammer pour publier des messages et obtenir de nouveaux messages."
[as2doc]: ../app-service-logic/app-service-logic-enterprise-integration-as2.md "Découvrez l’intégration d’entreprise AS2."
[x12doc]: ../app-service-logic/app-service-logic-enterprise-integration-x12.md "Découvrez l’intégration d’entreprise X12."
[flatfiledoc]: ../app-service-logic/app-service-logic-enterprise-integration-flatfile.md "Découvrez le fichier plat d’intégration d’entreprise."
[flatfiledecodedoc]: ../app-service-logic/app-service-logic-enterprise-integration-flatfile.md/#how-to-create-the-flat-file-decoding-connector "Découvrez le fichier plat d’intégration d’entreprise."
[xmlvalidatedoc]: ../app-service-logic/app-service-logic-enterprise-integration-xml.md "Découvrez la validation XML d’intégration d’entreprise."
[xmltransformdoc]: ../app-service-logic/app-service-logic-enterprise-integration-transform.md "Découvrez les transformations d’intégration d’entreprise."

<!--Icon references-->
[blobicon]: ./media/apis-list/blobicon.png
[bingsearchicon]: ./media/apis-list/bingsearchicon.png
[boxicon]: ./media/apis-list/boxicon.png
[ftpicon]: ./media/apis-list/ftpicon.png
[githubicon]: ./media/apis-list/githubicon.png
[crmonlineicon]: ./media/apis-list/dynamicscrmicon.png
[dropboxicon]: ./media/apis-list/dropboxicon.png
[excelicon]: ./media/apis-list/excelicon.png
[facebookicon]: ./media/apis-list/facebookicon.png
[googledriveicon]: ./media/apis-list/googledriveicon.png
[mailchimpicon]: ./media/apis-list/mailchimpicon.png
[microsofttranslatoricon]: ./media/apis-list/translatoricon.png
[office365icon]: ./media/apis-list/office365icon.png
[onedriveicon]: ./media/apis-list/onedriveicon.png
[onedriveforbusinessicon]: ./media/apis-list/onedriveforbusinessicon.png
[outlookicon]: ./media/apis-list/outlookicon.png
[projectonlineicon]: ./media/apis-list/projectonlineicon.png
[rssicon]: ./media/apis-list/rssicon.png
[salesforceicon]: ./media/apis-list/salesforceicon.png
[sendgridicon]: ./media/apis-list/sendgridicon.png
[servicebusicon]: ./media/apis-list/servicebusicon.png
[sftpicon]: ./media/apis-list/sftpicon.png
[sharepointicon]: ./media/apis-list/sharepointicon.png
[slackicon]: ./media/apis-list/slackicon.png
[smtpicon]: ./media/apis-list/smtpicon.png
[sqlicon]: ./media/apis-list/sqlicon.png
[trelloicon]: ./media/apis-list/trelloicon.png
[twilioicon]: ./media/apis-list/twilioicon.png
[twittericon]: ./media/apis-list/twittericon.png
[wunderlisticon]: ./media/apis-list/wunderlisticon.png
[yammericon]: ./media/apis-list/yammericon.png
[as2icon]: ./media/apis-list/as2new.png
[x12icon]: ./media/apis-list/x12new.png
[flatfileicon]: ./media/apis-list/flatfileencoding.png
[flatfiledecodeicon]: ./media/apis-list/flatfiledecoding.png
[xmlvalidateicon]: ./media/apis-list/xmlvalidation.png
[xmltransformicon]: ./media/apis-list/xsltransform.png

<!---HONumber=AcomDC_0713_2016-->