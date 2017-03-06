---
title: "SMTP | Microsoft Docs"
description: "Créez des applications logiques avec Azure App Service. Connectez-vous à SMTP pour envoyer un e-mail."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: 3a0fdef111fbd4a9f7491e247f2236cf70b89dca
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-the-smtp-connector"></a>Prise en main du connecteur SMTP
Connectez-vous à SMTP pour envoyer un e-mail.

Pour utiliser [n’importe quel connecteur](apis-list.md), vous devez commencer par créer une application logique. Vous pouvez démarrer maintenant en [créant une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-smtp"></a>Se connecter à SMTP
Pour que votre application logique puisse accéder à un service, vous devez commencer par créer une *connexion* à celui-ci. Une [connexion](connectors-overview.md) permet d’assurer la connectivité entre une application logique et un autre service. Par exemple, pour vous connecter à SMTP, vous devez préalablement disposer d’une *connexion* SMTP. Pour créer une connexion, vous devez fournir les informations d’identification que vous utilisez généralement pour accéder au service auquel vous souhaitez vous connecter. Ainsi, dans l’exemple SMTP, vous avez besoin des informations d’identification telles que votre nom de connexion, l’adresse du serveur SMTP et les informations de connexion utilisateur pour créer la connexion à SMTP. [Apprenez-en davantage sur les connexions]().  

### <a name="create-a-connection-to-smtp"></a>Créer une connexion à SMTP
> [!INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]
> 
> 

## <a name="use-an-smtp-trigger"></a>Utiliser un déclencheur SMTP
Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. [En savoir plus sur les déclencheurs](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Dans cet exemple, étant donné que SMTP ne comporte aucun déclencheur, nous utiliserons le déclencheur **Salesforce - Quand un objet est créé**. Ce déclencheur s’active lorsqu’un objet est créé dans Salesforce. Dans notre exemple, nous allons le configurer pour que chaque fois qu’un prospect est créé dans Salesforce, une action *Envoyer un message électronique* s’exécute par le biais du connecteur SMTP avec une notification signalant la création du prospect.

1. Saisissez *salesforce* dans la zone de recherche sur le concepteur d’applications logiques, puis sélectionnez le déclencheur **Salesforce - Création d’un objet** .  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  
2. Le contrôle **Lors de la création d’un objet** s’affiche.
   ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  
3. Sélectionnez le **Type d’objet** puis sélectionnez *Prospect* à partir de la liste d’objets. Lors de cette étape, vous indiquez que vous créez un déclencheur qui informe votre application logique de la création d’un prospect dans Salesforce.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  
4. Le déclencheur a été créé.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>Utiliser une action SMTP
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Une fois le déclencheur ajouté, procédez comme suit pour ajouter une action SMTP qui se produira chaque fois qu’un prospect sera créé dans Salesforce.

1. Sélectionnez **+ Nouvelle étape** pour ajouter l’action à exécuter lorsqu’un prospect est créé.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  
2. Sélectionnez **Ajouter une action**. Ouvre la zone de recherche dans laquelle vous pouvez rechercher l’action que vous souhaitez effectuer.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  
3. Entrez *smtp* pour rechercher les actions associées à SMTP.  
4. Sélectionnez **SMTP - Envoyer un message électronique** comme action à exécuter lorsque le prospect est créé. Le bloc de contrôles de l’action s’affiche. Vous devez établir votre connexion SMTP dans le bloc du concepteur, si ce n’est pas déjà fait.  
   ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    
5. Entrez les informations de messagerie de votre choix dans le bloc **SMTP - Envoyer un message électronique**.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  
6. Enregistrez votre travail afin d’activer votre workflow.  

## <a name="technical-details"></a>Détails techniques
Voici les détails des déclencheurs, actions et réponses pris en charge par cette connexion :

## <a name="smtp-triggers"></a>Déclencheurs SMTP
SMTP ne comporte aucun déclencheur. 

## <a name="smtp-actions"></a>Actions SMTP
SMTP comporte l’action suivante :

| Action | Description |
| --- | --- |
| [Envoi d’e-mail](connectors-create-api-smtp.md#send-email) |Cette opération envoie un e-mail à un ou plusieurs destinataires. |

### <a name="action-details"></a>Détails de l’action
Voici les détails de l’action de ce connecteur, ainsi que ses réponses :

### <a name="send-email"></a>Envoyer un message électronique
Cette opération envoie un e-mail à un ou plusieurs destinataires. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| À |À |Spécifier des adresses e-mail séparées par des points-virgules, au format recipient1@domain.com;recipient2@domain.com |
| CC |cc |Spécifier des adresses e-mail séparées par des points-virgules, au format recipient1@domain.com;recipient2@domain.com |
| Objet |Objet |Objet de l’e-mail |
| Corps |body |Corps de l’e-mail |
| À partir |À partir |Adresse e-mail de l’expéditeur, telle que sender@domain.com |
| IsHtml |HTML |Envoyer l’e-mail au format HTML (true/false) |
| Cci |bcc |Spécifier des adresses e-mail séparées par des points-virgules, au format recipient1@domain.com;recipient2@domain.com |
| Importance |Importance |Importance de l’e-mail (Élevée, Normale ou Faible) |
| ContentData |Attachments Content Data (Données de contenu des pièces jointes) |Données de contenu (codées en base&64; pour les flux et en l’état pour les chaînes) |
| ContentType |Attachments Content Type (Type de contenu des pièces jointes) |Type de contenu |
| ContentTransferEncoding |Attachments Content Transfer Encoding (Codage du transfert de contenu des pièces jointes) |Codage du transfert de contenu (base&64; ou aucun) |
| FileName |Attachments File Name (Nom de fichier des pièces jointes) |Nom de fichier |
| ContentId |Attachments Content ID (ID du contenu des pièces jointes) |Identificateur du contenu |

Le caractère * indique qu’une propriété est obligatoire.

## <a name="http-responses"></a>Réponses HTTP
Les actions et déclencheurs ci-dessus peuvent renvoyer un ou plusieurs des codes d’état HTTP suivants : 

| Nom | Description |
| --- | --- |
| 200 |OK |
| 202 |Acceptée |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md)


