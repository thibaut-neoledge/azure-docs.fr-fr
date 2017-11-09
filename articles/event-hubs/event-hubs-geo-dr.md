---
title: "Géorécupération d’urgence Azure Event Hubs | Microsoft Docs"
description: "Découvrez comment utiliser les régions géographiques pour le basculement et la récupération d’urgence dans Azure Event Hubs."
services: event-hubs
documentationcenter: 
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: sethm
ms.openlocfilehash: 94c2782b3166fbc65ae755291a82a2a14556b96f
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2017
---
# <a name="azure-event-hubs-geo-disaster-recovery-preview"></a>Géorécupération d’urgence Azure Event Hubs (préversion)

Si un centre de données régional connaît un temps d’arrêt, il est essentiel que le traitement des données puisse continuer dans les autres régions ou centres de données. Pour cette raison, la *géorécupération d’urgence* et la *géoréplication* sont des fonctionnalités importantes pour les entreprises. Azure Event Hubs prend en charge la géorécupération d’urgence et la géoréplication au niveau de l’espace de noms. 

La fonctionnalité de géorécupération d’urgence d’Azure Event Hubs est une solution de récupération d’urgence. Les concepts et le workflow décrits dans cet article concernent des scénarios de sinistres, pas des pannes temporaires ou transitoires.

Pour obtenir une présentation détaillée de la récupération d’urgence dans Microsoft Azure, consultez [cet article](/azure/architecture/resiliency/disaster-recovery-azure-applications). 

## <a name="terminology"></a>Terminologie

**Couplage** : l’espace de noms principal est considéré comme *actif* et reçoit des messages. L’espace de noms de basculement est *passif* et ne reçoit pas de messages. Les métadonnées sont synchronisées entre ces deux espaces de noms, qui peuvent ainsi accepter facilement les messages sans aucune modification du code d’application. L’établissement de la configuration de récupération d’urgence entre les régions active et passive est appelée *couplage* de régions.

**Alias** : nom d’une configuration de récupération d’urgence que vous avez configurée. L’alias fournit une chaîne de connexion de nom de domaine complet (FQDN) stable. Les applications utilisent cet alias de chaîne de connexion pour se connecter à un espace de noms.

**Métadonnées** : fait référence à des noms de hubs d’événements, des groupes de consommateurs, des partitions, des unités de débit, des entités et des propriétés qui sont associés à l’espace de noms.

## <a name="enable-geo-disaster-recovery"></a>Activer la géorécupération d’urgence

L’activation du service de géorécupération d’urgence Event Hubs s’effectue en trois étapes : 

1. Créer un géocouplage, ce qui crée un alias de chaîne de connexion et assure la réplication des métadonnées en direct. 
2. Mettre à jour les chaînes de connexion de client existantes en fonction de l’alias créé à l’étape 1.
3. Déclencher le basculement : le géocouplage est interrompu et l’alias pointe vers l’espace de noms secondaire en tant que nouvel espace de noms principal.

L’illustration suivante montre ce flux de travail :

![Flux de géocouplage][1] 

### <a name="step-1-create-a-geo-pairing"></a>Étape 1 : Créer un géocouplage

Pour créer un couplage entre deux régions, vous avez besoin d’un espace de noms principal et d’un espace de noms secondaire. Vous créez alors un alias pour former la géopaire. Une fois les espaces de noms couplés avec un alias, les métadonnées sont régulièrement répliquées dans les deux espaces de noms. 

Le code suivant illustre comment procéder :

```csharp
ArmDisasterRecovery adr = await client.DisasterRecoveryConfigs.CreateOrUpdateAsync(
                                    config.PrimaryResourceGroupName,
                                    config.PrimaryNamespace,
                                    config.Alias,
                                    new ArmDisasterRecovery(){ PartnerNamespace = config.SecondaryNamespace});
```

### <a name="step-2-update-existing-client-connection-strings"></a>Étape 2 : Mettre à jour les chaînes de connexion de client existantes

Une fois le géocouplage terminé, les chaînes de connexion qui pointent vers les espaces de noms principaux doivent être mises à jour pour pointer vers l’alias de chaîne de connexion. Obtenez les chaînes de connexion comme indiqué dans l’exemple suivant :

```csharp
var accessKeys = await client.Namespaces.ListKeysAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
```

### <a name="step-3-initiate-a-failover"></a>Étape 3 : Déclencher un basculement

Si un incident se produit, ou si vous décidez de déclencher un basculement vers l’espace de noms secondaire, les métadonnées et les données commencent à circuler dans l’espace de noms secondaire. Étant donné que les applications utilisent les chaînes de connexion d’alias, aucune action supplémentaire n’est requise, car elles commencent automatiquement à lire et à écrire dans les hubs d’événements de l’espace de noms secondaire. 

Le code suivant montre comment déclencher le basculement :

```csharp
await client.DisasterRecoveryConfigs.FailOverAsync(config.SecondaryResourceGroupName,
                                                   config.SecondaryNamespace, config.Alias);
```

Une fois que le basculement est terminé et que vous avez besoin des données présentes dans l’espace de noms principal, pour extraire les données vous devez utiliser une chaîne de connexion explicite aux hubs d’événements dans l’espace de noms principal.

### <a name="other-operations-optional"></a>Autres opérations (facultatives)

Vous pouvez également arrêter le géocouplage ou supprimer un alias, comme indiqué dans le code suivant. Notez que pour supprimer un alias de chaîne de connexion, vous devez d’abord arrêter le géocouplage :

```csharp
// Break pairing
await client.DisasterRecoveryConfigs.BreakPairingAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, config.Alias);

// Delete alias connection string
// Before the alias is deleted, pairing must be broken
await client.DisasterRecoveryConfigs.DeleteAsync(config.PrimaryResourceGroupName,
                                                 config.PrimaryNamespace, config.Alias);
```

## <a name="considerations-for-public-preview"></a>Considérations relatives à la préversion publique

Notez les points suivants pour cette version :

1. La fonctionnalité de géorécupération d’urgence est disponible uniquement dans les régions Nord du centre des États-Unis et Sud du centre des États-Unis. 
2. La fonctionnalité est prise en charge uniquement pour les espaces de noms nouvellement créés.
3. Pour la préversion, seule la réplication des métadonnées est activée. Les données réelles ne sont pas répliquées.
4. Avec la préversion, l’activation de la fonctionnalité est gratuite. Toutefois, les espaces de noms principal et secondaire occasionnent des frais pour les unités de débit réservés.

## <a name="next-steps"></a>Étapes suivantes

* [L’exemple sur GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) décrit un flux de travail simple qui crée un géocouplage et déclenche un basculement pour un scénario de récupération d’urgence.
* La [référence d’API REST](/rest/api/eventhub/disasterrecoveryconfigs) décrit les API nécessaires pour effectuer la configuration de la géorécupération.

Pour plus d’informations sur Event Hubs, accédez aux liens suivants :

* Prise en main avec un [didacticiel des concentrateurs d’événements](event-hubs-dotnet-standard-getstarted-send.md)
* [Forum Aux Questions (FAQ) sur Event Hubs](event-hubs-faq.md)
* [Exemples d’applications qui utilisent des Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/eh-geodr1.png

