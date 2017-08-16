---
title: "Personnaliser les règles de pare-feu d’applications web dans Azure Application Gateway -PowerShell | Microsoft Docs"
description: "Cette page fournit des informations sur la personnalisation des règles de pare-feu d’applications web dans Application Gateway avec PowerShell."
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
ms.openlocfilehash: 7ed79af5654939c86ca9449be2a59aa0be1136dc
ms.contentlocale: fr-fr
ms.lasthandoff: 08/04/2017

---

# <a name="customize-web-application-firewall-rules-through-powershell"></a>Personnaliser les règles de pare-feu d’applications web par le biais de PowerShell

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

Le pare-feu d’applications web Application Gateway fournit une protection pour les applications web. Ces protections sont assurées par des jeux de règles OWASP CRS. Certaines règles peuvent entraîner des faux positifs et bloquer le trafic réel.  Par conséquent, la passerelle d’application permet de personnaliser des règles et des groupes de règles sur une passerelle d’application reposant sur un pare-feu d’applications web. Pour plus d’informations sur les règles et groupes de règles spécifiques, rendez-vous sur la page [web application firewall CRS Rule groups and rules](application-gateway-crs-rulegroups-rules.md) (Règles et groupes de règles CRS de pare-feu d’applications web).

## <a name="view-rule-groups-and-rules"></a>Afficher les règles et groupes de règles

Voici des exemples montrant comment afficher les règles et les groupes de règles qui peuvent être configurés sur une passerelle d’application avec WAF activé.

### <a name="view-rule-groups"></a>Afficher les groupes de règles

```powershell
Get-AzureRmApplicationGatewayAvailableWafRuleSets
```

Voici un extrait de réponse issu de l’exemple précédent :

```
OWASP (Ver. 3.0):

    REQUEST-910-IP-REPUTATION:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            910011     Rule 910011
            910012     Rule 910012
            ...        ...

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            ...        ...

OWASP (Ver. 2.2.9):

    crs_20_protocol_violations:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            960911     Invalid HTTP Request Line
            981227     Apache Error: Invalid URI in Request.
            960000     Attempted multipart/form-data bypass
            ...        ...
```

## <a name="disable-rules"></a>Désactiver les règles

L’exemple suivant montre comment désactiver les règles `910018` et `910017` sur une passerelle d’application.

```azurecli
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré les règles désactivées, découvrez comment afficher les journaux WAF en vous rendant sur [Application Gateway Diagnostics](application-gateway-diagnostics.md#diagnostic-logging) (Diagnostics de la passerelle Application Gateway)

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
