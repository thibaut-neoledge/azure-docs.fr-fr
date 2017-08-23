---
title: "Personnaliser les règles de pare-feu d’applications web dans Azure Application Gateway Azure | Microsoft Docs"
description: "Cette page fournit des informations sur la personnalisation des règles de pare-feu d’applications web dans Application Gateway avec le portail."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: c3e93614f012eecff0e88f5f2ad13db199406f4a
ms.contentlocale: fr-fr
ms.lasthandoff: 08/04/2017

---

# <a name="customize-web-application-firewall-rules-through-the-portal"></a>Personnaliser les règles de pare-feu d’applications web via le portail

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

Le pare-feu d’applications web Application Gateway fournit une protection pour les applications web. Ces protections sont assurées par des jeux de règles OWASP CRS. Certaines règles peuvent entraîner des faux positifs et bloquer le trafic réel.  La passerelle d’application permet donc de personnaliser des règles et des groupes de règles sur une passerelle d’application reposant sur un pare-feu d’applications web. Pour plus d’informations sur les règles et groupes de règles spécifiques, rendez-vous sur la page [web application firewall CRS Rule groups and rules](application-gateway-crs-rulegroups-rules.md) (Règles et groupes de règles CRS de pare-feu d’applications web).

>[!NOTE]
> Si votre passerelle Application Gateway n’utilise pas la couche WAF, l’option de mise à niveau de la passerelle Application Gateway vers la couche WAF s’affiche comme indiqué dans l’image suivante :

![activer WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Afficher les règles et groupes de règles

Accédez à une passerelle Application Gateway et sélectionnez le **pare-feu d’applications web**.  Cliquez sur **Configuration de règle avancée**.  Un tableau s’affiche sur la page de tous les groupes de règles fournis avec l’ensemble de règles choisi.

![configurer des règles désactivées][1]

## <a name="search-for-rules-to-disable"></a>Rechercher des règles à désactiver

Le panneau des paramètres de pare-feu d’applications web permet de filtrer les règles par une recherche de texte. Le résultat affiche uniquement les groupes de règles et les règles contenant le texte recherché.

![rechercher des règles][2]

## <a name="disable-rule-groups-and-rules"></a>Désactiver les règles et les groupes de règles

Lors de la désactivation des règles, vous pouvez désactiver un groupe de règles entier, ou des règles spécifiques sous un ou plusieurs groupes de règles.  Une fois que vous avez désélectionné les règles à désactiver, cliquez sur **Enregistrer**.  Cette étape permet d’enregistrer les modifications apportées à la passerelle d’application.

![enregistrer les modifications][3]

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré les règles désactivées, découvrez comment afficher les journaux WAF en vous rendant sur [Application Gateway Diagnostics](application-gateway-diagnostics.md#diagnostic-logging) (Diagnostics de la passerelle Application Gateway)

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png

