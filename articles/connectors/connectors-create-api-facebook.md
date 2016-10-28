<properties
    pageTitle="Ajouter le connecteur Facebook à vos applications logiques | Microsoft Azure"
    description="Vue d’ensemble du connecteur Facebook avec les paramètres d’API REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# Prise en main du connecteur Facebook
Connectez-vous à Facebook et publiez dans un journal, obtenez un flux de page et bien plus encore.

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.


Avec Facebook, vous pouvez effectuer les opérations suivantes :

- Créer votre flux d’activité en fonction des données que vous obtenez de Facebook.
- Utiliser un déclencheur quand une publication est reçue.
- Utiliser des actions pour publier dans votre journal, obtenir un flux de page et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, quand il y a une nouvelle publication dans votre journal, vous pouvez la transférer vers votre flux Twitter.



Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions
Le connecteur Facebook inclut le déclencheur et les actions suivants.

| Déclencheurs | Actions|
| --- | --- |
| <ul><li>Quand il y a une nouvelle publication dans mon journal</li></ul> |<ul><li>Obtenir un flux à partir de mon journal</li><li>Publier dans mon journal</li><li>Quand il y a une nouvelle publication dans mon journal</li><li>Obtenir un flux de page</li><li>Obtenir le journal d’un utilisateur</li><li>Publier dans une page</li></ul>

Tous les connecteurs prennent en charge les données aux formats JSON et XML.

## Créer une connexion à Facebook
Quand vous ajoutez ce connecteur à vos applications logiques, vous devez autoriser celles-ci à se connecter à votre compte Facebook.

1. Connectez-vous à votre compte Facebook.
2. Sélectionnez **Autoriser** et permettez à vos applications logiques de se connecter à votre compte Facebook et de l’utiliser.

>[AZURE.INCLUDE [Procédure de création d’une connexion à Facebook](../../includes/connectors-create-api-facebook.md)]

>[AZURE.TIP] Vous pouvez utiliser cette même connexion Facebook dans d’autres applications logiques.

## Informations de référence sur l'API REST Swagger
S’applique à la version 1.0.

### Obtenir un flux à partir de mon journal
Obtient les flux à partir du journal de l’utilisateur connecté. ```GET: /me/feed```

| Name|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|fields|string|no|query|(aucun) |Spécifie les champs à retourner. Exemple : id, name, image.|
|limit|integer|no|query| (aucun)|Nombre maximal de publications à récupérer|
|par|string|no|query| (aucun)|Limiter la liste des publications à celles auxquelles est associé un emplacement.|
|filter|string|no|query| (aucun)|Récupérer uniquement les publications qui correspondent à un filtre de flux particulier.|

#### Réponse
|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


### Publier dans mon journal
Publier un message de statut dans le journal d’un utilisateur connecté. ```POST: /me/feed```

| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|post|string |yes|body|(aucun) |Nouveau message à publier|

#### Réponse
|Name|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


### Quand il y a une nouvelle publication dans mon journal
Déclenche un nouveau flux quand il y a une nouvelle publication dans le journal de l’utilisateur connecté. ```GET: /trigger/me/feed```

Il n’existe aucun paramètre.

#### Réponse
|Name|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


### Obtenir un flux de page
Obtenir les publications du flux d’une page spécifiée. ```GET: /{pageId}/feed```

| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|pageId|string|yes|path| (aucun)|ID de la page à partir de laquelle les publications doivent être récupérées.|
|limit|integer|no|query| (aucun)|Nombre maximal de publications à récupérer|
|include\_hidden|booléenne|no|query|(aucun) |Indique s’il faut inclure des publications masquées par la page|
|fields|string|no|query|(aucun) |Spécifie les champs à retourner. Exemple : id, name, image.|

#### Réponse
|Nom|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


### Obtenir le journal d’un utilisateur
Obtenir les publications du journal d’un utilisateur. ```GET: /{userId}/feed```

| Name|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|userId|string|yes|path|(aucun) |ID de l’utilisateur dont le journal doit être récupéré.|
|limit|integer|no|query|(aucun) |Nombre maximal de publications à récupérer|
|par|string|no|query|(aucun) |Limiter la liste des publications à celles auxquelles est associé un emplacement.|
|filter|string|no|query| (aucun)|Récupérer uniquement les publications qui correspondent à un filtre de flux particulier.|
|fields|string|no|query| (aucun)|Spécifie les champs à retourner. Exemple : id, name, image.|

#### Réponse
|Name|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


### Publier dans une page
Publier un message dans une page Facebook en tant qu’utilisateur connecté. ```POST: /{pageId}/feed```

| Nom|Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|pageId|string|yes|path|(aucun) |ID de la page à publier.|
|post|divers |yes|body|(aucun) |Nouveau message à publier|

#### Réponse
|Name|Description|
|---|---|
|200|OK|
|400|Demande incorrecte|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


## Définitions d’objet

#### GetFeedResponse

|Nom de la propriété | Type de données | Requis|
|---|---|---|
|données|array|no|

#### TriggerFeedResponse

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|données|array|no|

#### PostItem : entrée unique dans le flux d’un profil
Le profil peut être un utilisateur, une page, une application ou un groupe.

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|id|string|no|
|admin\_creator|array|no|
|caption|string|no|
|created\_time|string|no|
|description|string|no|
|feed\_targeting|non défini|no|
|from|non défini|no|
|icon|string|no|
|is\_hidden|booléenne|no|
|is\_published|booléenne|no|
|link|string|no|
|message|string|no|
|name|string|no|
|object\_id|string|no|
|picture|string|no|
|place|non défini|no|
|privacy|non défini|no|
|properties|array|no|
|source|string|no|
|status\_type|string|no|
|story|string|no|
|targeting|non défini|no|
|to|array|no|
|type|string|no|
|updated\_time|string|no|
|with\_tags|non défini|no|

#### TriggerItem : entrée unique dans le flux d’un profil
Le profil peut être un utilisateur, une page, une application ou un groupe.

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|id|string|no|
|created\_time|string|no|
|from|non défini|no|
|message|string|no|
|type|string|no|

#### AdminItem

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|id|string|no|
|link|string|no|

#### PropertyItem

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|name|string|no|
|texte|string|no|
|href|string|no|

#### UserPostFeedRequest

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|message|string|yes|
|link|string|no|
|picture|string|no|
|name|string|no|
|caption|string|no|
|description|string|no|
|place|string|no|
|tags|string|no|
|privacy|non défini|no|
|object\_attachment|string|no|

#### PagePostFeedRequest

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|message|string|yes|
|link|string|no|
|picture|string|no|
|name|string|no|
|caption|string|no|
|description|string|no|
|actions|array|no|
|place|string|no|
|tags|string|no|
|object\_attachment|string|no|
|targeting|non défini|no|
|feed\_targeting|non défini|no|
|published|booléenne|no|
|scheduled\_publish\_time|string|no|
|backdated\_time|string|no|
|backdated\_time\_granularity|string|no|
|child\_attachments|array|no|
|multi\_share\_end\_card|booléenne|no|

#### PostFeedResponse

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|id|string|no|

#### ProfileCollection

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|données|array|no|

#### UserItem

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|id|string|no|
|first\_name|string|no|
|last\_name|string|no|
|name|string|no|
|gender|string|no|
|about|string|no|

#### ActionItem

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|name|string|no|
|link|string|no|

#### TargetItem

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|countries|array|no|
|locales|array|no|
|regions|array|no|
|cities|array|no|

#### FeedTargetItem : objet qui contrôle le ciblage du flux d’informations pour cette publication
Tout membre de ces groupes est susceptible de voir cette publication, à la différence des autres personnes. S’applique uniquement aux pages.

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|countries|array|no|
|regions|array|no|
|cities|array|no|
|age\_min|integer|no|
|age\_max|integer|no|
|genders|array|no|
|relationship\_statuses|array|no|
|interested\_in|array|no|
|college\_years|array|no|
|interests|array|no|
|relevant\_until|integer|no|
|education\_statuses|array|no|
|locales|array|no|

#### PlaceItem

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|id|string|no|
|name|string|no|
|overall\_rating|number|no|
|location|non défini|no|

#### LocationItem

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|city|string|no|
|country|string|no|
|latitude|number|no|
|located\_in|string|no|
|longitude|number|no|
|name|string|no|
|region|string|no|
|state|string|no|
|street|string|no|
|zip|string|no|

#### PrivacyItem

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|description|string|no|
|value|string|yes|
|allow|string|no|
|deny|string|no|
|friends|string|no|

#### ChildAttachmentsItem

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|link|string|no|
|picture|string|no|
|image\_hash|string|no|
|name|string|no|
|description|string|no|

#### PostPhotoRequest

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|url|string|yes|
|caption|string|no|

#### PostPhotoResponse

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|id|string|yes|
|post\_id|string|yes|

#### PostVideoRequest

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|videoData|string|yes|
|description|string|yes|
|title|string|yes|
|uploadedVideoName|string|no|

#### GetPhotoResponse

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|données|non défini|yes|

#### GetPhotoResponseItem

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|url|string|yes|
|is\_silhouette|booléenne|yes|
|height|string|no|
|width|string|no|

#### GetEventResponse

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|données|array|yes|

#### GetEventResponseItem

|Nom de la propriété | Type de données |Requis|
|---|---|---|
|id|string|yes|
|name|string|yes|
|start\_time|string|no|
|end\_time|string|no|
|timezone|string|no|
|location|string|no|
|description|string|no|
|ticket\_uri|string|no|
|rsvp\_status|string|yes|


## Étapes suivantes

[Créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0824_2016-->