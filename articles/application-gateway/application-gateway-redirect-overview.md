---
title: "Vue d’ensemble de la redirection pour Azure Application Gateway | Microsoft Docs"
description: "En savoir plus sur la capacité de redirection dans Azure Application Gateway"
services: application-gateway
documentationcenter: na
author: amsriva
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2017
ms.author: amsriva
ms.openlocfilehash: ea9ae8373ff67bf9557b06bbc8a4b0d82a03e2d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="application-gateway-redirect-overview"></a>Vue d’ensemble de la redirection Application Gateway

Un scénario courant pour de nombreuses applications web consiste à prendre en charge la redirection automatique de HTTP vers HTTPS pour vous assurer que toutes les communications entre l’application et ses utilisateurs auront lieu via un chemin d’accès chiffré. Dans le passé, les utilisateurs utilisaient des techniques telles que la création d’un pool backend dédié dont le seul objectif consistait à rediriger les demandes qu’il recevait sur HTTP vers HTTPS.  La passerelle d’application prend désormais en charge la possibilité de rediriger le trafic sur Application Gateway. Cela simplifie la configuration de l’application, optimise l’utilisation des ressources et prend en charge de nouveaux scénarios de redirection, notamment la redirection globale et basée sur le chemin d’accès. La prise en charge de la redirection Application Gateway n’est pas limitée à la seule redirection HTTP -> HTTPS. Il s’agit d’un mécanisme de redirection générique, qui permet la redirection du trafic reçu au niveau d’un écouteur vers un autre écouteur sur Application Gateway. Il prend également en charge la redirection vers un site externe. La prise en charge de la redirection Application Gateway offre les fonctionnalités suivantes :

1. Redirection globale à partir d’un écouteur vers un autre écouteur sur la passerelle. Cela permet la redirection HTTP vers HTTPS sur un site.
2. Redirection basée sur un chemin d’accès. Ce type de redirection permet la redirection HTTP vers HTTPS uniquement sur une zone de site spécifique, par exemple une zone de panier d’achat indiquée par /cart/*.
3. Redirection vers un site externe.

![redirection](./media/application-gateway-redirect-overview/redirect.png)

Avec cette modification, les clients doivent créer un nouvel objet de configuration de redirection, qui spécifie l’écouteur cible ou le site externe vers lequel la redirection est souhaitée. L’élément de configuration prend également en charge des options qui permettent d’ajouter le chemin d’URI et la chaîne de requête à l’URL redirigée. Les clients peuvent également choisir si la redirection est temporaire (code d’état HTTP 302) ou permanente (code d’état HTTP 301). Une fois créée, cette configuration de redirection est attachée à l’écouteur source via une nouvelle règle. Lorsque vous utilisez une règle de base, la configuration de redirection est associée à un écouteur source et correspond à une redirection globale. Lorsqu’une règle basée sur le chemin d’accès est utilisée, la configuration de redirection est définie sur le mappage de chemin d’URL et, par conséquent, s’applique uniquement à la zone de chemin d’accès spécifique d’un site.

### <a name="next-steps"></a>Étapes suivantes

[Configurer la redirection d’URL sur une passerelle d’application](application-gateway-configure-redirect-powershell.md)
