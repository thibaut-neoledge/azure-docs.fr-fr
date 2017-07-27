---
title: Analyse des journaux pour Azure CDN | Microsoft Docs
description: "Le client peut activer l’analyse des journaux pour Azure CDN."
services: cdn
documentationcenter: 
author: lichard
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: rli
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: e3be8aced50a2ce330523d8633f524803af8c8a7
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---

# <a name="diagnostics-logs-for-azure-cdn"></a>Journaux de diagnostic pour Azure CDN

Après avoir activé le CDN pour votre application, vous souhaiterez probablement surveiller l’utilisation du CDN, vérifier l’intégrité de votre application et corriger les éventuels problèmes. Azure CDN fournit ces fonctionnalités avec les [analyses de base](cdn-analyze-usage-patterns.md).

En tant qu’utilisateur d’Azure CDN actuel avec un profil Verizon standard ou premium, vous pouvez déjà afficher les analyses de base dans le portail supplémentaire accessible via l’option « Gérer » à partir du portail Azure. Avec cette nouvelle fonctionnalité de [Journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), vous pouvez maintenant afficher les analyses de base et les enregistrer dans une ou plusieurs destinations, dont un compte de stockage Azure, un concentrateur d’événements Azure, et/ou [un espace de travail Log Analytics (OMS)](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started). Cette fonctionnalité est disponible pour tous les points de terminaison CDN appartenant à des profils CDN Verizon (standard et premium) et Akamai (Standard).

Les journaux de diagnostic vous permettent d’exporter des métriques d’utilisation de base de votre point de terminaison vers diverses sources afin de pouvoir les utiliser de façon personnalisée. Vous pouvez par exemple procéder comme suit :

- Exporter les données vers un stockage d’objets blob, exporter au format CSV et générer des graphiques dans Excel.
- Exporter des données vers des concentrateurs d’événements et mettre les données en corrélation avec d’autres services Azure.
- Exporter des données vers Log Analytics et afficher les données dans votre propre espace de travail OMS


![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/OMS-workspace.png)

La procédure ci-dessous passera par le schéma des données d’analyse de base, les étapes d’activation de la fonctionnalité et la livraison à différentes destinations, et la consommation à partir de ces destinations.

## <a name="enable-logging-with-azure-portal"></a>Activation de la journalisation avec le portail Azure

Les journaux de diagnostic sont **désactivés** par défaut. Suivez les étapes ci-dessous pour les activer :


Connectez-vous au [portail Azure](http://portal.azure.com). Si vous n’avez pas activé CDN pour votre flux de travail, [activez Azure CDN](cdn-create-new-endpoint.md) avant de continuer.

1. Dans le portail, accédez à **Profil CDN**.
2. Sélectionnez un profil CDN, puis sélectionnez le point de terminaison CDN pour lequel vous souhaitez activer les **journaux de diagnostic**.
    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/Browse-to-Diagnostics-logs.png)
3. Accédez au panneau **Journaux de diagnostic** sous la section **Surveillance**, puis modifiez l’état sur **Activé**.
    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/Diagnostics-logs-options.png)
4. Sélectionnez et configurez la cible d’archivage (compte de stockage, Event Hub, Log Analytics) de votre choix. 
    
    Dans cet exemple, le stockage Azure est utilisé pour stocker les journaux. Sélectionnez **Archiver dans un compte de stockage**, sélectionnez la durée de conservation en jours, puis cliquez sur **CoreAnalytics** sous **Journal**. Nous proposons uniquement **CoreAnalytics** actuellement, mais d’autres types de journaux seront disponibles à l’avenir. Voir ci-dessous pour le schéma, l’agrégation et les informations de retard sur CoreAnalytics. 

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/Diagnostics-logs-storage.png)
5.  Enregistrez la nouvelle configuration de diagnostic.
    
    Les données de journal Verizon sont retardées de 1 heure et ont besoin de jusqu'à 2 heures pour commencer à apparaître après l’achèvement de la propagation du point de terminaison.
    Les données de journal Akamai sont retardées de 24 heures et ont besoin de jusqu'à 2 heures pour commencer à apparaître si la création remonte à plus de 24 heures. Si elle vient d’être effectuée, il falloir jusqu'à 25 heures pour que les journaux commencent à apparaître.

## <a name="enable-logging-with-powershell"></a>Activation de la journalisation avec PowerShell

Voici deux exemples d’activation et d’obtention des journaux de diagnostic via les applets de commande Azure PowerShell.

###<a name="enabling-diagnostic-logs-in-a-storage-account"></a>Activation des journaux de diagnostic dans un compte de stockage

Pour activer les journaux de diagnostic dans un compte de stockage, utilisez cette commande :

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
```

## <a name="consuming-logs-from-storage"></a>Consommation des journaux depuis le stockage
Cette section décrit le schéma d’analyses de base de CDN et leur organisation à l’intérieur d’un compte de stockage Azure, et fournit des exemples de code pour télécharger les journaux dans un fichier CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Utilisation de l’explorateur de stockage Microsoft Azure
Pour pouvoir accéder aux données d’analyse de base à partir du compte de stockage Azure, vous avez d’abord besoin d’un outil pour accéder au contenu dans un compte de stockage. Si plusieurs outils sont disponibles sur le marché, celui que nous recommandons est Microsoft Azure Storage Explorer. Vous pouvez télécharger l’outil [ici](http://storageexplorer.com/). Après le téléchargement et l’installation du logiciel, veuillez le configurer pour utiliser le même compte de stockage Azure que celui qui a été configuré en tant que destination pour les journaux de diagnostic CDN.

1.    Ouvrez **l’explorateur de stockage Microsoft Azure**
2.    Localiser le compte de stockage
3.    Accédez au nœud **« Conteneurs d’objets Blob »** sous ce compte de stockage, puis développez le nœud
4.    Sélectionnez le conteneur nommé **« journaux-insights-coreanalytics »** et double-cliquez dessus
5.    Les résultats s’affichent dans le volet droit, en commençant par le premier niveau, qui ressemble à **« resourceId= »**. Continuez à cliquer jusqu'à ce que vous voyiez le fichier **PT1H.json**. Voir la remarque ci-dessous pour une explication sur le chemin d’accès.
6.    Chaque objet blob **PT1H.json** représente les journaux d’analyse pendant une heure pour un point de terminaison CDN spécifique ou son domaine personnalisé.
7.    Le schéma du contenu de ce fichier JSON est décrit dans la section Schéma des journaux Core Analytics


> [!NOTE]
> **Format du chemin d’accès des objets blob**
> 
> Les journaux Core Analytics sont générés toutes les heures. Toutes les données pendant une heure sont collectées et stockées à l’intérieur d’un objet blob Azure en tant que charge utile JSON. Le chemin d’accès à cet objet blob Azure apparaît comme s’il existait une structure hiérarchique. En effet, l’outil Explorateur de stockage interprète '/' comme un séparateur de répertoire, et affiche la hiérarchie pour des raisons pratiques. En fait, le chemin d’accès complet représente simplement le nom de l’objet blob. Ce nom d’objet blob suit la convention d’affectation de noms suivante    
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**Description des champs :**

|value|description|
|-------|---------|
|ID d’abonnement    |ID de l’abonnement Azure. Cette valeur est au format Guid.|
|Ressource |Nom de groupe    Nom du groupe de ressources auquel les ressources CDN appartiennent.|
|Nom de profil |Nom du profil CDN|
|Nom du point de terminaison |Nom du point de terminaison CDN|
|Year|    Représentation à 4 chiffres de l’année, par ex. 2017|
|Mois|    Représentation à 2 chiffres du mois de l’année. 01 = Janvier... 12 = Décembre|
|jour|    Représentation à 2 chiffres du jour du mois|
|PT1H.json|    Fichier JSON où sont effectivement stockées les données d’analyse|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportation des données de Core Analytics vers un fichier CSV

Pour faciliter l’accès à Core Analytics, nous fournissons un exemple de code pour un outil qui permet de télécharger les fichiers JSON dans un format de fichier plat avec séparation par virgules qui peut être utilisé pour créer facilement des graphiques ou autres agrégations.

Voici comment vous pouvez utiliser l’outil :

1.    Consultez le lien GitHub : [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv )
2.    Téléchargement du code
3.    Suivez les instructions pour compiler et configurer
4.    Exécuter l’outil
5.    Le fichier CSV résultant présente les données d’analyse dans une hiérarchie plate simple.

## <a name="diagnostic-logs-types"></a>Types de journaux de diagnostic

Nous proposons actuellement uniquement les journaux Core Analytics qui contiennent les métriques affichant les statistiques de réponse HTTP et de sortie, comme dans les points de présence/périmètres CDN. Au fil du temps, nous allons ajouter des types de journaux supplémentaires.

### <a name="core-analytics-metrics-details"></a>Détails des métriques de Core Analytics
Voici une liste des métriques disponibles dans les journaux de Core Analytics. Toutes les métriques ne sont pas disponibles auprès tous les fournisseurs, même si ces différences sont minimes. Le tableau ci-dessous indique également si une métrique donnée est disponible à partir d’un fournisseur particulier. Veuillez noter que les métriques sont disponibles uniquement pour les points de terminaison CDN recevant du trafic.


|Mesure                     | Description   | Verizon  | Akamai 
|---------------------------|---------------|---|---|
| RequestCountTotal         |Nombre total d’occurrences de requêtes pendant cette période| Oui  |Oui   |
| RequestCountHttpStatus2xx |Nombre de toutes les requêtes qui ont abouti à un code 2xx (par ex. 200, 202)              | Oui  |Oui   |
| RequestCountHttpStatus3xx | Nombre de toutes les requêtes qui ont abouti à un code 3xx (par ex. 300, 302)              | Oui  |Oui   |
| RequestCountHttpStatus4xx |Nombre de toutes les requêtes qui ont abouti à un code 4xx (par ex. 400, 404)               | Oui   |Oui   |
| RequestCountHttpStatus5xx | Nombre de toutes les requêtes qui ont abouti à un code 5xx (par ex. 500, 504)              | Oui  |Oui   |
| RequestCountHttpStatusOthers |  Nombre d’occurrences de tous les autres codes HTTP (en dehors de 2xx-5xx) | Oui  |Oui   |
| RequestCountHttpStatus200 | Nombre de toutes les requêtes qui ont abouti au code de réponse HTTP 200              |Non   |Oui   |
| RequestCountHttpStatus206 | Nombre de toutes les requêtes qui ont abouti au code de réponse HTTP 206              |Non   |Oui   |
| RequestCountHttpStatus302 | Nombre de toutes les requêtes qui ont abouti au code de réponse HTTP 302              |Non   |Oui   |
| RequestCountHttpStatus304 |  Nombre de toutes les requêtes qui ont abouti au code de réponse HTTP 304             |Non   |Oui   |
| RequestCountHttpStatus404 | Nombre de toutes les requêtes qui ont abouti au code de réponse HTTP 404              |Non   |Oui   |
| RequestCountCacheHit |Nombre de toutes les requêtes qui ont abouti à un accès au cache. Cela signifie que la ressource a été traitée directement du point de présence vers le client.               | Oui  |Non   |
| RequestCountCacheMiss | Nombre de toutes les requêtes qui ont abouti à un échec de cache. Cela signifie que la ressource est introuvable sur le point de présence le plus proche du client et a donc été récupérée à l’origine.              |Oui   | Non  |
| RequestCountCacheNoCache | Nombre de requêtes vers une ressource empêchées d’être mises en cache en raison d’une configuration de l’utilisateur sur le périmètre.              |Oui   | Non  |
| RequestCountCacheUncacheable | Nombre de requêtes de ressources empêchées d’être mises en cache par les en-têtes Cache-Control et Expires, qui indiquent qu’elles ne doivent pas être mises en cache sur un point de présence ou par le client HTTP                |Oui   |Non   |
| RequestCountCacheOthers | Nombre de toutes les requêtes avec un état du cache non traité ci-dessus.              |Oui   | Non  |
| EgressTotal | Transfert de données sortantes en Go              |Oui   |Oui   |
| EgressHttpStatus2xx | Transfert de données sortantes * pour les réponses avec des codes d’état HTTP 2xx en Go            |Oui   |Non   |
| EgressHttpStatus3xx | Transfert de données sortantes pour les réponses avec des codes d’état HTTP 3xx en Go              |Oui   |Non   |
| EgressHttpStatus4xx | Transfert de données sortantes pour les réponses avec des codes d’état HTTP 4xx en Go               |Oui   | Non  |
| EgressHttpStatus5xx | Transfert de données sortantes pour les réponses avec des codes d’état HTTP 5xx en Go               |Oui   |  Non |
| EgressHttpStatusOthers | Transfert de données sortantes pour les réponses avec d’autres codes d’état HTTP en Go                |Oui   |Non   |
| EgressCacheHit |  Transfert de données sortantes pour les réponses qui ont été livrées directement depuis le cache CDN sur les points de présence/périmètres CDN    |Oui   |  Non |
| EgressCacheMiss | Transfert des données sortantes pour les réponses introuvables sur le serveur le plus proche et récupérées à partir du serveur d’origine              |Oui   |  Non |
| EgressCacheNoCache | Transferts de données sortants pour les ressources empêchés d’être mis en cache en raison d’une configuration de l’utilisateur sur le périmètre.                |Oui   |Non   |
| EgressCacheUncacheable | Transferts de données sortants pour les ressources empêchés d’être mis en cache par les en-têtes Cache-Control et/ou Expires, qui indiquent qu’elles ne doivent pas être mises en cache sur un point de présence ou par le client HTTP                    |Oui   | Non  |
| EgressCacheOthers |  Transfère les données sortantes pour d’autres scénarios de cache.             |Oui   | Non  |

*Le transfert de données sortantes fait référence au trafic produit des serveurs POP CDN vers le client.


### <a name="schema-of-the-core-analytics-logs"></a>Schéma des journaux Core Analytics 

Tous les journaux sont stockés au format JSON, et chaque entrée comprend des champs de chaîne au schéma présenté ci-après :

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Où « time » représente l’heure de début des limites d’heure pour lesquelles les statistiques sont rapportées. Notez que lorsqu’une métrique n’est pas prise en charge par un fournisseur CDN, il y aura une valeur null au lieu d’un entier ou d’un double. Cette valeur null indique l’absence de métrique, et diffère de la valeur 0. Notez également qu’il y aura un ensemble de ces métriques par domaine configuré sur le point de terminaison.

Exemple de propriétés ci-dessous :

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```
## <a name="additional-resources"></a>Ressources supplémentaires

* [Journaux de diagnostic Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Core Analytics via le portail supplémentaire Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)


