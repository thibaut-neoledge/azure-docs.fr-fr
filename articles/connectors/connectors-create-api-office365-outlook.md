---
title: "Ajouter le connecteur Office 365 Outlook dans vos applications logiques | Microsoft Docs"
description: "Créez des applications logiques avec un connecteur Office 365 pour permettre l’interaction avec Office 365. Par exemple : création, modification et mise à jour de contacts et d’éléments de calendrier."
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2f6cc2c-bba2-493a-b0ba-841785462a80
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 92c0892128655141f29380890c31451e62ca8853


---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Prise en main du connecteur Office 365 Outlook
Le connecteur Office 365 Outlook permet d’interagir avec Outlook dans Office 365. Utilisez ce connecteur pour créer, modifier et mettre à jour des contacts et des éléments de calendrier, ainsi que pour recevoir et envoyer des e-mails et pour y répondre.

Avec Office 365 Outlook, vous pouvez effectuer les opérations suivantes :

* Créer votre workflow à l’aide des fonctionnalités de messagerie et de calendrier dans Office 365. 
* Utiliser des déclencheurs pour démarrer votre workflow lorsqu’un nouvel e-mail apparaît, lorsqu’un élément de calendrier est mis à jour, et bien davantage.
* Utiliser des actions pour envoyer un e-mail, créer un événement de calendrier, etc. Par exemple, lorsqu’un nouvel objet existe dans Salesforce (déclencheur), un e-mail doit être envoyé à votre application Office 365 Outlook (action). 

Cette rubrique décrit comment utiliser le connecteur Office 365 Outlook dans une application logique, et répertorie les déclencheurs et les actions.

> [!NOTE]
> Cette version de l’article s’applique à la disponibilité générale des applications logiques.
> 
> 

Pour plus d’informations sur Logic Apps, voir [Qu’est-ce qu’une application logique ?](../logic-apps/logic-apps-what-are-logic-apps.md) et [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-office-365"></a>Connexion à Office 365
Pour que votre application logique puisse accéder à un service, vous devez d’abord créer une *connexion* à celui-ci. Une connexion permet d’assurer la connectivité entre une application logique et un autre service. Par exemple, pour vous connecter à Office 365 Outlook, vous devez préalablement disposer d’une *connexion* Office 365. Pour créer une connexion, entrez les informations d’identification que vous utilisez généralement pour accéder au service auquel vous souhaitez vous connecter. Ensuite, dans Office 365 Outlook, entrez les informations d’identification de votre compte Office 365 pour créer la connexion.

## <a name="create-the-connection"></a>Créer la connexion
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Utilisation d’un déclencheur
Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. Les déclencheurs « interrogent » le service à l’intervalle et à la fréquence de votre choix. [En savoir plus sur les déclencheurs](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Dans l’application logique, tapez « office 365 » pour obtenir la liste des déclencheurs :  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Sélectionnez **Office 365 Outlook - When an upcoming event is starting soon** (Office 365 Outlook - Quand un événement à venir est imminent). Si une connexion existe déjà, sélectionnez un calendrier dans la liste déroulante.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Si vous êtes invité à vous connecter, entrez les informations de connexion pour créer la connexion. La section [Créer la connexion](connectors-create-api-office365-outlook.md#create-the-connection) dans cette rubrique répertorie les étapes. 
   
   > [!NOTE]
   > Dans cet exemple, l’application logique s’exécute lorsqu’un événement de calendrier est mis à jour. Pour visualiser les résultats de ce déclencheur, ajoutez une autre action qui vous envoie un SMS. Par exemple, ajoutez l’action Twilio *Send message* (Envoyer un message) qui vous envoie un SMS lorsque l’événement de calendrier doit démarrer dans 15 minutes. 
   > 
   > 
3. Sélectionnez le bouton **Modifier**, puis renseignez les valeurs **Fréquence** et **Intervalle**. Par exemple, si vous souhaitez que le déclencheur interroge le service toutes les 15 minutes, définissez le champ **Fréquence** sur **Minute**, et le champ **Intervalle** sur **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Enregistrez** vos modifications (dans le coin supérieur gauche de la barre d’outils). Votre application logique est enregistrée et peut être activée automatiquement.

## <a name="use-an-action"></a>Utilisation d’une action
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Sélectionnez le signe plus. Vous disposez de plusieurs options : **Ajouter une action**, **Ajouter une condition** ou l’une des options **Plus**.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Choisissez **Ajouter une action**.
3. Dans la zone de texte, tapez « office 365 » pour obtenir la liste de toutes les actions disponibles.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. Dans notre exemple, choisissez **Office 365 Outlook - Créer un contact**. Si une connexion existe déjà, choisissez **ID du dossier**, **Prénom** et d’autres propriétés :  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Si vous êtes invité à saisir les informations de connexion, entrez les informations requises pour créer la connexion. La section [Créer la connexion](connectors-create-api-office365-outlook.md#create-the-connection) dans cette rubrique décrit ces propriétés. 
   
   > [!NOTE]
   > Dans cet exemple, nous créons un contact dans Office 365 Outlook. Vous pouvez utiliser la sortie d’un autre déclencheur pour créer le contact. Par exemple, ajoutez le déclencheur SalesForce *Quand un objet est créé*. Ensuite, ajoutez l’action Office 365 Outlook *Créer un contact* qui utilise les champs SalesForce pour créer le contact dans Office 365. 
   > 
   > 
5. **Enregistrez** vos modifications (dans le coin supérieur gauche de la barre d’outils). Votre application logique est enregistrée et peut être activée automatiquement.

## <a name="technical-details"></a>Détails techniques
Voici les détails des déclencheurs, actions et réponses pris en charge par cette connexion :

## <a name="office-365-triggers"></a>Déclencheurs Office 365
| Déclencheur | Description |
| --- | --- |
| [When an upcoming event is starting soon](connectors-create-api-office365-outlook.md#when-an-upcoming-event-is-starting-soon) (Quand un événement à venir est imminent) |Cette opération déclenche un flux au démarrage d’un événement de calendrier à venir. |
| [When a new email arrives](connectors-create-api-office365-outlook.md#when-a-new-email-arrives) (Quand un nouveau courrier électronique arrive) |Cette opération déclenche un flux à l’arrivée d’un nouvel e-mail. |
| [When a new event is created](connectors-create-api-office365-outlook.md#when-a-new-event-is-created) (Quand un événement est créé) |Cette opération déclenche un flux lorsqu’un événement est créé dans un calendrier. |
| [When an event is modified](connectors-create-api-office365-outlook.md#when-an-event-is-modified) (Quand un événement est modifié) |Cette opération déclenche un flux lorsqu’un événement est modifié dans un calendrier. |

## <a name="office-365-actions"></a>Actions Office 365
| Action | Description |
| --- | --- |
| [Obtenir les messages électroniques](connectors-create-api-office365-outlook.md#get-emails) |Cette opération récupère les e-mails à partir d’un dossier. |
| [Envoyer un message électronique](connectors-create-api-office365-outlook.md#send-an-email) |Cette opération envoie un e-mail. |
| [Supprimer un message électronique](connectors-create-api-office365-outlook.md#delete-email) |Cette opération supprime un e-mail par son identificateur. |
| [Marquer comme lu](connectors-create-api-office365-outlook.md#mark-as-read) |Cette opération marque un e-mail comme étant lu. |
| [Répondre à un message électronique](connectors-create-api-office365-outlook.md#reply-to-email) |Cette opération répond à un e-mail. |
| [Obtenir une pièce jointe](connectors-create-api-office365-outlook.md#get-attachment) |Cette opération récupère une pièce jointe à un e-mail par son identificateur. |
| [Envoyer un message électronique avec des options](connectors-create-api-office365-outlook.md#send-email-with-options) |Cette opération envoie un e-mail avec plusieurs options et attend que le destinataire réponde avec l’une des options. |
| [Envoyer un message électronique d’approbation](connectors-create-api-office365-outlook.md#send-approval-email) |Cette opération envoie un e-mail d’approbation et attend une réponse du destinataire. |
| [Obtenir des calendriers](connectors-create-api-office365-outlook.md#get-calendars) |Cette opération répertorie les calendriers disponibles. |
| [Obtenir les événements](connectors-create-api-office365-outlook.md#get-events) |Cette opération récupère les événements d’un calendrier. |
| [Créer un événement](connectors-create-api-office365-outlook.md#create-event) |Cette opération crée un événement dans un calendrier. |
| [Obtenir un événement](connectors-create-api-office365-outlook.md#get-event) |Cette opération récupère un événement spécifique d’un calendrier. |
| [Supprimer un événement](connectors-create-api-office365-outlook.md#delete-event) |Cette opération supprime un événement d’un calendrier. |
| [Mettre à jour un événement](connectors-create-api-office365-outlook.md#update-event) |Cette opération met à jour un événement dans un calendrier. |
| [Obtenir des dossiers de contacts](connectors-create-api-office365-outlook.md#get-contact-folders) |Cette opération répertorie les dossiers des contacts disponibles. |
| [Obtenir des contacts](connectors-create-api-office365-outlook.md#get-contacts) |Cette opération récupère les contacts d’un dossier des contacts. |
| [Créer un contact](connectors-create-api-office365-outlook.md#create-contact) |Cette opération crée un contact dans un dossier des contacts. |
| [Obtenir un contact](connectors-create-api-office365-outlook.md#get-contact) |Cette opération récupère un contact spécifique d’un dossier des contacts. |
| [Supprimer un contact](connectors-create-api-office365-outlook.md#delete-contact) |Cette opération supprime un contact d’un dossier des contacts. |
| [Mettre à jour un contact](connectors-create-api-office365-outlook.md#update-contact) |Cette opération met à jour un contact dans un dossier des contacts. |

### <a name="trigger-and-action-details"></a>Détail des déclencheurs et des actions
Dans cette section, consultez les détails relatifs à chacun des déclencheurs et actions, y compris toutes les propriétés d’entrée requises ou facultatives et toute sortie correspondante associée au connecteur.

#### <a name="when-an-upcoming-event-is-starting-soon"></a>When an upcoming event is starting soon (Lorsqu’un événement à venir est imminent)
Cette opération déclenche un flux au démarrage d’un événement de calendrier à venir. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |Calendar id (ID de calendrier) |Identificateur unique du calendrier |
| lookAheadTimeInMinutes |Look ahead time (Temps d’anticipation) |Plage (en minutes) dans laquelle rechercher les événements à venir |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
CalendarItemsList : liste des éléments de calendrier

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| value |array |Liste des éléments de calendrier |

#### <a name="get-emails"></a>Obtenir des messages électroniques
Cette opération récupère les e-mails à partir d’un dossier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| folderPath |Chemin d'accès du dossier |Chemin du dossier duquel récupérer des messages électroniques (valeur par défaut : « Inbox ») |
| top |Top (Premiers) |Nombre de messages électroniques à récupérer (valeur par défaut : 10) |
| fetchOnlyUnread |Récupérer uniquement les messages non lus |Récupérer uniquement les messages électroniques non lus ? |
| includeAttachments |Inclure les pièces jointes |Si défini sur true, les pièces jointes sont également récupérées avec les messages électroniques |
| searchQuery |Requête de recherche |Requête de recherche pour filtrer les messages électroniques |
| skip |Skip |Nombre de messages électroniques à ignorer (valeur par défaut : 0) |
| skipToken |Skip Token (Jeton d’évitement) |Jeton d’évitement pour récupérer une nouvelle page |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
ReceiveMessage : recevoir un message électronique

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| À partir |string |À partir |
| À |string |À |
| Objet |string |Objet |
| body |string |body |
| importance |string |importance |
| HasAttachment |booléenne |Comporte une pièce jointe |
| ID |string |ID du message |
| IsRead |booléenne |Est lu |
| DateTimeReceived |string |Date et heure de réception |
| Pièces jointes |array |Pièces jointes |
| Cc |string |Spécifier des adresses e-mail séparées par des points-virgules, au format someone@contoso.com |
| Cci |string |Spécifier des adresses e-mail séparées par des points-virgules, au format someone@contoso.com |
| IsHtml |booléenne |HTML |

#### <a name="send-an-email"></a>Envoi d'un courrier électronique
Cette opération envoie un e-mail. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| emailMessage* |Email |Email |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
Aucune.

#### <a name="delete-email"></a>Supprimer un message électronique
Cette opération supprime un e-mail par son identificateur. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| messageId* |ID du message |ID du message électronique à supprimer |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
Aucune.

#### <a name="mark-as-read"></a>Marquer comme lu
Cette opération marque un e-mail comme étant lu. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| messageId* |ID du message |Identifiant du message électronique à marquer comme lu |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
Aucune.

#### <a name="reply-to-email"></a>Répondre à un message électronique
Cette opération répond à un e-mail. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| messageId* |ID du message |ID du message électronique auquel répondre |
| comment* |Commentaire |Commentaire de réponse |
| replyAll |Répondre à tous |Répondre à tous les destinataires |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
Aucune.

#### <a name="get-attachment"></a>Obtenir une pièce jointe
Cette opération récupère une pièce jointe à un e-mail par son identificateur. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| messageId* |ID du message |ID du message électronique |
| attachmentId* |Attachment Id (ID de pièce jointe) |Identifiant de la pièce jointe à télécharger |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
Aucune.

#### <a name="when-a-new-email-arrives"></a>Lorsqu’un nouveau courrier électronique arrive
Cette opération déclenche un flux à l’arrivée d’un nouvel e-mail.

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| folderPath |Chemin d'accès du dossier |Dossier de courrier électronique à récupérer (valeur par défaut : Inbox) |
| to |À |Adresses de messagerie des destinataires |
| À partir |À partir |Adresse de l’expéditeur |
| importance |importance |Importance du message (High, Normal, Low) (valeur par défaut : Normal) |
| fetchOnlyWithAttachment |Contient des pièces jointes |Récupérer uniquement les messages électroniques avec une pièce jointe |
| includeAttachments |Inclure les pièces jointes |Inclure les pièces jointes |
| subjectFilter |Subject Filter (Filtre d’objet) |Chaîne à rechercher dans l’objet |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
TriggerBatchResponse[ReceiveMessage]

| Nom de la propriété | Type de données |
| --- | --- |
| value |array |

#### <a name="send-email-with-options"></a>Envoyer un message électronique avec des options
Cette opération envoie un e-mail avec plusieurs options et attend que le destinataire réponde avec l’une des options. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| optionsEmailSubscription* |Demande d’abonnement pour courrier électronique avec options |Demande d’abonnement pour courrier électronique avec options |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
SubscriptionResponse : modèle d’abonnement au courrier électronique d’approbation

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| id |string |ID de l’abonnement |
| resource |string |Ressources de la demande d’abonnement |
| notificationType |string |Type de notification |
| notificationUrl |string |URL de notification |

#### <a name="send-approval-email"></a>Envoyer un message électronique d’approbation
Cette opération envoie un e-mail d’approbation et attend une réponse du destinataire. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| approvalEmailSubscription* |Demande d’abonnement pour courrier électronique d’approbation |Demande d’abonnement pour courrier électronique d’approbation |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
SubscriptionResponse : modèle d’abonnement au courrier électronique d’approbation

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| id |string |ID de l’abonnement |
| resource |string |Ressources de la demande d’abonnement |
| notificationType |string |Type de notification |
| notificationUrl |string |URL de notification |

#### <a name="get-calendars"></a>Obtenir des calendriers
Cette opération répertorie les calendriers disponibles. 

Il n'existe aucun paramètre pour cet appel.

##### <a name="output-details"></a>Détails des résultats
TablesList

| Nom de la propriété | Type de données |
| --- | --- |
| value |array |

#### <a name="get-events"></a>Obtenir les événements
Cette opération récupère les événements d’un calendrier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |Calendar id (ID de calendrier) |Sélectionner un calendrier |
| $filter |Requête de filtre |Requête de filtre ODATA pour limiter le nombre d’entrées renvoyées |
| $orderby |Trier par |Requête orderBy ODATA pour spécifier l’ordre des entrées |
| $skip |Nombre à ignorer |Nombre d’entrées à ignorer (valeur par défaut : 0) |
| $top |Nombre maximal à récupérer |Nombre maximal d’entrées à récupérer (valeur par défaut : 256) |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
CalendarEventList : liste des éléments de calendrier

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| value |array |Liste des éléments de calendrier |

#### <a name="create-event"></a>Créer un événement
Cette opération crée un événement dans un calendrier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |Calendar id (ID de calendrier) |Sélectionner un calendrier |
| item* |Élément |Événement à créer |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
CalendarEvent : classe de modèle d’événement de calendrier spécifique du connecteur.

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| ID |string |Identificateur unique de l’événement. |
| Attendees |array |Liste des participants à l’événement. |
| body |non défini |Corps du message associé à l’événement. |
| BodyPreview |string |Aperçu du message associé à l’événement. |
| Catégories |array |Catégories associées à l’événement. |
| ChangeKey |string |Identifie la version de l’objet d’événement. Chaque fois que l’événement est modifié, ChangeKey change également. |
| DateTimeCreated |string |Date et heure de création de l’événement. |
| DateTimeLastModified |string |Date et heure de la dernière modification de l’événement. |
| Terminer |string |Heure de fin de l’événement. |
| EndTimeZone |string |Spécifie le fuseau horaire de l’heure de fin de la réunion. Cette valeur doit être telle que définie dans Windows (par exemple : « Pacifique »). |
| HasAttachments |booléenne |Définie sur true si l’événement comporte des pièces jointes. |
| Importance |string |Importance de l’événement : Faible, Normale ou Élevée. |
| IsAllDay |booléenne |Définie sur true si l’événement dure toute la journée. |
| IsCancelled |booléenne |Définie sur true si l’événement a été annulé. |
| IsOrganizer |booléenne |Définie sur true si l’expéditeur du message est également l’organisateur. |
| Emplacement |non défini |Lieu de l’événement. |
| Organizer |non défini |Organisateur de l’événement. |
| Périodicité |non défini |Périodicité de l’événement. |
| Rappel |integer |Nombre de minutes avant l’événement à partir duquel afficher un rappel. |
| ResponseRequested |booléenne |Définie sur true si l’expéditeur souhaite être averti de l’acceptation ou du refus de l’événement. |
| ResponseStatus |non défini |Indique le type de la réponse envoyée à un message d’événement. |
| SeriesMasterId |string |Identificateur unique du type d’événement Series Master (Principal de la série). |
| ShowAs |string |S’affiche comme étant disponible ou occupé. |
| Démarrer |string |Heure de début de l’événement. |
| StartTimeZone |string |Spécifie le fuseau horaire de l’heure de début de la réunion. Cette valeur doit être telle que définie dans Windows (par exemple : « Pacifique »). |
| Objet |string |Objet de l’événement. |
| Type |string |Type de l’événement : Instance unique, Occurrence, Exception ou Series Master (Principal de la série). |
| WebLink |string |Aperçu du message associé à l’événement. |

#### <a name="get-event"></a>Obtenir un événement
Cette opération récupère un événement spécifique d’un calendrier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |Calendar id (ID de calendrier) |Sélectionner un calendrier |
| id* |ID d’élément |Sélectionner un événement |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
CalendarEvent : classe de modèle d’événement de calendrier spécifique du connecteur.

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| ID |string |Identificateur unique de l’événement. |
| Attendees |array |Liste des participants à l’événement. |
| body |non défini |Corps du message associé à l’événement. |
| BodyPreview |string |Aperçu du message associé à l’événement. |
| Catégories |array |Catégories associées à l’événement. |
| ChangeKey |string |Identifie la version de l’objet d’événement. Chaque fois que l’événement est modifié, ChangeKey change également. |
| DateTimeCreated |string |Date et heure de création de l’événement. |
| DateTimeLastModified |string |Date et heure de la dernière modification de l’événement. |
| Terminer |string |Heure de fin de l’événement. |
| EndTimeZone |string |Spécifie le fuseau horaire de l’heure de fin de la réunion. Cette valeur doit être telle que définie dans Windows (par exemple : « Pacifique »). |
| HasAttachments |booléenne |Définie sur true si l’événement comporte des pièces jointes. |
| Importance |string |Importance de l’événement : Faible, Normale ou Élevée. |
| IsAllDay |booléenne |Définie sur true si l’événement dure toute la journée. |
| IsCancelled |booléenne |Définie sur true si l’événement a été annulé. |
| IsOrganizer |booléenne |Définie sur true si l’expéditeur du message est également l’organisateur. |
| Emplacement |non défini |Lieu de l’événement. |
| Organizer |non défini |Organisateur de l’événement. |
| Périodicité |non défini |Périodicité de l’événement. |
| Rappel |integer |Nombre de minutes avant l’événement à partir duquel afficher un rappel. |
| ResponseRequested |booléenne |Définie sur true si l’expéditeur souhaite être averti de l’acceptation ou du refus de l’événement. |
| ResponseStatus |non défini |Indique le type de la réponse envoyée à un message d’événement. |
| SeriesMasterId |string |Identificateur unique du type d’événement Series Master (Principal de la série). |
| ShowAs |string |S’affiche comme étant disponible ou occupé. |
| Démarrer |string |Heure de début de l’événement. |
| StartTimeZone |string |Spécifie le fuseau horaire de l’heure de début de la réunion. Cette valeur doit être telle que définie dans Windows (par exemple : « Pacifique »). |
| Objet |string |Objet de l’événement. |
| Type |string |Type de l’événement : Instance unique, Occurrence, Exception ou Series Master (Principal de la série). |
| WebLink |string |Aperçu du message associé à l’événement. |

#### <a name="delete-event"></a>Supprimer un événement
Cette opération supprime un événement d’un calendrier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |Calendar id (ID de calendrier) |Sélectionner un calendrier |
| id* |ID |Sélectionner un événement |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
Aucune.

#### <a name="update-event"></a>Mettre à jour un événement
Cette opération met à jour un événement dans un calendrier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |Calendar id (ID de calendrier) |Sélectionner un calendrier |
| id* |ID |Sélectionner un événement |
| item* |Élément |Événement à mettre à jour |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
CalendarEvent : classe de modèle d’événement de calendrier spécifique du connecteur.

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| ID |string |Identificateur unique de l’événement. |
| Attendees |array |Liste des participants à l’événement. |
| body |non défini |Corps du message associé à l’événement. |
| BodyPreview |string |Aperçu du message associé à l’événement. |
| Catégories |array |Catégories associées à l’événement. |
| ChangeKey |string |Identifie la version de l’objet d’événement. Chaque fois que l’événement est modifié, ChangeKey change également. |
| DateTimeCreated |string |Date et heure de création de l’événement. |
| DateTimeLastModified |string |Date et heure de la dernière modification de l’événement. |
| Terminer |string |Heure de fin de l’événement. |
| EndTimeZone |string |Spécifie le fuseau horaire de l’heure de fin de la réunion. Cette valeur doit être telle que définie dans Windows (par exemple : « Pacifique »). |
| HasAttachments |booléenne |Définie sur true si l’événement comporte des pièces jointes. |
| Importance |string |Importance de l’événement : Faible, Normale ou Élevée. |
| IsAllDay |booléenne |Définie sur true si l’événement dure toute la journée. |
| IsCancelled |booléenne |Définie sur true si l’événement a été annulé. |
| IsOrganizer |booléenne |Définie sur true si l’expéditeur du message est également l’organisateur. |
| Emplacement |non défini |Lieu de l’événement. |
| Organizer |non défini |Organisateur de l’événement. |
| Périodicité |non défini |Périodicité de l’événement. |
| Rappel |integer |Nombre de minutes avant l’événement à partir duquel afficher un rappel. |
| ResponseRequested |booléenne |Définie sur true si l’expéditeur souhaite être averti de l’acceptation ou du refus de l’événement. |
| ResponseStatus |non défini |Indique le type de la réponse envoyée à un message d’événement. |
| SeriesMasterId |string |Identificateur unique du type d’événement Series Master (Principal de la série). |
| ShowAs |string |S’affiche comme étant disponible ou occupé. |
| Démarrer |string |Heure de début de l’événement. |
| StartTimeZone |string |Spécifie le fuseau horaire de l’heure de début de la réunion. Cette valeur doit être telle que définie dans Windows (par exemple : « Pacifique »). |
| Objet |string |Objet de l’événement. |
| Type |string |Type de l’événement : Instance unique, Occurrence, Exception ou Series Master (Principal de la série). |
| WebLink |string |Aperçu du message associé à l’événement. |

#### <a name="when-a-new-event-is-created"></a>When a new event is created (Lorsqu’un événement est créé)
Cette opération déclenche un flux lorsqu’un événement est créé dans un calendrier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |Calendar id (ID de calendrier) |Sélectionner un calendrier |
| $filter |Requête de filtre |Requête de filtre ODATA pour limiter le nombre d’entrées renvoyées |
| $orderby |Trier par |Requête orderBy ODATA pour spécifier l’ordre des entrées |
| $skip |Nombre à ignorer |Nombre d’entrées à ignorer (valeur par défaut : 0) |
| $top |Nombre maximal à récupérer |Nombre maximal d’entrées à récupérer (valeur par défaut : 256) |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
CalendarItemsList : liste des éléments de calendrier

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| value |array |Liste des éléments de calendrier |

#### <a name="when-an-event-is-modified"></a>When an event is modified (Lorsqu’un événement est modifié)
Cette opération déclenche un flux lorsqu’un événement est modifié dans un calendrier. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |Calendar id (ID de calendrier) |Sélectionner un calendrier |
| $filter |Requête de filtre |Requête de filtre ODATA pour limiter le nombre d’entrées renvoyées |
| $orderby |Trier par |Requête orderBy ODATA pour spécifier l’ordre des entrées |
| $skip |Nombre à ignorer |Nombre d’entrées à ignorer (valeur par défaut : 0) |
| $top |Nombre maximal à récupérer |Nombre maximal d’entrées à récupérer (valeur par défaut : 256) |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
CalendarItemsList : liste des éléments de calendrier

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| value |array |Liste des éléments de calendrier |

#### <a name="get-contact-folders"></a>Obtenir des dossiers de contacts
Cette opération répertorie les dossiers des contacts disponibles. 

Il n'existe aucun paramètre pour cet appel.

##### <a name="output-details"></a>Détails des résultats
TablesList

| Nom de la propriété | Type de données |
| --- | --- |
| value |array |

#### <a name="get-contacts"></a>Obtenir des contacts
Cette opération récupère les contacts d’un dossier des contacts. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |ID du dossier |Identificateur unique du dossier de contacts à récupérer |
| $filter |Requête de filtre |Requête de filtre ODATA pour limiter le nombre d’entrées renvoyées |
| $orderby |Trier par |Requête orderBy ODATA pour spécifier l’ordre des entrées |
| $skip |Nombre à ignorer |Nombre d’entrées à ignorer (valeur par défaut : 0) |
| $top |Nombre maximal à récupérer |Nombre maximal d’entrées à récupérer (valeur par défaut : 256) |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
ContactList : liste de contacts

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| value |array |Liste de contacts |

#### <a name="create-contact"></a>Créer un contact
Cette opération crée un contact dans un dossier des contacts. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |ID du dossier |Sélectionner un dossier des contacts |
| item* |Élément |Contact à créer |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
Contact : contact

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| ID |string |Identificateur unique du contact. |
| ParentFolderId |string |Identificateur du dossier parent du contact. |
| Birthday |string |Date d’anniversaire du contact. |
| FileAs |string |Nom sous lequel est archivé le contact. |
| displayName |string |Nom d’affichage du contact. |
| GivenName |string |Prénom du contact. |
| Initials |string |Initiales du contact. |
| MiddleName |string |Deuxième prénom du contact. |
| NickName |string |Surnom du contact. |
| Surname |string |Nom du contact. |
| Intitulé |string |Titre du contact. |
| Generation |string |Génération du contact. |
| EmailAddresses |array |Adresses e-mail du contact. |
| ImAddresses |array |Adresses de messagerie instantanée (MI) du contact. |
| JobTitle |string |Fonction du contact. |
| CompanyName |string |Nom de la société du contact. |
| department |string |Service du contact. |
| OfficeLocation |string |Emplacement du bureau du contact. |
| Profession |string |Profession du contact. |
| BusinessHomePage |string |Page d’accueil professionnelle du contact. |
| AssistantName |string |Nom de l’assistant du contact. |
| Manager |string |Nom du responsable du contact. |
| HomePhones |array |Numéros de téléphone personnels du contact. |
| BusinessPhones |array |Numéros de téléphone professionnels du contact. |
| MobilePhone1 |string |Numéro de téléphone mobile du contact. |
| HomeAddress |non défini |Adresse personnelle du contact. |
| BusinessAddress |non défini |Adresse professionnelle du contact. |
| OtherAddress |non défini |Autres adresses du contact. |
| YomiCompanyName |string |Nom phonétique de la société japonaise du contact. |
| YomiGivenName |string |Prénom japonais phonétique du contact. |
| YomiSurname |string |Nom japonais phonétique du contact. |
| Catégories |array |Catégories associées au contact. |
| ChangeKey |string |Identifie la version de l’objet d’événement. |
| DateTimeCreated |string |Heure de création du contact. |
| DateTimeLastModified |string |Heure de modification du contact. |

#### <a name="get-contact"></a>Obtenir un contact
Cette opération récupère un contact spécifique d’un dossier des contacts. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |ID du dossier |Sélectionner un dossier des contacts |
| id* |ID d’élément |Identificateur unique d’un contact à récupérer |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
Contact : contact

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| ID |string |Identificateur unique du contact. |
| ParentFolderId |string |Identificateur du dossier parent du contact. |
| Birthday |string |Date d’anniversaire du contact. |
| FileAs |string |Nom sous lequel est archivé le contact. |
| displayName |string |Nom d’affichage du contact. |
| GivenName |string |Prénom du contact. |
| Initials |string |Initiales du contact. |
| MiddleName |string |Deuxième prénom du contact. |
| NickName |string |Surnom du contact. |
| Surname |string |Nom du contact. |
| Intitulé |string |Titre du contact. |
| Generation |string |Génération du contact. |
| EmailAddresses |array |Adresses e-mail du contact. |
| ImAddresses |array |Adresses de messagerie instantanée (MI) du contact. |
| JobTitle |string |Fonction du contact. |
| CompanyName |string |Nom de la société du contact. |
| department |string |Service du contact. |
| OfficeLocation |string |Emplacement du bureau du contact. |
| Profession |string |Profession du contact. |
| BusinessHomePage |string |Page d’accueil professionnelle du contact. |
| AssistantName |string |Nom de l’assistant du contact. |
| Manager |string |Nom du responsable du contact. |
| HomePhones |array |Numéros de téléphone personnels du contact. |
| BusinessPhones |array |Numéros de téléphone professionnels du contact. |
| MobilePhone1 |string |Numéro de téléphone mobile du contact. |
| HomeAddress |non défini |Adresse personnelle du contact. |
| BusinessAddress |non défini |Adresse professionnelle du contact. |
| OtherAddress |non défini |Autres adresses du contact. |
| YomiCompanyName |string |Nom phonétique de la société japonaise du contact. |
| YomiGivenName |string |Prénom japonais phonétique du contact. |
| YomiSurname |string |Nom japonais phonétique du contact. |
| Catégories |array |Catégories associées au contact. |
| ChangeKey |string |Identifie la version de l’objet d’événement. |
| DateTimeCreated |string |Heure de création du contact. |
| DateTimeLastModified |string |Heure de modification du contact. |

#### <a name="delete-contact"></a>Supprimer un contact
Cette opération supprime un contact d’un dossier des contacts. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |ID du dossier |Sélectionner un dossier des contacts |
| id* |ID |Identificateur unique du contact à supprimer |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
Aucune.

#### <a name="update-contact"></a>Mettre à jour un contact
Cette opération met à jour un contact dans un dossier des contacts. 

| Nom de la propriété | Display Name | Description |
| --- | --- | --- |
| table* |ID du dossier |Sélectionner un dossier des contacts |
| id* |ID |Identificateur unique du contact à mettre à jour |
| item* |Élément |Élément de contact à mettre à jour |

Un astérisque (*) signifie que la propriété est requise.

##### <a name="output-details"></a>Détails des résultats
Contact : contact

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| ID |string |Identificateur unique du contact. |
| ParentFolderId |string |Identificateur du dossier parent du contact. |
| Birthday |string |Date d’anniversaire du contact. |
| FileAs |string |Nom sous lequel est archivé le contact. |
| displayName |string |Nom d’affichage du contact. |
| GivenName |string |Prénom du contact. |
| Initials |string |Initiales du contact. |
| MiddleName |string |Deuxième prénom du contact. |
| NickName |string |Surnom du contact. |
| Surname |string |Nom du contact. |
| Intitulé |string |Titre du contact. |
| Generation |string |Génération du contact. |
| EmailAddresses |array |Adresses e-mail du contact. |
| ImAddresses |array |Adresses de messagerie instantanée (MI) du contact. |
| JobTitle |string |Fonction du contact. |
| CompanyName |string |Nom de la société du contact. |
| department |string |Service du contact. |
| OfficeLocation |string |Emplacement du bureau du contact. |
| Profession |string |Profession du contact. |
| BusinessHomePage |string |Page d’accueil professionnelle du contact. |
| AssistantName |string |Nom de l’assistant du contact. |
| Manager |string |Nom du responsable du contact. |
| HomePhones |array |Numéros de téléphone personnels du contact. |
| BusinessPhones |array |Numéros de téléphone professionnels du contact. |
| MobilePhone1 |string |Numéro de téléphone mobile du contact. |
| HomeAddress |non défini |Adresse personnelle du contact. |
| BusinessAddress |non défini |Adresse professionnelle du contact. |
| OtherAddress |non défini |Autres adresses du contact. |
| YomiCompanyName |string |Nom phonétique de la société japonaise du contact. |
| YomiGivenName |string |Prénom japonais phonétique du contact. |
| YomiSurname |string |Nom japonais phonétique du contact. |
| Catégories |array |Catégories associées au contact. |
| ChangeKey |string |Identifie la version de l’objet d’événement. |
| DateTimeCreated |string |Heure de création du contact. |
| DateTimeLastModified |string |Heure de modification du contact. |

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
| 500 |Erreur interne du serveur. Une erreur inconnue s'est produite |
| default |L’opération a échoué. |

## <a name="next-steps"></a>Étapes suivantes
[Créez une application logique](../logic-apps/logic-apps-create-a-logic-app.md). Explorez les autres connecteurs disponibles dans les applications logiques en consultant notre [liste d’API](apis-list.md).




<!--HONumber=Jan17_HO3-->


