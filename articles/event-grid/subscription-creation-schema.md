---
title: "Schéma d’abonnement à Azure Event Grid"
description: "Décrit les propriétés d’abonnement à un événement avec Azure Event Grid."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 08/17/2017
ms.author: babanisa
ms.openlocfilehash: eff2352066a76010d6d882a7b7e1961870cd2d46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="event-grid-subscription-schema"></a>Schéma d’abonnement à Event Grid

Pour créer un abonnement Event Grid, envoyez une requête à l’opération Créer un abonnement aux événements. Utilisez le format suivant :

```
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2017-06-15-preview
``` 

Par exemple, pour créer un abonnement aux événements pour un compte de stockage nommé `examplestorage` dans un groupe de ressources nommé `examplegroup`, utilisez le format suivant :

```
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2017-06-15-preview
``` 

L’article décrit les propriétés et le schéma pour le corps de la requête.
 
## <a name="event-subscription-properties"></a>Propriétés de l’abonnement aux événements

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| destination | objet | Objet qui définit le point de terminaison. |
| filter | objet | Champ facultatif pour filtrer les types d’événements. |

### <a name="destination-object"></a>objet de destination

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| endpointType | string | Type de point de terminaison pour l’abonnement (webhook/HTTP, concentrateur d’événements ou file d’attente). | 
| endpointUrl | string |  | 

### <a name="filter-object"></a>objet de filtre

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| includedEventTypes | array | Affiche une correspondance lorsque le type d’événement du message d’événement correspond exactement à l’un de ces noms de type d’événement. Génère une erreur lorsque le nom de l’événement ne correspond pas aux noms de type d’événement inscrits pour la source d’événements. Génère une correspondance pour tous les types d’événements. |
| subjectBeginsWith | string | Filtre de correspondance de préfixe appliqué au champ objet du message de l’événement. La chaîne vide ou par défaut représente une correspondance générale. | 
| subjectEndsWith | string | Filtre de correspondance de suffixe appliqué au champ objet du message de l’événement. La chaîne vide ou par défaut représente une correspondance générale. |
| subjectIsCaseSensitive | string | Contrôle la correspondance sensible à la casse pour les filtres. |


## <a name="example-subscription-schema"></a>Exemple de schéma d’abonnement

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "blobCreated", "blobDeleted" ],
      "subjectBeginsWith": "blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "subjectIsCaseSensitive": "true"
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Event Grid, consultez l’article [What is Event Grid?](overview.md) (Présentation d’Event Grid).