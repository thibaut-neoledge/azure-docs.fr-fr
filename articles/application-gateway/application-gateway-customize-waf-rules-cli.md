---
title: "Personnaliser les règles de pare-feu d’applications web dans Azure Application Gateway - Azure CLI 2.0 | Microsoft Docs"
description: "Cette page fournit des informations sur la personnalisation des règles de pare-feu d’applications web dans Application Gateway avec Azure CLI 2.0."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: cfc01caf5c8ca11f790a6640ec57e3d14c1b7767
ms.contentlocale: fr-fr
ms.lasthandoff: 08/04/2017

---

# <a name="customize-web-application-firewall-rules-through-the-azure-cli-20"></a>Personnaliser les règles de pare-feu d’applications web par le biais d’Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

Le pare-feu d’applications web Application Gateway fournit une protection pour les applications web. Ces protections sont assurées par des jeux de règles OWASP CRS. Certaines règles peuvent entraîner des faux positifs et bloquer le trafic réel.  Par conséquent, la passerelle d’application permet de personnaliser des règles et des groupes de règles sur une passerelle d’application reposant sur un pare-feu d’applications web. Pour plus d’informations sur les règles et groupes de règles spécifiques, rendez-vous sur la page [web application firewall CRS Rule groups and rules](application-gateway-crs-rulegroups-rules.md) (Règles et groupes de règles CRS de pare-feu d’applications web).

## <a name="view-rule-groups-and-rules"></a>Afficher les règles et groupes de règles

Voici des exemples montrant comment afficher les règles et les groupes de règles qui peuvent être configurés sur une passerelle d’application avec WAF activé.

### <a name="view-rule-groups"></a>Afficher les groupes de règles

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

Voici un extrait de réponse issu de l’exemple précédent :

```
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>Afficher les règles dans un groupe de règles

L’exemple suivant montre comment afficher les règles dans un groupe de règles spécifique.

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

Voici un extrait de réponse issu de l’exemple précédent :

```
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>Désactiver les règles

L’exemple suivant montre comment désactiver les règles `910018` et `910017` sur une passerelle d’application.

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré les règles désactivées, découvrez comment afficher les journaux WAF en vous rendant sur [Application Gateway Diagnostics](application-gateway-diagnostics.md#diagnostic-logging) (Diagnostics de la passerelle Application Gateway)

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
