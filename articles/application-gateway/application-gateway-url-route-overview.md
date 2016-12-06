---
title: "Présentation du routage de contenu basé sur URL | Microsoft Docs"
description: "Cette page fournit une présentation du routage de contenu basé sur l’URL Application Gateway, de la configuration de l’élément UrlPathMap et de la règle PathBasedRouting."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 4409159b-e22d-4c9a-a103-f5d32465d163
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 3a8e5583f213c6d35f8e41dd31fe2ccad7389977
ms.openlocfilehash: 34c699bb39413ec4fe72dd0c3f4a6ce568472eaa


---
# <a name="url-path-based-routing-overview"></a>Présentation du routage basé sur le chemin d’accès de l’URL
Le routage basé sur le chemin d’accès de l’URL vous permet d’acheminer le trafic vers des pools de serveurs principaux en fonction des chemins d’accès de l’URL de la demande. L’un des scénarios consiste à acheminer les demandes pour différents types de contenu vers des pools de serveurs principaux différents.
Dans l’exemple suivant, Application Gateway achemine le trafic pour contoso.com à partir de trois pools de serveurs principaux, par exemple : VideoServerPool, ImageServerPool et DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Les demandes pour http://contoso.com/video* sont acheminées vers VideoServerPool et celles pour http://contoso.com/images* vers ImageServerPool. DefaultServerPool est sélectionné si aucun des modèles de chemin d’accès ne correspond.

## <a name="urlpathmap-configuration-element"></a>Élément de configuration UrlPathMap
L’élément UrlPathMap est utilisé pour spécifier les modèles de chemin d’accès vers les mappages de pools de serveurs principaux. L’exemple de code suivant est un extrait de l’élément urlPathMap issu du fichier de modèle.

```json
"urlPathMaps": [
{
"name": "<urlPathMapName>",
"id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/ urlPathMaps/<urlPathMapName>",
"properties": {
    "defaultBackendAddressPool": {
        "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendAddressPools/<poolName>"
    },
    "defaultBackendHttpSettings": {
        "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendHttpSettingsList/<settingsName>"
    },
    "pathRules": [
        {
            "paths": [
                <pathPattern>
            ],
            "backendAddressPool": {
                "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendAddressPools/<poolName2>"
            },
            "backendHttpsettings": {
                "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendHttpsettingsList/<settingsName2>"
            },

        },

    ],

}
}
]
```

> [!NOTE]
> PathPattern : ce paramètre est une liste de modèles de chemin d’accès à utiliser pour la correspondance. Chaque modèle doit commencer par le signe « * » et le seul endroit où un astérisque * est autorisé est à la fin après un signe « / ». La chaîne transmise à l’outil de correspondance de chemin d’accès n’inclut pas de texte après le premier signe ? ou #. De plus, ces caractères ne sont pas autorisés ici. 
> 
> 

Pour plus d’informations, vous pouvez consulter un [modèle Resource Manager utilisant le routage basé sur URL](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing) .

## <a name="pathbasedrouting-rule"></a>Règle PathBasedRouting
La règle RequestRoutingRule de type PathBasedRouting est utilisée pour lier un écouteur à un élément urlPathMap. Toutes les demandes qui sont reçues par cet écouteur sont acheminées en fonction de la stratégie spécifiée dans l’élément urlPathMap.
Exemple de la règle PathBasedRouting :

```json
"requestRoutingRules": [
    {

"name": "<ruleName>",
"id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/requestRoutingRules/<ruleName>",
"properties": {
    "ruleType": "PathBasedRouting",
    "httpListener": {
        "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/httpListeners/<listenerName>"
    },
    "urlPathMap": {
        "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/ urlPathMaps/<urlPathMapName>"
    },

}
    }
]
```

## <a name="next-steps"></a>Étapes suivantes
Après vous être familiarisé avec le routage de contenu basé sur URL, accédez à la section [Créer une passerelle d’application à l’aide du routage basé sur URL](application-gateway-create-url-route-portal.md) pour créer une passerelle d’application avec les règles de routage URL.




<!--HONumber=Nov16_HO2-->


