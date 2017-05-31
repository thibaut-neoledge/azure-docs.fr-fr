---
title: Limites et configuration des applications logiques | Microsoft Docs
description: "Vue d’ensemble des limites de service et des valeurs de configuration disponibles pour Logic Apps."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: LADocs; jehollan
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 859f9a1ca9dbe166fd514c8eba868b466dbc4d2c
ms.contentlocale: fr-fr
ms.lasthandoff: 05/18/2017


---
# <a name="logic-app-limits-and-configuration"></a>Limites et configuration des applications logiques

Vous trouverez ci-dessous des informations sur les limites actuelles et les détails de la configuration d’Azure Logic Apps.

## <a name="limits"></a>Limites

### <a name="http-request-limits"></a>Limites de requête HTTP

Il s’agit des limites pour un appel de requête et/ou de connecteur HTTP.

#### <a name="timeout"></a>Délai d'expiration

|Name|Limite|Remarques|
|----|----|----|
|Délai d’expiration de la demande|120 secondes|Un [modèle asynchrone](../logic-apps/logic-apps-create-api-app.md) ou une [boucle Until](logic-apps-loops-and-scopes.md) peuvent compenser en fonction des besoins.|

#### <a name="message-size"></a>Taille des messages

|Name|Limite|Remarques|
|----|----|----|
|Taille des messages|100 Mo|Certains connecteurs et certaines API peuvent ne pas prendre en charge 100 Mo |
|Limite d’évaluation des expressions|131 072 caractères|`@concat()`, `@base64()` et `string` ne peuvent pas contenir plus de caractères.|

#### <a name="retry-policy"></a>Stratégie de nouvelle tentative

|Name|Limite|Remarques|
|----|----|----|
|Nouvelles tentatives|4|Peut être configuré avec le [paramètre de stratégie de nouvelles tentatives](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Délai maximal avant nouvelle tentative|1 heure|Peut être configuré avec le [paramètre de stratégie de nouvelles tentatives](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Délai minimal avant nouvelle tentative|5 secondes|Peut être configuré avec le [paramètre de stratégie de nouvelles tentatives](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|

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
|Éléments ForEach|100 000|Vous pouvez utiliser [l’action de requête](../connectors/connectors-native-query.md) pour filtrer des tableaux plus grands au besoin.|
|Itérations Until|5 000||
|Éléments SplitOn|100 000||
|Parallélisme ForEach|20|Vous pouvez le définir sur une opération foreach séquentielle en ajoutant `"operationOptions": "Sequential"` à l’action `foreach`.|


### <a name="throughput-limits"></a>Limites de débit

Voici les limites pour une instance d’application logique. 

|Name|Limite|Remarques|
|----|----|----|
|Exécutions d’actions par tranche de 5 minutes |100 000|Possibilité de distribution au besoin des charges de travail entre plusieurs applications|
|Appels sortants simultanés des actions |~2,500|Diminuer le nombre de demandes simultanées ou réduire la durée en fonction des besoins|
|Appels entrants simultanés de point de terminaison de runtime |~1,000|Diminuer le nombre de demandes simultanées ou réduire la durée en fonction des besoins|
|Appels de lecture de point de terminaison de runtime toutes les 5 minutes |60 000|Possibilité de distribution au besoin des charges de travail entre plusieurs applications|
|Appels d’appel de point de terminaison de runtime toutes les 5 minutes |45,000|Possibilité de distribution au besoin des charges de travail entre plusieurs applications|

Si vous pensez dépasser cette limite dans le cadre du traitement normal ou souhaitez exécuter des tests de charge susceptibles de dépasser cette limite pour une période donnée, [contactez-nous](mailto://logicappsemail@microsoft.com) afin que nous puissions répondre à vos besoins spécifiques.

### <a name="definition-limits"></a>Limites de définition

Voici les limites pour la définition d’une application logique.

|Name|Limite|Remarques|
|----|----|----|
|Actions par flux de travail|250|Vous pouvez ajouter des flux de travail imbriqués pour l’étendre au besoin.|
|Niveaux d’imbrication d’actions autorisés|5|Vous pouvez ajouter des flux de travail imbriqués pour l’étendre au besoin.|
|Flux de travail par région et par abonnement|1 000||
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
|Schéma|8 Mo|Vous pouvez utiliser [l’URI d’objet blob](logic-apps-enterprise-integration-schemas.md) pour télécharger des fichiers supérieurs à 2 Mo. |
|Mappage (fichier XSLT)|2 Mo| |
|Appels de lecture de point de terminaison de runtime toutes les 5 minutes |60 000|Possibilité de distribution au besoin des charges de travail entre plusieurs comptes|
|Appels d’appel de point de terminaison de runtime toutes les 5 minutes |45,000|Possibilité de distribution au besoin des charges de travail entre plusieurs comptes|
|Appels de suivi de point de terminaison runtime par 5 minutes |45,000|Possibilité de distribution au besoin des charges de travail entre plusieurs comptes|
|Appels simultanés de blocage de point de terminaison de runtime |~1,000|Diminuer le nombre de demandes simultanées ou réduire la durée en fonction des besoins|

### <a name="b2b-protocols-as2-x12-edifact-message-size"></a>Taille des messages des protocoles B2B (AS2, X12, EDIFACT)

Il s’agit des limites des protocoles B2B.

|Nom|Limite|Remarques|
|----|----|----|
|AS2|50 Mo|Applicable pour le décodage et l’encodage|
|X 12|50 Mo|Applicable pour le décodage et l’encodage|
|EDIFACT|50 Mo|Applicable pour le décodage et l’encodage|

## <a name="configuration"></a>Configuration

### <a name="ip-address"></a>Adresse IP

#### <a name="logic-app-service"></a>Logic App Service

Les appels effectués directement à partir d’une application logique (c'est-à-dire, via [HTTP](../connectors/connectors-native-http.md) ou [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)) ou d’autres demandes HTTP proviendront de l’adresse IP spécifiée ci-dessous :

|Région de l’application logique|Adresse IP sortante|
|-----|----|
|Est de l’Australie|13.75.153.66, 104.210.89.222, 104.210.89.244, 13.75.149.4, 104.210.91.55, 104.210.90.241|
|Sud-Est de l’Australie|13.73.115.153, 40.115.78.70, 40.115.78.237, 13.73.114.207, 13.77.3.139, 13.70.159.205|
|Sud du Brésil|191.235.86.199, 191.235.95.229, 191.235.94.220, 191.235.82.221, 191.235.91.7, 191.234.182.26|
|Centre du Canada|52.233.29.92,52.228.39.241,52.228.39.244|
|Est du Canada|52.232.128.155,52.229.120.45,52.229.126.25|
|Inde centrale|52.172.157.194, 52.172.184.192, 52.172.191.194, 52.172.154.168, 52.172.186.159, 52.172.185.79|
|Centre des États-Unis|13.67.236.76, 40.77.111.254, 40.77.31.87, 13.67.236.125, 104.208.25.27, 40.122.170.198|
|Est de l'Asie|168.63.200.173, 13.75.89.159, 23.97.68.172, 13.75.94.173, 40.83.127.19, 52.175.33.254|
|Est des États-Unis|137.135.106.54, 40.117.99.79, 40.117.100.228, 13.92.98.111, 40.121.91.41, 40.114.82.191|
|Est des États-Unis 2|40.84.25.234, 40.79.44.7, 40.84.59.136, 40.84.30.147, 104.208.155.200, 104.208.158.174|
|Est du Japon|13.71.146.140, 13.78.84.187, 13.78.62.130, 13.71.158.3, 13.73.4.207, 13.71.158.120|
|Ouest du Japon|40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.140.4, 104.214.137.243, 138.91.26.45|
|États-Unis - partie centrale septentrionale|168.62.249.81, 157.56.12.202, 65.52.211.164, 168.62.248.37, 157.55.210.61, 157.55.212.238|
|Europe du Nord|13.79.173.49, 52.169.218.253, 52.169.220.174, 40.113.12.95, 52.178.165.215, 52.178.166.21|
|Centre-Sud des États-Unis|13.65.98.39, 13.84.41.46, 13.84.43.45, 104.210.144.48, 13.65.82.17, 13.66.52.232|
|Asie du Sud-Est|52.163.93.214, 52.187.65.81, 52.187.65.155, 13.76.133.155, 52.163.228.93, 52.163.230.166|
|Inde du Sud|52.172.9.47, 52.172.49.43, 52.172.51.140, 52.172.50.24, 52.172.55.231, 52.172.52.0|
|Europe de l'Ouest|13.95.155.53, 52.174.54.218, 52.174.49.6, 40.68.222.65, 40.68.209.23, 13.95.147.65|
|Inde occidentale|104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.164.80, 104.211.162.205, 104.211.164.136|
|Ouest des États-Unis|52.160.90.237, 138.91.188.137, 13.91.252.184, 52.160.92.112, 40.118.244.241, 40.118.241.243|

#### <a name="connectors"></a>Connecteurs

Les appels effectués à partir d’un [connecteur](../connectors/apis-list.md) proviennent de l’adresse IP spécifiée ci-dessous :

|Région de l’application logique|Adresse IP sortante|
|-----|----|
|Est de l’Australie|40.126.251.213|
|Sud-Est de l’Australie|40.127.80.34|
|Sud du Brésil|191.232.38.129|
|Centre du Canada|52.233.31.197,52.228.42.205,52.228.33.76,52.228.34.13|
|Est du Canada|52.229.123.98,52.229.120.178,52.229.126.202,52.229.120.52|
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

- Pour vous familiariser avec les applications logiques, effectuez le didacticiel [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md) .  
- [Afficher des exemples et des scénarios courants](../logic-apps/logic-apps-examples-and-scenarios.md)
- [Logic Apps vous permet d’automatiser vos processus métiers](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Apprenez à intégrer vos systèmes avec Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)

