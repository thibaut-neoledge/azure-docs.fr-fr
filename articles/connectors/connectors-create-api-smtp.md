<properties
pageTitle="SMTP | Microsoft Azure"
description="Créez des applications logiques avec Azure App Service. Connectez-vous à SMTP pour envoyer un e-mail."
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/15/2016"
ms.author="deonhe"/>

# Prise en main du connecteur SMTP

Connectez-vous à SMTP pour envoyer un e-mail.

Pour utiliser [n’importe quel connecteur](./apis-list.md), vous devez commencer par créer une application logique. Vous pouvez démarrer maintenant en [créant une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Se connecter à SMTP

Pour que votre application logique puisse accéder à un service, vous devez commencer par créer une *connexion* à celui-ci. Une [connexion](./connectors-overview.md) permet d’assurer la connectivité entre une application logique et un autre service. Par exemple, pour vous connecter à SMTP, vous devez préalablement disposer d’une *connexion* SMTP. Pour créer une connexion, vous devez fournir les informations d’identification que vous utilisez généralement pour accéder au service auquel vous souhaitez vous connecter. Ainsi, dans l’exemple SMTP, vous avez besoin des informations d’identification telles que votre nom de connexion, l’adresse du serveur SMTP et les informations de connexion utilisateur pour créer la connexion à SMTP. [Apprenez-en davantage sur les connexions]().

### Créer une connexion à SMTP

>[AZURE.INCLUDE [Procédure de création d’une connexion à SMTP](../../includes/connectors-create-api-smtp.md)]

## Utiliser un déclencheur SMTP

Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. [Apprenez-en davantage sur les déclencheurs](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Dans cet exemple, étant donné que SMTP ne comporte aucun déclencheur, nous utiliserons le déclencheur **Salesforce - Quand un objet est créé**. Ce déclencheur s’active lorsqu’un objet est créé dans Salesforce. Dans notre exemple, nous allons le configurer pour que chaque fois qu’un prospect est créé dans Salesforce, une action *Envoyer un message électronique* s’exécute par le biais du connecteur SMTP avec une notification signalant la création du prospect.

1. Entrez *salesforce* dans la zone de recherche du concepteur d’applications logiques, puis sélectionnez le déclencheur **Salesforce - Quand un objet est créé**.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  

2. Le contrôle **Quand un objet est créé** s’affiche.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  

3. Sélectionnez le champ **Type d’objet**, puis sélectionnez *Prospect* dans la liste d’objets. Dans le cadre de cette étape, vous indiquez que vous créez un déclencheur qui informera votre application logique de toute nouvelle création d’un prospect dans Salesforce.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  

4. Le déclencheur a été créé.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## Utiliser une action SMTP

Une action est une opération effectuée par le flux de travail défini dans une application logique. [Apprenez-en davantage sur les actions](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Une fois le déclencheur ajouté, procédez comme suit pour ajouter une action SMTP qui se produira chaque fois qu’un prospect sera créé dans Salesforce.

1. Sélectionnez **+ Nouvelle étape** pour ajouter l’action à exécuter lorsqu’un prospect est créé.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  

2. Sélectionnez **Ajouter une action**. Cette opération ouvre la zone de recherche vous permettant de rechercher l’action que vous souhaitez exécuter.  
 ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  

3. Entrez *smtp* pour rechercher les actions associées à SMTP.

4. Sélectionnez **SMTP - Envoyer un message électronique** comme action à exécuter lorsque le prospect est créé. Le bloc de contrôles de l’action s’affiche. Vous devrez établir votre connexion smtp dans le bloc du concepteur, si vous n’avez pas fait précédemment.  
 ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)  

5. Entrez les informations de messagerie de votre choix dans le bloc **SMTP - Envoyer un message électronique**.  
 ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  

6. Enregistrez votre travail afin d’activer votre workflow.

## Détails techniques

Voici les détails des déclencheurs, actions et réponses pris en charge par cette connexion :

## Déclencheurs SMTP

SMTP ne comporte aucun déclencheur.

## Actions SMTP

SMTP comporte l’action suivante :


|Action|Description|
|--- | ---|
|[Envoyer un message électronique](connectors-create-api-smtp.md#send-email)|Cette opération envoie un e-mail à un ou plusieurs destinataires.|

### Détails de l’action

Voici les détails de l’action de ce connecteur, ainsi que ses réponses :


### Envoyer un message électronique
Cette opération envoie un e-mail à un ou plusieurs destinataires.


|Nom de la propriété| Display Name|Description|
| ---|---|---|
|À|À|Spécifier les adresses e-mail séparées par des points-virgules, au format recipient1@domain.com;destinataire2@domaine.com|
|CC|cc|Spécifier les adresses e-mail séparées par des points-virgules, au format recipient1@domain.com;destinataire2@domaine.com|
|Objet|Objet|Objet de l’e-mail|
|Corps|Corps|Corps de l’e-mail|
|À partir|À partir|Adresse e-mail de l’expéditeur, telle que sender@domain.com|
|IsHtml|HTML|Envoyer l’e-mail au format HTML (true/false)|
|Cci|bcc|Spécifier les adresses e-mail séparées par des points-virgules, au format recipient1@domain.com;destinataire2@domaine.com|
|Importance|Importance|Importance de l’e-mail (Élevée, Normale ou Faible)|
|ContentData|Attachments Content Data (Données de contenu des pièces jointes)|Données de contenu (codées en base 64 pour les flux et en l’état pour les chaînes)|
|ContentType|Attachments Content Type (Type de contenu des pièces jointes)|Type de contenu|
|ContentTransferEncoding|Attachments Content Transfer Encoding (Codage du transfert de contenu des pièces jointes)|Codage du transfert de contenu (base 64 ou aucun)|
|FileName|Attachments File Name (Nom de fichier des pièces jointes)|Nom de fichier|
|ContentId|Attachments Content ID (ID du contenu des pièces jointes)|Identificateur du contenu|

Le caractère * indique qu’une propriété est obligatoire.


## Réponses HTTP

Les actions et déclencheurs ci-dessus peuvent renvoyer un ou plusieurs des codes d’état HTTP suivants :

|Nom|Description|
|---|---|
|200|OK|
|202|Acceptée|
|400|Demande incorrecte|
|401|Non autorisé|
|403|Interdit|
|404|Introuvable|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|default|L’opération a échoué.|

## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!----HONumber=AcomDC_0803_2016-->
