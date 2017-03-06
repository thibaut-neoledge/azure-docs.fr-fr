---
title: "Activation d’une stratégie SSL de bout en bout sur la passerelle Application Gateway | Microsoft Docs"
description: "Cette page fournit une vue d’ensemble de la prise en charge du chiffrement SSL de bout en bout pour la passerelle Application Gateway."
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 3976399b-25ad-45eb-8eb3-fdb736a598c5
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: amsriva
translationtype: Human Translation
ms.sourcegitcommit: 5009b13cec57e6974f71610c84fdaad837085df0
ms.openlocfilehash: 5f81d8146f8000e73a2eb578ff2371a62c8875e9
ms.lasthandoff: 03/01/2017


---
# <a name="overview-of-end-to-end-ssl-and-ssl-policy-on-application-gateway"></a>Présentation de la stratégie SSL et SSL de bout en bout sur la passerelle Application Gateway

La passerelle Application Gateway prend en charge la terminaison SSL au niveau de la passerelle, après laquelle le trafic transite généralement de façon non chiffrée vers les serveurs principaux. Grâce à cette fonctionnalité, les serveurs web ne sont plus chargés des opérations coûteuses de chiffrement/déchiffrement. Mais pour certains clients, une communication non chiffrée vers les serveurs principaux n’est pas une option acceptable. Cette communication non chiffrée peut être due à des exigences de sécurité ou de conformité, ou au fait que l’application n’accepte que des connexions sécurisées. Pour de telles applications, la passerelle Application Gateway prend désormais en charge le chiffrement SSL de bout en bout.

## <a name="overview"></a>Vue d'ensemble

Le chiffrement SSL de bout en bout vous permet de transmettre en toute sécurité des données sensibles au serveur principal tout en continuant de bénéficier des avantages des fonctionnalités d’équilibrage de charge de couche 7 fournies par la passerelle Application Gateway. Il s’agit notamment de la fonctionnalité d’affinité des cookies, du routage basé sur l’URL, de la prise en charge du routage basé sur des sites ou de la possibilité d’injecter des en-têtes X-Forwarded.

Configuré avec un mode de communication SSL de bout en bout, la passerelle Application Gateway ferme les sessions SSL de l’utilisateur au niveau de la passerelle et déchiffre le trafic de l’utilisateur. Il applique ensuite les règles configurées pour sélectionner une instance de pool principal appropriée vers laquelle acheminer le trafic. La passerelle Application Gateway établit ensuite une nouvelle connexion SSL vers le serveur principal, puis chiffre à nouveau les données à l’aide du certificat de clé publique du serveur principal avant de transmettre la requête au serveur principal. Le chiffrement SSL de bout en bout est activé en définissant le paramètre de protocole BackendHTTPSetting sur Https, qui est ensuite appliqué à un pool principal. Chaque serveur principal du pool principal pour lequel un chiffrement SSL de bout en bout a été activé doit être configuré avec un certificat afin de permettre une communication sécurisée.

![scénario ssl de bout en bout][1]

Dans cet exemple, les demandes utilisant TLS1.2 sont acheminées vers les serveurs principaux dans Pool1 à l’aide du chiffrement SSL de bout en bout.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Chiffrement SSL de bout en bout et liste blanche de certificats

La passerelle Application Gateway communique uniquement avec les instances de serveur principal identifiées dont le certificat figure sur la liste approuvée par la passerelle Application Gateway. Pour approuver des certificats, vous devez télécharger la clé publique des certificats du serveur principal pour la passerelle Application Gateway (non le certificat racine). Seules les connexions aux serveurs principaux connus et sur liste blanche sont alors autorisées. Les autres serveurs principaux renvoient une erreur de passerelle. Les certificats auto-signés sont uniquement destinés à des fins de test et ne sont pas recommandés pour les charges de travail de production. Pour être utilisables, ces certificats doivent figurer dans la liste approuvée par la passerelle Application Gateway, comme décrit dans les étapes précédentes.

## <a name="application-gateway-ssl-policy"></a>Stratégie SSL de la passerelle Application Gateway

La passerelle Application Gateway prend en charge les stratégies de négociation SSL configurables par l’utilisateur, offrant aux clients un meilleur contrôle des connexions SSL au niveau de la passerelle Application Gateway.

1. Les protocoles SSL 2.0 et 3.0 sont désactivés par défaut pour toutes les passerelles Application Gateway. Ils ne sont pas configurables du tout.
2. La définition de la stratégie SSL vous permet de désactiver les trois protocoles suivants : **TLSv1\_0**, **TLSv1\_1**, **TLSv1\_2**.
3. Si aucune stratégie SSL n’est définie, les trois protocoles (TLSv1\_0, TLSv1\_1, TLSv1_2) sont activés.

## <a name="next-steps"></a>Étapes suivantes

Après avoir découvert le chiffrement SSL de bout en bout et la stratégie SSL, consultez l’article indiquant comment [activer le chiffrement SSL de bout en bout sur la passerelle Application Gateway](application-gateway-end-to-end-ssl-powershell.md) pour créer une passerelle d’application à l’aide du chiffrement SSL de bout en bout.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png

