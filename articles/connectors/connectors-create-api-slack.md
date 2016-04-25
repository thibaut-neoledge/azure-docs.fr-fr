<properties
pageTitle="Utiliser l’API Slack dans vos applications logiques | Microsoft Azure"
description="Prendre en main l’utilisation de l’API Slack (connecteur) dans vos applications logiques Microsoft Azure App Service"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="03/16/2016"
ms.author="deonhe"/>

# Prendre en main l’API Slack

Slack est un outil de communication collaboratif qui centralise toutes les communications de votre équipe dans un seul emplacement que vous pouvez consulter instantanément, où que vous vous trouviez.

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.

Avec le connecteur Slack, vous pouvez :

* créer des applications logiques.

Pour ajouter une opération à des applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## À propos des déclencheurs et des actions

Le connecteur Slack peut être utilisé en tant qu’action ; il n’y a aucun déclencheur. Tous les connecteurs prennent en charge les données aux formats JSON et XML.

 Le connecteur Slack met à votre disposition les actions et/ou les déclencheurs ci-après.

### Actions Slack
Vous pouvez effectuer les actions suivantes :

|Action|Description|
|--- | ---|
|PostMessage|Publier un message dans un canal spécifié.|
## Créer une connexion à Slack
Pour utiliser l’API Slack, vous devez créer une **connexion**, puis fournir les détails de ces propriétés :

|Propriété| Requis|Description|
| ---|---|---|
|Jeton|Oui|Fournir les informations d’identification de Slack|

Suivez ces étapes pour vous connecter à Slack et terminer la configuration de la **connexion** à Slack dans votre application logique :

1. Sélectionnez **Périodicité**.
2. Sélectionnez une **Fréquence** et entrez un **Intervalle**
3. Sélectionnez **Ajouter une action**. ![Configurer Slack][1]  
4. Entrez Slack dans la zone de recherche et attendez que la recherche renvoie toutes les entrées contenant Slack dans leur nom
5. Sélectionnez **Slack - Publier un message**
6. Sélectionnez **Se connecter à Slack** : ![Configurer Slack][2]
7. Entrez vos informations d’identification Slack pour vous connecter et autoriser l’application ![Configurer Slack][3]  
8. Vous êtes redirigé vers la page de connexion de votre organisation. **Autorisez** Slack à interagir avec votre application logique : ![Configurer Slack][5] 
9. Une fois l’autorisation terminée, vous êtes redirigé vers votre application logique pour la terminer en configurant la section **Slack - Obtenir tous les messages**. Ajoutez les autres déclencheurs et actions dont vous avez besoin. ![Configurer Slack][6]
10. Enregistrez votre travail en sélectionnant **Enregistrer** sur la barre de menu supérieure.


>[AZURE.TIP] Vous pouvez utiliser cette connexion dans d'autres applications logiques.

## Informations de référence sur l’API REST Slack
#### Cette documentation concerne la version 1.0.


### Publier un message dans un canal spécifié.
**```POST: /chat.postMessage```**



| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|channel|string|yes|query|(aucun)|Canal, groupe privé ou canal de messagerie instantanée auquel envoyer le message. Peut être un nom (ex : #general) ou un identifiant encodé.|
|texte|string|yes|query|(aucun)|Texte du message à envoyer. Pour plus d’options de mise en forme, consultez https://api.slack.com/docs/formatting.|
|username|string|no|query|(aucun)|Nom du robot|
|as\_user|booléenne|no|query|(aucun)|Transmettre true pour publier le message en tant qu’utilisateur authentifié, plutôt que robot|
|parse|string|no|query|(aucun)|Modifier la façon dont les messages sont traités. Pour plus d’informations, consultez https://api.slack.com/docs/formatting.|
|link\_names|integer|no|query|(aucun)|Rechercher et lier les noms de canaux et les noms d’utilisateur.|
|unfurl\_links|booléenne|no|query|(aucun)|Transmettre true pour activer le déploiement d’un contenu principalement textuel.|
|unfurl\_media|booléenne|no|query|(aucun)|Transmettre false pour désactiver le déploiement d’un contenu multimédia.|
|icon\_url|string|no|query|(aucun)|URL d’une image à utiliser comme icône pour ce message|
|icon\_emoji|string|no|query|(aucun)|Emoji à utiliser comme icône pour ce message|


### Voici les réponses possibles :

|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|408|Délai d’expiration de la demande|
|429|Trop de demandes|
|500|Erreur interne du serveur. Une erreur inconnue s’est produite.|
|503|Service Slack indisponible|
|504|Dépassement du délai de la passerelle|
|default|L’opération a échoué.|
------



## Définition(s) d’objet : 

 **Message** : message Yammer

Propriétés requises pour Message :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|id|integer|
|content\_excerpt|string|
|sender\_id|integer|
|replied\_to\_id|integer|
|created\_at|string|
|network\_id|integer|
|message\_type|string|
|sender\_type|string|
|url|string|
|web\_url|string|
|group\_id|integer|
|body|non défini|
|thread\_id|integer|
|direct\_message|booléenne|
|client\_type|string|
|client\_url|string|
|language|string|
|notified\_user\_ids|array|
|privacy|string|
|liked\_by|non défini|
|system\_message|booléenne|



 **PostOperationRequest** : représente une demande de publication sur Yammer pour le connecteur Yammer

Propriétés requises pour PostOperationRequest :

body

**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|body|string|
|group\_id|integer|
|replied\_to\_id|integer|
|direct\_to\_id|integer|
|broadcast|booléenne|
|topic1|string|
|topic2|string|
|topic3|string|
|topic4|string|
|topic5|string|
|topic6|string|
|topic7|string|
|topic8|string|
|topic9|string|
|topic10|string|
|topic11|string|
|topic12|string|
|topic13|string|
|topic14|string|
|topic15|string|
|topic16|string|
|topic17|string|
|topic18|string|
|topic19|string|
|topic20|string|



 **MessageList** : liste des messages

Propriétés requises pour MessageList :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|messages|array|



 **MessageBody** : corps du message

Propriétés requises pour MessageBody :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|parsed|string|
|plain|string|
|rich|string|



 **LikedBy** : aimé par

Propriétés requises pour LikedBy :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|count|integer|
|names|array|



 **YammmerEntity** : aimé par

Propriétés requises pour YammmerEntity :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|type|string|
|id|integer|
|full\_name|string|


## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)
## Définition(s) d’objet : 

 **WebResultModel** : résultats de la recherche sur le web par Bing

Propriétés requises pour WebResultModel :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|Intitulé|string|
|Description|string|
|DisplayUrl|string|
|ID|string|
|FullUrl|string|



 **VideoResultModel** : résultats de la recherche de vidéos par Bing

Propriétés requises pour VideoResultModel :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|Intitulé|string|
|DisplayUrl|string|
|ID|string|
|MediaUrl|string|
|Runtime|integer|
|Miniature|non défini|



 **ThumbnailModel** : propriétés de miniature de l’élément multimédia

Propriétés requises pour ThumbnailModel :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|MediaUrl|string|
|ContentType|string|
|Largeur|integer|
|Hauteur|integer|
|FileSize|integer|



 **ImageResultModel** : résultats de la recherche d’images par Bing

Propriétés requises pour ImageResultModel :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|Intitulé|string|
|DisplayUrl|string|
|ID|string|
|MediaUrl|string|
|SourceUrl|string|
|Miniature|non défini|



 **NewsResultModel** : résultats de la recherche d’actualités par Bing

Propriétés requises pour NewsResultModel :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|Intitulé|string|
|Description|string|
|DisplayUrl|string|
|ID|string|
|Source|string|
|Date|string|



 **SpellResultModel** : résultats des suggestions orthographiques de Bing

Propriétés requises pour SpellResultModel :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|ID|string|
|Valeur|string|



 **RelatedSearchResultModel** : résultats de la recherche liée par Bing

Propriétés requises pour RelatedSearchResultModel :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|Intitulé|string|
|ID|string|
|BingUrl|string|



 **CompositeSearchResultModel** : résultats de la recherche composite par Bing

Propriétés requises pour CompositeSearchResultModel :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|WebResultsTotal|integer|
|ImageResultsTotal|integer|
|VideoResultsTotal|integer|
|NewsResultsTotal|integer|
|SpellSuggestionsTotal|integer|
|WebResults|array|
|ImageResults|array|
|VideoResults|array|
|NewsResults|array|
|SpellSuggestionResults|array|
|RelatedSearchResults|array|


## Définition(s) d’objet : 

 **PostOperationResponse** : représente une réponse de l’opération de publication du connecteur Slack pour publier sur Slack

Propriétés requises pour PostOperationResponse :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|ok|booléenne|
|channel|string|
|ts|string|
|message|non défini|
|error|string|



 **MessageItem** : message de canal.

Propriétés requises pour MessageItem :


Aucune des propriétés n’est obligatoire.


**Toutes les propriétés** :


| Nom | Type de données |
|---|---|
|texte|string|
|id|string|
|user|string|
|created|integer|
|is\_user-deleted|booléenne|


## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png

<!---HONumber=AcomDC_0413_2016-->