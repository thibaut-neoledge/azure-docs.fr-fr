---
title: Prise en charge de WebSocket pour la passerelle Application Gateway | Microsoft Docs
description: "Cette page fournit une vue d’ensemble de la prise en charge de WebSocket pour la passerelle Application Gateway."
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 8968dac1-e9bc-4fa1-8415-96decacab83f
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/16/2016
ms.author: amsriva
translationtype: Human Translation
ms.sourcegitcommit: 119275f335344858cd20b6a17ef87e3ef32b6e12
ms.openlocfilehash: 5af1b1bacce2fe189a7b557527520795ed622b54
ms.lasthandoff: 03/01/2017


---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Vue d’ensemble de la prise en charge de WebSocket dans Application Gateway

Application Gateway prend en charge WebSocket de manière native, quelle que soit la taille de la passerelle. Il n’existe aucun paramètre configurable par l’utilisateur permettant d’activer ou de désactiver de manière sélective la prise en charge de WebSocket. Vous pouvez continuer à utiliser un élément HTTPListener standard sur le port 80/443 pour recevoir le trafic WebSocket. Le trafic WebSocket est ensuite dirigé vers le serveur principal compatible WebSocket à l’aide du pool principal approprié tel que spécifié dans les règles Application Gateway. Le protocole WebSocket, standardisé dans la [RFC6455](https://tools.ietf.org/html/rfc6455) , permet une communication bidirectionnelle entre le serveur et le client via une connexion TCP de longue durée. Cette fonctionnalité permet une communication plus interactive entre le serveur web et le client, qui peut être bidirectionnelle sans nécessiter d’interrogation, comme c’est le cas pour les mises en œuvre basées sur le protocole HTTP.  Contrairement au protocole HTTP, WebSocket engendre une faible surcharge et peut réutiliser la même connexion TCP pour plusieurs demandes/réponses, ce qui entraîne une utilisation plus efficace des ressources. Les protocoles WebSocket sont conçus pour fonctionner sur les ports HTTP traditionnels (80 et 443).

Le serveur principal doit répondre aux sondes de la passerelle Application Gateway, qui sont décrites dans la section [Vue d’ensemble de la sonde d’intégrité](application-gateway-probe-overview.md) . Les sondes d’intégrité Application Gateway sont uniquement pour HTTP/HTTPS, ce qui implique que chaque serveur principal doit répondre aux sondes HTTP pour que la passerelle Application Gateway achemine le trafic WebSocket vers le serveur.

## <a name="listener-configuration-element"></a>Élément de configuration d’écouteur

L’élément HTTPListener existant peut servir à prendre en charge WebSocket. Voici un extrait de l’élément HttpListeners provenant d’un exemple de fichier de modèle. Vous auriez besoin d’écouteurs HTTP et HTTPS pour prendre en charge WebSocket et sécuriser le trafic WebSocket. De même, vous pouvez utiliser le [portail](application-gateway-create-gateway-portal.md) ou [PowerShell](application-gateway-create-gateway-arm.md) afin de créer une passerelle Application Gateway avec des écouteurs sur le port 80/443 pour prendre en charge le trafic WebSocket.

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Configuration des règles de routage, BackendHttpSetting et BackendAddressPool

BackendAddressPool permet de définir un pool principal avec des serveurs compatibles WebSocket. BackendHttpSetting doit être défini uniquement avec le port 80 ou 443 du serveur principal. Les propriétés de l’affinité basée sur les cookies et de requestTimeouts ne sont pas pertinentes pour le trafic WebSocket. Aucune modification n’est requise dans la règle de routage. La règle de routage « De base » doit continuer à être utilisée pour lier l’écouteur approprié au pool d’adresses principal correspondant. 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>Serveur principal compatible WebSocket

Votre serveur principal doit exécuter un serveur web HTTP/HTTPS sur le port configuré (généralement 80/443) pour que WebSocket fonctionne. Cela est nécessaire, car le protocole WebSocket impose que la négociation initiale soit HTTP avec mise à niveau vers le protocole WebSocket comme champ d’en-tête.

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

Autre raison : la sonde d’intégrité principale d’Application Gateway prend en charge uniquement les protocoles HTTP/HTTPS. Si le serveur principal ne répond pas aux sondes HTTP/HTTPS, il est exclu du pool principal et aucune demande, y compris les demandes WebSocket, n’atteint ce backend.

## <a name="next-steps"></a>Étapes suivantes

Après avoir découvert la prise en charge de WebSocket, consultez [Créer une passerelle Application Gateway](application-gateway-create-gateway.md) pour commencer à utiliser une application web compatible avec WebSocket.


