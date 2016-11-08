---
title: Activation d’une stratégie SSL de bout en bout sur la passerelle Application Gateway | Microsoft Docs
description: Cette page fournit une vue d’ensemble de la prise en charge du chiffrement SSL de bout en bout pour la passerelle Application Gateway.
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva

ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2016
ms.author: amsriva

---
# <a name="enabling-ssl-policy-and-end-to-end-ssl-on-application-gateway"></a>Activation d’une stratégie SSL de bout en bout sur la passerelle Application Gateway
## <a name="overview"></a>Vue d'ensemble
La passerelle Application Gateway prend en charge la terminaison SSL au niveau de la passerelle, après laquelle le trafic transite généralement de façon non chiffrée vers les serveurs principaux. Les serveurs web ne sont ainsi plus chargés des opérations coûteuses de chiffrement/déchiffrement. Mais pour certains clients, une communication non chiffrée vers les serveurs principaux n’est pas une option acceptable. Cela peut être dû à des d’exigences de sécurité et de conformité, ou au fait que l’application n’accepte que des connexions sécurisées. Pour de telles applications, la passerelle Application Gateway prend désormais en charge le chiffrement SSL de bout en bout.

Le chiffrement SSL de bout en bout vous permet de transmettre en toute sécurité des données sensibles au backend en profitant des avantages des fonctions d’équilibrage de charge de couche 7 fournies par la passerelle Application Gateway, notamment l’affinité des cookies, le routage basé sur l’URL, la prise en charge du routage basé sur des sites ou la possibilité d’injecter des en-têtes X-Forwarded.

Configuré avec un mode de communication SSL de bout en bout, la passerelle Application Gateway ferme les sessions SSL de l’utilisateur au niveau de la passerelle et déchiffre le trafic de l’utilisateur. Il applique ensuite les règles configurées pour sélectionner une instance de pool principal appropriée vers laquelle acheminer le trafic. La passerelle Application Gateway établit ensuite une nouvelle connexion SSL vers le serveur principal, puis chiffre à nouveau les données à l’aide du certificat de clé publique du serveur principal avant de transmettre la requête au serveur principal. Le chiffrement SSL de bout en bout est activé en définissant le paramètre de protocole BackendHTTPSetting sur Https, qui est ensuite appliqué à un pool principal. Chaque serveur principal du pool principal pour lequel un chiffrement SSL de bout en bout a été activé doit être configuré avec un certificat afin de permettre une communication sécurisée.

![imageURLroute](./media/application-gateway-multi-site-overview/multisite.png)

Dans cet exemple, les demandes de https://contoso.com peuvent être acheminées vers ContosoServerPool via HTTP, et https://fabrikam.com sera acheminé vers FabrikamServerPool via HTTPS avec un chiffrement SSL de bout en bout.

## <a name="end-to-end-ssl-and-white-listing-of-certificates"></a>Chiffrement SSL de bout en bout et liste blanche de certificats
La passerelle Application Gateway communique uniquement avec les instances de backend identifiées dont le certificat figure sur la liste approuvée par la passerelle d’application. Pour approuver des certificats, vous devez télécharger la clé publique des certificats du serveur principal pour la passerelle d’application. Seules les connexions identifiées et figurant sur la liste approuvée du backend sont alors autorisées tandis que les autres connexions entraînent une erreur de passerelle. Les certificats auto-signés sont uniquement destinés à des fins de test et ne sont pas recommandés pour les charges de travail de production. Ces certificats doivent également figurer sur la liste approuvée par la passerelle d’application comme décrit ci-dessus avant de pouvoir être utilisés.

## <a name="application-gateway-ssl-policy"></a>Stratégie SSL de la passerelle Application Gateway
lLa passerelle Application Gateway prend également en charge les stratégies de négociation SSL configurables par l’utilisateur, offrant aux clients un contrôle plus précis des connexions SSL au niveau de la passerelle d’application.

1. Les protocoles SSL 2.0 et 3.0 sont désactivés de force pour toutes les passerelles d’application. Ils ne sont pas configurables du tout.
2. La définition de la stratégie SSL vous permet de désactiver les 3 protocoles suivants : TLSv1_0, TLSv1_1, TLSv1_2.
3. Si aucune stratégie SSL n’est définie, les trois protocoles (TLSv1_0, TLSv1_1, TLSv1_2) sont activés.

## <a name="next-steps"></a>Étapes suivantes
Après avoir découvert le chiffrement SSL de bout en bout et la stratégie SSL, accédez à la rubrique [Activation d’une stratégie SSL de bout en bout sur la passerelle Application Gateway](application-gateway-end-to-end-ssl-powershell.md) afin de créer une passerelle d’application capable d’envoyer le trafic au serveur principal de manière chiffrée.

<!--HONumber=Oct16_HO2-->


