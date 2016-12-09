---
title: Limites et configuration des applications logiques | Microsoft Docs
description: "Vue d’ensemble des limites de service et des valeurs de configuration disponibles pour Logic Apps."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: dwrede
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 47c13d5cbaeb114da18b710c52e2c989df4c0e01
ms.openlocfilehash: 2417d4e3d8033ff8d66ace8b27f398266a7fd101


---
# <a name="logic-app-limits-and-configuration"></a>Limites et configuration des applications logiques

Vous trouverez ci-dessous des informations sur les limites actuelles et les détails de la configuration d’Azure Logic Apps.

## <a name="limits"></a>Limites

### <a name="http-request-limits"></a>Limites de requête HTTP

Il s’agit des limites pour un appel de requête et/ou de connecteur HTTP.

#### <a name="timeout"></a>Délai d'expiration

|Name|Limite|Remarques|
|----|----|----|
|Délai d’expiration de la demande|90 secondes|Un [modèle asynchrone](app-service-logic-create-api-app.md) ou une [boucle Until](app-service-logic-loops-and-scopes.md) peuvent compenser en fonction des besoins.|

#### <a name="message-size"></a>Taille des messages

|Name|Limite|Remarques|
|----|----|----|
|Taille des messages|50 Mo|Certains connecteurs et certaines API peuvent ne pas prendre en charge 50 Mo. |
|Limite d’évaluation des expressions|131 072 caractères|`@concat()`, `@base64()` et `string` ne peuvent pas contenir plus de caractères.|

#### <a name="retry-policy"></a>Stratégie de nouvelle tentative

|Name|Limite|Remarques|
|----|----|----|
|Nouvelles tentatives|4|Peut être configuré avec le [paramètre de stratégie de nouvelles tentatives](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Délai maximal avant nouvelle tentative|1 heure|Peut être configuré avec le [paramètre de stratégie de nouvelles tentatives](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Délai minimal avant nouvelle tentative|20 secondes|Peut être configuré avec le [paramètre de stratégie de nouvelles tentatives](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|

### <a name="run-duration-and-retention"></a>Durée d’exécution et rétention

Voici les limites pour l’exécution d’une application logique.

|Name|Limite|Remarques|
|----|----|----|
|Durée d’exécution|90 jours||
|Rétention de stockage|90 jours|À compter de l’heure de début de l’exécution.|
|Intervalle de périodicité minimal|1 seconde|| 15 secondes pour les applications logiques avec un plan App Service
|Intervalle de périodicité maximal|500 jours||


### <a name="looping-and-debatching-limits"></a>Limites de bouclage et de décomposition

Voici les limites pour l’exécution d’une application logique.

|Name|Limite|Remarques|
|----|----|----|
|Éléments ForEach|5 000|Vous pouvez utiliser [l’action de requête](../connectors/connectors-native-query.md) pour filtrer des tableaux plus grands au besoin.|
|Itérations Until|5 000||
|Éléments SplitOn|5 000||
|Parallélisme ForEach|20|Vous pouvez le définir sur une opération foreach séquentielle en ajoutant `"operationOptions": "Sequential"` à l’action `foreach`.|


### <a name="throughput-limits"></a>Limites de débit

Voici les limites pour une instance d’application logique. 

|Name|Limite|Remarques|
|----|----|----|
|Déclencheurs par seconde|100|Peuvent distribuer au besoin des flux de travail entre plusieurs applications.|

### <a name="definition-limits"></a>Limites de définition

Voici les limites pour la définition d’une application logique.

|Name|Limite|Remarques|
|----|----|----|
|Actions par flux de travail|250|Vous pouvez ajouter des flux de travail imbriqués pour l’étendre au besoin.|
|Niveaux d’imbrication d’actions autorisés|5|Vous pouvez ajouter des flux de travail imbriqués pour l’étendre au besoin.|
|Flux par région et par abonnement|1 000||
|Déclencheurs par flux de travail|10||
|Caractères max par expression|8 192||
|Taille max de `trackedProperties` en caractères|16 000|
|`action`/`trigger` |80||
|`description` |256||
|`parameters` limit|50||
|`outputs` limit|10||

### <a name="integration-account-limits"></a>Limites du compte d’intégration

Il s’agit des limites concernant les artefacts ajoutés au compte d’intégration.

|Nom|Limite|Remarques|
|----|----|----|
|Schéma|8 Mo|Vous pouvez utiliser [l’URI d’objet blob](app-service-logic-enterprise-integration-schemas.md) pour télécharger des fichiers supérieurs à 2 Mo. |
|Mappage (fichier XSLT)|2 Mo| |

### <a name="b2b-protocols-as2-x12-edifact-message-size"></a>Taille des messages des protocoles B2B (AS2, X12, EDIFACT)

Il s’agit des limites des protocoles B2B.

|Nom|Limite|Remarques|
|----|----|----|
|AS2|50 Mo|Applicable pour le décodage et l’encodage|
|X 12|50 Mo|Applicable pour le décodage et l’encodage|
|EDIFACT|50 Mo|Applicable pour le décodage et l’encodage|

## <a name="configuration"></a>Configuration

### <a name="ip-address"></a>Adresse IP

Les appels effectués à partir d’un [connecteur](../connectors/apis-list.md) proviennent de l’adresse IP spécifiée ci-dessous.

Les appels effectués directement à partir d’une application logique (c’est-à-dire via [HTTP](../connectors/connectors-native-http.md) ou [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)) peuvent provenir de l’une des [plages d’adresses IP du centre de données Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

|Région de l’application logique|Adresse IP sortante|
|-----|----|
|Est de l’Australie|40.126.251.213|
|Sud-Est de l’Australie|40.127.80.34|
|Sud du Brésil|191.232.38.129|
|Inde centrale|104.211.98.164|
|Centre des États-Unis|40.122.49.51|
|Est de l'Asie|23.99.116.181|
|Est des États-Unis|191.237.41.52|
|Est des États-Unis 2|104.208.233.100|
|Japon de l’Est|40.115.186.96|
|Ouest du Japon|40.74.130.77|
|États-Unis - partie centrale septentrionale|65.52.218.230|
|Europe du Nord|104.45.93.9|
|Centre-Sud des États-Unis|104.214.70.191|
|Asie du Sud-Est|13.76.231.68|
|Inde du Sud|104.211.227.225|
|Europe de l'Ouest|40.115.50.13|
|Inde occidentale|104.211.161.203|
|Ouest des États-Unis|104.40.51.248|


## <a name="next-steps"></a>Étapes suivantes  

- Pour vous familiariser avec les applications logiques, effectuez le didacticiel [Créer une application logique](app-service-logic-create-a-logic-app.md) .  
- [Afficher des exemples et des scénarios courants](app-service-logic-examples-and-scenarios.md)
- [Logic Apps vous permet d’automatiser vos processus métiers](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Apprenez à intégrer vos systèmes avec Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Nov16_HO3-->


