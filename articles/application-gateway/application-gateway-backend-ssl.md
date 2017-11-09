---
title: Activation du chiffrement SSL de bout en bout sur la passerelle Azure Application Gateway | Microsoft Docs
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
ms.date: 07/19/2017
ms.author: amsriva
ms.openlocfilehash: 689ee54dc1db2ea371b08270718278fd98c65bb5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>Présentation du chiffrement SSL de bout en bout sur la passerelle Application Gateway

La passerelle Application Gateway prend en charge la terminaison SSL au niveau de la passerelle, après laquelle le trafic transite généralement de façon non chiffrée vers les serveurs principaux. Grâce à cette fonctionnalité, les serveurs web ne sont plus chargés des opérations coûteuses de chiffrement et de déchiffrement. Mais pour certains clients, une communication non chiffrée vers les serveurs principaux n’est pas une option acceptable. Cette communication non chiffrée peut être due à des exigences de sécurité ou de conformité, ou au fait que l’application n’accepte qu’une connexion sécurisée. Pour de telles applications, la passerelle Application Gateway prend désormais en charge le chiffrement SSL de bout en bout.

## <a name="overview"></a>Vue d’ensemble

Le chiffrement SSL de bout en bout vous permet de transmettre en toute sécurité des données sensibles au serveur back-end, tout en continuant de bénéficier des avantages des fonctionnalités d’équilibrage de charge de couche 7 fournies par la passerelle Application Gateway. Il s’agit notamment de la fonctionnalité d’affinité basée sur les cookies, du routage basé sur l’URL, de la prise en charge du routage basé sur des sites ou de la possibilité d’injecter des en-têtes X-Forwarded.

Lorsqu’elle est configurée avec un mode de communication SSL de bout en bout, la passerelle Application Gateway ferme les sessions SSL au niveau de la passerelle et déchiffre le trafic de l’utilisateur. Il applique ensuite les règles configurées pour sélectionner une instance de pool principal appropriée vers laquelle acheminer le trafic. La passerelle Application Gateway établit ensuite une nouvelle connexion SSL vers le serveur back-end, puis chiffre à nouveau les données à l’aide du certificat de clé publique de ce serveur avant de transmettre la requête au back-end. Pour activer le chiffrement SSL de bout en bout, définissez le paramètre de protocole BackendHTTPSetting sur HTTPS, qui est ensuite appliqué à un pool back-end. Chaque serveur principal du pool principal pour lequel un chiffrement SSL de bout en bout a été activé doit être configuré avec un certificat afin de permettre une communication sécurisée.

![scénario ssl de bout en bout][1]

Dans cet exemple, les demandes utilisant TLS1.2 sont acheminées vers les serveurs principaux dans Pool1 à l’aide du chiffrement SSL de bout en bout.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Chiffrement SSL de bout en bout et liste blanche de certificats

La passerelle Application Gateway communique uniquement avec les instances de serveur principal identifiées dont le certificat figure sur la liste approuvée par la passerelle Application Gateway. Pour approuver des certificats, vous devez télécharger la clé publique des certificats du serveur principal pour la passerelle Application Gateway (non le certificat racine). Seules les connexions aux serveurs principaux connus et sur liste blanche sont alors autorisées. Les autres serveurs principaux renvoient une erreur de passerelle. Les certificats auto-signés sont uniquement destinés à des fins de test et ne sont pas recommandés pour les charges de travail de production. Pour être utilisables, ces certificats doivent figurer dans la liste approuvée par la passerelle Application Gateway, comme décrit dans les étapes précédentes.

## <a name="next-steps"></a>Étapes suivantes

Après avoir découvert le chiffrement SSL de bout en bout, consultez l’article indiquant comment [activer le chiffrement SSL de bout en bout sur la passerelle Application Gateway](application-gateway-end-to-end-ssl-powershell.md) pour créer une passerelle d’application à l’aide du chiffrement SSL de bout en bout.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png
