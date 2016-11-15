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
ms.workload: infrastructure-services
ms.date: 11/10/2016
ms.author: amsriva
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9642ea16bff4b0cd4bf3e88d7530765d4c7bfbc5


---
# <a name="enabling-ssl-policy-and-end-to-end-ssl-on-application-gateway"></a>Activation d’une stratégie SSL de bout en bout sur la passerelle Application Gateway
## <a name="overview"></a>Vue d'ensemble
La passerelle Application Gateway prend en charge la terminaison SSL au niveau de la passerelle, après laquelle le trafic transite généralement de façon non chiffrée vers les serveurs principaux. Les serveurs web ne sont ainsi plus chargés des opérations coûteuses de chiffrement/déchiffrement. Mais pour certains clients, une communication non chiffrée vers les serveurs principaux n’est pas une option acceptable. Cela peut être dû à des d’exigences de sécurité et de conformité, ou au fait que l’application n’accepte que des connexions sécurisées. Pour de telles applications, la passerelle Application Gateway prend désormais en charge le chiffrement SSL de bout en bout.

Le chiffrement SSL de bout en bout vous permet de transmettre en toute sécurité des données sensibles au serveur principal en profitant toujours des avantages des fonctions d’équilibrage de charge de couche 7 fournies par la passerelle Application Gateway, notamment l’affinité des cookies, le routage basé sur l’URL, la prise en charge du routage basé sur des sites ou la possibilité d’injecter des en-têtes X-Forwarded.

Configuré avec un mode de communication SSL de bout en bout, la passerelle Application Gateway ferme les sessions SSL de l’utilisateur au niveau de la passerelle et déchiffre le trafic de l’utilisateur. Il applique ensuite les règles configurées pour sélectionner une instance de pool principal appropriée vers laquelle acheminer le trafic. La passerelle Application Gateway établit ensuite une nouvelle connexion SSL vers le serveur principal, puis chiffre à nouveau les données à l’aide du certificat de clé publique du serveur principal avant de transmettre la requête au serveur principal. Le chiffrement SSL de bout en bout est activé en définissant le paramètre de protocole BackendHTTPSetting sur Https, qui est ensuite appliqué à un pool principal. Chaque serveur principal du pool principal pour lequel un chiffrement SSL de bout en bout a été activé doit être configuré avec un certificat afin de permettre une communication sécurisée.

![scénario ssl de bout en bout][1]

Dans cet exemple, les demandes utilisant TLS1.2 sont acheminées vers les serveurs principaux dans Pool1 à l’aide du chiffrement SSL de bout en bout.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Chiffrement SSL de bout en bout et liste blanche de certificats
La passerelle Application Gateway communique uniquement avec les instances de serveur principal identifiées dont le certificat figure sur la liste approuvée par la passerelle Application Gateway. Pour approuver des certificats, vous devez télécharger la clé publique des certificats du serveur principal pour la passerelle Application Gateway (non le certificat racine). Seules les connexions aux serveurs principaux connus et sur liste blanche sont alors autorisées. Les autres serveurs principaux renvoient une erreur de passerelle. Les certificats auto-signés sont uniquement destinés à des fins de test et ne sont pas recommandés pour les charges de travail de production. Ces certificats doivent également figurer sur la liste approuvée par la passerelle Application Gateway comme décrit dans les étapes précédentes avant de pouvoir être utilisés.

## <a name="application-gateway-ssl-policy"></a>Stratégie SSL de la passerelle Application Gateway
La passerelle Application Gateway prend en charge les stratégies de négociation SSL configurables par l’utilisateur, offrant aux clients un meilleur contrôle des connexions SSL au niveau de la passerelle Application Gateway.

1. Les protocoles SSL 2.0 et 3.0 sont désactivés par défaut pour toutes les passerelles Application Gateway. Ils ne sont pas configurables du tout.
2. La définition de la stratégie SSL vous permet de désactiver les 3 protocoles suivants : TLSv1\_0, TLSv1\_1, TLSv1\_2.
3. Si aucune stratégie SSL n’est définie, les trois protocoles (TLSv1\_0, TLSv1\_1, TLSv1_2) sont activés.

## <a name="next-steps"></a>Étapes suivantes
Après avoir découvert le chiffrement SSL de bout en bout et la stratégie SSL, accédez à la rubrique [Activation d’une stratégie SSL de bout en bout sur la passerelle Application Gateway](application-gateway-end-to-end-ssl-powershell.md) afin de créer une passerelle d’application capable d’envoyer le trafic aux serveurs principaux de manière chiffrée.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png



<!--HONumber=Nov16_HO2-->


