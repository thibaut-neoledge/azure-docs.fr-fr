---
title: Archivage des journaux de diagnostic Azure | Microsoft Docs
description: "Découvrez comment archiver vos journaux de diagnostic Azure pour une conservation à long terme dans un compte de stockage."
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 3a55c73f-2ef3-45f3-8956-bcf9c0cb7e05
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2016
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: 3e82377c36da1563931bc0301daa4534e6f82377
ms.openlocfilehash: e3413236445f3f6034a228dc93624d3fdc9b87ad


---
# <a name="archive-azure-diagnostic-logs"></a>Archivage des journaux de diagnostic Azure
Dans cet article, nous vous expliquons comment utiliser le portail Azure, les applets de commande PowerShell, l’interface de ligne de commande ou l’API REST pour archiver vos [journaux de diagnostic Azure](monitoring-overview-of-diagnostic-logs.md) dans un compte de stockage. Cette option est utile si vous voulez conserver vos journaux de diagnostic avec une stratégie de rétention facultative à des fins d’audit, d’analyse statique ou de sauvegarde. Il n’est pas nécessaire que le compte de stockage se trouve dans le même abonnement que la ressource générant des journaux, à condition que l’utilisateur qui configure le paramètre ait un accès RBAC approprié aux deux abonnements.

## <a name="prerequisites"></a>Composants requis
Avant de commencer, vous devez [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) sur lequel vous pouvez archiver vos journaux de diagnostic. Nous vous recommandons vivement de ne pas utiliser un compte de stockage existant sur lequel sont stockées d’autres données de non-analyse, afin de pouvoir mieux contrôler l’accès aux données d’analyse. En revanche, si vous archivez également votre journal d’activité et des métriques de diagnostic sur un compte de stockage, il peut être judicieux d’utiliser également ce compte pour vos journaux de diagnostic, afin de centraliser toutes vos données d’analyse. Le compte de stockage que vous utilisez doit être un compte de stockage à usage général, et non un compte de stockage Blob.

## <a name="diagnostic-settings"></a>Paramètres de diagnostic
Pour archiver vos journaux de diagnostic à l’aide de l’une des méthodes ci-dessous, vous devez définir un **paramètre de diagnostic** pour chaque ressource. Le paramètre de diagnostic d’une ressource définit les catégories de journaux qui sont stockés ou transmis en continu, ainsi que les sorties : compte de stockage et/ou hub d’événement. Il définit également la stratégie de rétention (nombre de jours de conservation) pour les événements de chaque catégorie de journal stockés dans un compte de stockage. Si la stratégie de rétention est définie sur zéro, les événements de cette catégorie de journal sont stockés indéfiniment (c’est-à-dire pour toujours). Une stratégie de rétention peut également être définie sur n’importe quel nombre de jours entre 1 et 2147483647. [Vous trouverez plus d’informations sur les paramètres de diagnostic ici](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings). Les stratégies de rétention sont appliquées sur une base quotidienne. Donc, à la fin d’une journée (UTC), les journaux de la journée qui est désormais au-delà de la stratégie de rétention sont supprimés. Par exemple, si vous aviez une stratégie de rétention d’une journée, au début de la journée d’aujourd’hui les journaux d’avant-hier seraient supprimés.

## <a name="archive-diagnostic-logs-using-the-portal"></a>Archivage des journaux de diagnostic à l’aide du portail
1. Dans le portail, cliquez sur le panneau de la ressource pour laquelle vous voulez activer l’archivage des journaux de diagnostic.
2. Dans la section **Surveillance** du menu Paramètres de la ressource, sélectionnez **Diagnostics**.
   
    ![Section Surveillance du menu de la ressource](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-sec.png)
3. Cochez la case **Exporter vers un compte de stockage**, puis sélectionnez un compte de stockage. Si vous le souhaitez, vous pouvez également définir un nombre de jours de conservation de ces journaux à l’aide des curseurs **Rétention (jours)** . Si la valeur zéro est appliquée à la rétention, les journaux sont stockés pour une durée indéfinie.
   
    ![Panneau Journaux de diagnostic](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-blade.png)
4. Cliquez sur **Save**.

Les journaux de diagnostic sont archivés dans ce compte de stockage dès que de nouvelles données d’événement sont générées.

## <a name="archive-diagnostic-logs-via-the-powershell-cmdlets"></a>Archivage des journaux de diagnostic à l’aide des applets de commande PowerShell
```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Propriété | Requis | Description |
| --- | --- | --- |
| ResourceId |OUI |ID de la ressource pour laquelle vous voulez définir un paramètre de diagnostic. |
| StorageAccountId |Non |ID de ressource du compte de stockage dans lequel les journaux de diagnostic doivent être enregistrés. |
| Catégories |Non |Liste séparée par des virgules des catégories de journaux à activer. |
| Activé |OUI |Valeur booléenne indiquant si les diagnostics sont activés ou désactivés pour cette ressource. |
| RetentionEnabled |Non |Valeur booléenne indiquant si une stratégie de rétention est activée pour cette ressource. |
| RetentionInDays |Non |Nombre de jours pendant lesquels les événements doivent être conservés, compris entre 1 et 2147483647. Une valeur de zéro signifie que les journaux seront stockés pour une durée indéfinie. |

## <a name="archive-diagnostic-logs-via-the-cross-platform-cli"></a>Archivage des journaux de diagnostic à l’aide de l’interface de ligne de commande multiplateforme
```
azure insights diagnostic set --resourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg --storageId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage –categories networksecuritygroupevent,networksecuritygrouprulecounter --enabled true
```

| Propriété | Requis | Description |
| --- | --- | --- |
| ResourceId |OUI |ID de la ressource pour laquelle vous voulez définir un paramètre de diagnostic. |
| storageId |Non |ID de ressource du compte de stockage dans lequel les journaux de diagnostic doivent être enregistrés. |
| Catégories |Non |Liste séparée par des virgules des catégories de journaux à activer. |
| Activé |OUI |Valeur booléenne indiquant si les diagnostics sont activés ou désactivés pour cette ressource. |

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Archivage des journaux de diagnostic à l’aide de l’API REST
[Consultez ce document](https://msdn.microsoft.com/library/azure/dn931931.aspx) pour obtenir plus d’informations sur la configuration d’un paramètre de diagnostic à l’aide de l’API REST Azure Monitor.

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Schéma des journaux de diagnostic dans le compte de stockage
Une fois que vous avez configuré l’archivage, un conteneur de stockage est créé dans le compte de stockage dès qu’un événement se produit dans les catégories de journaux que vous avez activées. Les objets blob au sein du conteneur suivent le même format dans les journaux de diagnostic et le journal d’activité. La structure de ces objets blob est la suivante :

> insights-logs-{nom de la catégorie de journal}/resourceId=/SUBSCRIPTIONS/{ID d’abonnement}/RESOURCEGROUPS/{nom du groupe de ressources}/PROVIDERS/{nom du fournisseur de ressources}/{type de ressource}/{nom de la ressource}/y={année, quatre chiffres}/m={mois, deux chiffres}/d={jour, deux chiffres}/h={heure, deux chiffres, format 24 h}/m=00/PT1H.json
> 
> 

Ou, plus simplement,

> insights-logs-{nom de la catégorie de journal}/resourceId=/{ID de la ressource}/y={année, quatre chiffres}/m={mois, deux chiffres}/d={jour, deux chiffres}/h={heure, deux chiffres, format 24 h}/m=00/PT1H.json
> 
> 

Voici un exemple de nom d’objet blob :

> insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
> 
> 

Chaque objet blob PT1H.json contient un objet blob d’événements JSON qui se sont produits pendant l’heure spécifiée dans l’URL de l’objet blob (par exemple, h=12). Pendant l’heure en cours, les événements sont ajoutés au fichier PT1H.json à mesure qu’ils se produisent. La valeur de minute (m = 00) est toujours 00, étant donné que les événements du journal de diagnostic sont répartis en différents objets blob par heure.

Dans le fichier PT1H.json, chaque événement est stocké dans le tableau « enregistrements », au format suivant :

```
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| Nom de l'élément | Description |
| --- | --- |
| time |Horodatage lorsque l’événement a été généré par le service Azure traitant la demande correspondant à l’événement. |
| resourceId |ID de ressource de la ressource affectée. |
| operationName |Nom de l’opération. |
| category |Catégorie de journal de l’événement. |
| properties |Jeu de paires `<Key, Value>` (p. ex. Dictionary) décrivant les détails de l’événement. |

> [!NOTE]
> Les propriétés et l’utilisation de ces propriétés peuvent varier en fonction de la ressource.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* [Télécharger des objets blob pour analyse](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
* [Diffuser en continu le journal des activités Azure sur les Event Hubs](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [En savoir plus sur les journaux de diagnostic](monitoring-overview-of-diagnostic-logs.md)




<!--HONumber=Dec16_HO2-->


