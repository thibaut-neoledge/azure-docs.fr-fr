---
title: Journaux des événements Windows dans Log Analytics | Microsoft Docs
description: Les journaux d’événements Windows sont une des sources de données les plus couramment utilisées par Log Analytics.  Cet article décrit comment configurer la collecte des journaux d’événements Windows et des détails des enregistrements qu'ils créent dans le référentiel OMS.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn

ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: bwren

---
# <a name="windows-event-log-data-sources-in-log-analytics"></a>Sources de données de journal d’événements Windows dans Log Analytics
Journaux d’événements Windows sont l’une des [sources de données](log-analytics-data-sources.md) les plus utilisées pour les agents Windows car il s'agit de la méthode utilisée par la plupart des applications pour consigner des informations et des erreurs.  Vous pouvez collecter des événements à partir de journaux standard tels que Système et Application, ou spécifier des journaux personnalisés créés par les applications que vous souhaitez surveiller.

![Événements Windows](media/log-analytics-data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Configuration des journaux d’événements Windows
Configurez les journaux des événements Windows à partir du [menu Données dans Paramètres de Log Analytics](log-analytics-data-sources.md#configuring-data-sources).

Log Analytics collecte uniquement les événements des journaux d’événements Windows spécifiés dans les paramètres.  Vous pouvez ajouter un nouveau journal en tapant le nom du journal puis en cliquant sur **+**.  Pour chaque journal, seuls les événements avec les niveaux de gravité sélectionnés seront collectés.  Vérifiez les niveaux de gravité du journal que vous souhaitez collecter.  Vous ne pouvez pas fournir d’autres critères supplémentaires pour filtrer les événements.

![Configurer les événements Windows](media/log-analytics-data-sources-windows-events/configure.png)

## <a name="data-collection"></a>Collecte des données
Log Analytics collectera chaque événement correspondant à un niveau de gravité sélectionné à partir d'un journal d’événements surveillé à mesure que l'événement est créé.  L’agent enregistre sa place dans chaque journal d’événements d’où il la collecte.  Si l’agent est mis hors connexion pendant un moment, Log Analytics collecte les événements à partir de là où il s’était arrêté, même si ces événements ont été créés lorsque l’agent était hors connexion.

## <a name="windows-event-records-properties"></a>Propriétés des enregistrements d’événements Windows
Les enregistrements d'événements Windows sont de type **Événement** et possèdent les propriétés affichées dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| Ordinateur |Nom de l'ordinateur à partir duquel l'événement a été collecté. |
| EventCategory |Catégorie de l’événement. |
| EventData |Toutes les données d'événement au format brut. |
| EventID |Numéro de l’événement. |
| EventLevel |Gravité de l'événement au format numérique. |
| EventLevelName |Gravité de l'événement au format texte. |
| EventLog |Nom du journal des événements à partir duquel l'événement a été collecté. |
| ParameterXml |Valeurs des paramètres d'événement au format XML. |
| ManagementGroupName |Nom du groupe d'administration pour les agents SCOM.  Pour les autres agents, il s'agit d’AOI-<workspace ID> |
| RenderedDescription |Description de l'événement avec les valeurs de paramètres |
| Source |Source de l’événement. |
| SourceSystem |Type d’agent auprès duquel l’événement a été collecté. <br> Ops Manager – Agent Windows, connexion directe ou SCOM <br>  Linux – Tous les agents Linux  <br>  AzureStorage – Diagnostics Azure |
| TimeGenerated |Date et heure de création de l’événement dans Windows. |
| Nom d’utilisateur |Nom d'utilisateur du compte qui a consigné l'événement. |

## <a name="log-searches-with-windows-events"></a>Recherches de journaux avec des événements Windows
Le tableau suivant fournit plusieurs exemples de recherches qui extraient des enregistrements d’événements Windows.

| Interroger | Description |
|:--- |:--- |
| Type=Event |Tous les événements Windows. |
| Type=Event EventLevelName=error |Tous les événements Windows avec la gravité de l'erreur. |
| Type=Event &#124; Measure count() by Source |Nombre d’événements Windows par source. |
| Type=Event EventLevelName=error &#124; Measure count() by Source |Nombre d’événements d’erreur Windows par source. |

## <a name="next-steps"></a>Étapes suivantes
* Configurez Log Analytics pour collecter d’autres [sources de données](log-analytics-data-sources.md) à analyser.
* En savoir plus sur les [recherches de journal](log-analytics-log-searches.md) pour analyser les données collectées à partir de sources de données et de solutions.  
* Utilisez [Champs personnalisés](log-analytics-custom-fields.md) pour analyser les enregistrements d'événements dans des champs individuels.
* Configurez la [collecte des compteurs de performances](log-analytics-data-sources-performance-counters.md) à partir de vos agents Windows.

<!--HONumber=Oct16_HO2-->


