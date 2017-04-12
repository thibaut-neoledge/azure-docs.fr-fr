---
title: "Considérations sur la topologie du réseau lors de l’utilisation du proxy d’application Azure Active Directory | Microsoft Docs"
description: "Couvre les considérations sur la topologie du réseau lors de l’utilisation du proxy d’application Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6869453e0776405841890978eef97f549be97541
ms.lasthandoff: 04/03/2017


---

# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Considérations sur la topologie du réseau lors de l’utilisation du proxy d’application Azure Active Directory

Cet article explique les considérations de topologie réseau lors de l’utilisation de l’application de proxy d’application Azure Active Directory (Azure AD) pour la publication et l’accès à distance de vos applications.

## <a name="traffic-flow"></a>Flux de trafic

Lorsqu’une application est publiée via le proxy d’application Azure AD, tout le trafic des utilisateurs pour les flux des applications principales cibles circule via les tronçons suivants :

* Tronçon 1 : Utilisateur vers point de terminaison public du service de proxy d’application Azure AD sur Azure
* Tronçon 2 : Service de proxy d’application vers le connecteur
* Tronçon 3 : Connecteur vers application cible

 ![Diagramme illustrant le flux de trafic de l’utilisateur vers l’application cible](./media/application-proxy-network-topologies/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Emplacement du locataire et service de proxy d’application

Lorsque vous vous inscrivez pour un locataire Azure AD, la région de votre locataire est déterminée par le pays que vous spécifiez. Lorsque vous activez le proxy d’application, les instances du service de proxy d’application de votre locataire sont affichées dans la même région que votre locataire Azure AD, ou la région la plus proche de celui-ci.

Par exemple, si la région de votre locataire Azure AD est l’Union européenne (UE), tous vos connecteurs de proxy d’application utilisent des instances de service dans des centres de données Azure dans l’Union européenne. Cela signifie également que tous vos utilisateurs passent par les instances du service de proxy d’application à cet emplacement, lors des tentatives d’accès aux applications publiées.

## <a name="considerations-for-reducing-latency"></a>Considérations relatives à la réduction de la latence

Toutes les solutions de proxy ajoutent de la latence à votre connexion réseau. Quel que soit le proxy ou la solution VPN que vous choisissez comme solution d’accès à distance, cela comprend toujours un ensemble de serveurs permettant la connexion à votre réseau d’entreprise.

Les organisations comptent généralement des points de terminaison de serveur dans leur réseau de périmètre. Avec un proxy d’application Azure AD, aucun réseau de périmètre n’est toutefois obligatoire. En effet, le trafic passe par le service de proxy dans le cloud, tandis que les connecteurs se trouvent sur votre réseau d’entreprise.

### <a name="connector-placement"></a>Placement d’un connecteur

Le proxy d’application choisit l’emplacement des instances pour vous, en fonction de l’emplacement de votre locataire. Par conséquent, vous pouvez décider de l’emplacement auquel installer le connecteur, ce qui vous donne la possibilité de définir les caractéristiques de latence de votre trafic réseau.

Lorsque vous configurez le service de proxy d’application, vous devez poser les questions suivantes :

* Où se trouve l’application ?
* Où se trouvent la plupart des utilisateurs qui accèdent à l’application ?
* Où se trouve l’instance de proxy d’application (cela dépend de votre locataire) ?
* Disposez-vous déjà d’une connexion réseau dédiée vers les centres de données Azure (comme Azure ExpressRoute ou un VPN similaire) ?

Le placement du connecteur détermine la latence des tronçons 2 et 3 (décrits dans la section précédente). Lorsque vous évaluez le placement du connecteur, vous devez tenir compte des facteurs suivants :

* Le connecteur a besoin d’une ligne de vue sur un centre de données. Ceci permet au connecteur d’effectuer des opérations de délégation Kerberos contrainte (KCD) lorsque vous souhaitez utiliser l’authentification unique (SSO) pour les applications principales.
* Le connecteur est généralement installé plus près de l’application, afin de réduire la latence du connecteur à l’application.

### <a name="general-approach-to-minimize-latency"></a>Approche générale pour la réduction de la latence

Vous pouvez essayer de réduire la latence du trafic de bout en bout en optimisant chaque tronçon réseau. Chaque tronçon peut être optimisé en :

* Réduisant la distance entre les deux extrémités du tronçon.
* Choisissant le réseau approprié à parcourir. Par exemple, parcourir un réseau privé au lieu de l’Internet public peut être plus rapide en raison des liaisons dédiées.

Si vous disposez d’une liaison VPN ou ExpressRoute dédiée entre Azure et votre réseau d’entreprise, vous souhaiterez peut-être l’utiliser.

## <a name="focus-your-optimizing-strategy"></a>Concentrer votre stratégie d’optimisation

Étant donné que vos utilisateurs peuvent accéder aux applications à distance sur Internet, vous devez toujours vous concentrer sur l’optimisation des tronçons 2 et 3. Voici quelques modèles courants que vous pouvez incorporer.

### <a name="pattern-1-optimize-hop-3"></a>Modèle 1 : Optimisation du tronçon 3

Pour optimiser le tronçon 3, le connecteur est placé près de l’application cible dans le réseau du client. L’avantage de cette procédure est que le connecteur est susceptible d’avoir besoin d’une ligne de vue vers le contrôleur de domaine. Cette approche suffit dans la plupart des scénarios. (En pratique, la plupart de nos clients suivent ce modèle.)

 ![Diagramme illustrant l’optimisation du tronçon 3, avec le connecteur placé à proximité de l’application cible](./media/application-proxy-network-topologies/application-proxy-hop3.png)


> [!NOTE]
Il existe certains scénarios pour lesquels vous devez optimiser les tronçons 2 et 3 pour obtenir les caractéristiques de latence que vous souhaitez. Par exemple, si vous avez une installation VPN ou ExpressRoute entre votre réseau et le centre de données Azure, vous pouvez optimiser ces deux tronçons.

### <a name="pattern-2-take-advantage-of-expressroute-with-public-peering"></a>Modèle 2 : Tirer parti d’ExpressRoute avec l’homologation publique

Si vous avez une installation ExpressRoute avec l’homologation publique, vous pouvez utiliser la connexion ExpressRoute plus rapide pour le tronçon 2. (Le tronçon 3 est déjà optimisé en plaçant le connecteur à proximité de l’application dans votre réseau.)

![Diagramme illustrant l’optimisation du tronçon 2, à l’aide d’une connexion ExpressRoute](./media/application-proxy-network-topologies/application-proxy-expressroute-public.png)

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Modèle 3 : Tirer parti d’ExpressRoute avec l’homologation privée

Si vous avez une installation VPN ou ExpressRoute dédiée avec l’homologation privée entre Azure et votre réseau d’entreprise, vous avez une autre option. Dans cette configuration, le réseau virtuel dans Azure est généralement considéré comme une extension du réseau de l’entreprise. Par conséquent, vous pouvez installer le connecteur dans le centre de données Azure tout en répondant aux exigences de faible latence de la connexion connecteur à application pour le tronçon 3.

La latence n’est pas compromise, car le trafic circule sur une connexion dédiée. Vous avez également l’avantage supplémentaire d’améliorer la latence du tronçon 2. Ceci est dû au fait que la connexion entre le service de proxy d’application et le connecteur constitue maintenant un tronçon plus court. Le connecteur est installé dans un centre de données Azure proche de l’emplacement de votre locataire Azure AD (et donc du proxy d’application).

![Diagramme illustrant un connecteur installé dans un centre de données Azure](./media/application-proxy-network-topologies/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Autres approches

Bien que cet article ait pour objectif le placement du connecteur, vous pouvez également modifier le placement de l’application pour obtenir de meilleures caractéristiques de latence.

De plus en plus d’organisations déplacent leurs réseaux dans des environnements hébergés. Cela leur permet de placer leurs applications dans un environnement hébergé qui fait également partie de leur réseau d’entreprise tout en restant au sein du domaine. Dans ce cas, les modèles décrits dans les sections précédentes peuvent être appliqués au nouvel emplacement de l’application.

Envisagez d’utiliser des groupes de connecteurs pour les applications cibles qui se trouvent dans des réseaux et des emplacements différents. Si vous envisagez cette option, consultez la page [Services de domaine Azure AD](../active-directory-domain-services/active-directory-ds-overview.md).

## <a name="common-scenarios"></a>Scénarios courants

Dans cette section, nous allons étudier quelques cas d’utilisation. Supposons que le locataire Azure AD (et par conséquent le point de terminaison du service de proxy) se trouve aux États-Unis. Les considérations présentées dans ces cas d’utilisation s’appliquent généralement également à d’autres régions du monde.

### <a name="use-case-1"></a>Cas d’utilisation 1

L’application se trouve dans un réseau d’entreprise aux États-Unis avec des utilisateurs dans la même région. Aucun ExpressRoute ou VPN n’existe entre le centre de données Azure et le réseau d’entreprise.

**Recommandation :** suivez le modèle 1, expliqué dans la section précédente. Pour une latence améliorée, envisagez l’utilisation d’ExpressRoute, si nécessaire.

Il s’agit d’un modèle simple. Vous optimisez le tronçon 3 en plaçant le connecteur à proximité de l’application. C’est également un choix naturel, étant donné que le connecteur est généralement installé avec la ligne de vue vers l’application et le centre de données pour effectuer des opérations de KCD.

![Diagramme illustrant le contour des États-Unis et comment les tronçons sont organisés dans ce cas d’utilisation](./media/application-proxy-network-topologies/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Cas d’utilisation 2

L’application se trouve dans un réseau d’entreprise aux États-Unis avec des utilisateurs répartis dans le monde. Aucun ExpressRoute ou VPN n’existe entre le centre de données Azure et le réseau d’entreprise.

**Recommandation :** suivez le modèle 2, expliqué dans la section précédente. Pour une latence améliorée, envisagez l’utilisation d’ExpressRoute, si nécessaire.

Là encore, le modèle courant consiste à optimiser le tronçon 3, où vous placez le connecteur à proximité de l’application. Le tronçon 3 n’est généralement pas coûteux, si le tout est contenu dans la même région. Toutefois, le tronçon 1 peut être plus coûteux selon l’emplacement de l’utilisateur, car tous les utilisateurs accèdent à l’instance du proxy d’application aux États-Unis. Il est important de noter que toutes les solutions de proxy ont des caractéristiques similaires en ce qui concerne les utilisateurs répartis globalement.

![Diagramme illustrant le contour des continents et comment les tronçons sont organisés dans ce cas d’utilisation](./media/application-proxy-network-topologies/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Cas d’utilisation 3

L’application se trouve dans un réseau d’entreprise aux États-Unis. ExpressRoute avec l’homologation publique existe entre Azure et le réseau d’entreprise.

**Recommandation :** placez le connecteur aussi près que possible de l’application. Le système utilise automatiquement ExpressRoute pour le tronçon 2. Cela correspond au modèle 2, expliqué dans la section précédente.

Si la liaison ExpressRoute utilise l’homologation publique, le trafic entre le proxy et le connecteur suit ce lien. Le tronçon 2 a une latence optimisée.

![Diagramme illustrant le contour des États-Unis et comment les tronçons sont organisés dans ce cas d’utilisation](./media/application-proxy-network-topologies/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Cas d’utilisation 4

L’application se trouve dans un réseau d’entreprise aux États-Unis. ExpressRoute avec l’homologation privée existe entre Azure et le réseau d’entreprise.

**Recommandation :** placez le connecteur dans le centre de données Azure qui est connecté au réseau d’entreprise via l’homologation privée ExpressRoute. Cela correspond au modèle 3, expliqué dans la section précédente.

Le connecteur peut être placé dans le centre de données Azure. Dans la mesure où le connecteur a toujours une ligne de vue à l’application et le centre de données via le réseau privé, le tronçon 3 reste optimisé. En outre, le tronçon 2 est davantage optimisé.

![Diagramme illustrant le contour des États-Unis et comment les tronçons sont organisés dans ce cas d’utilisation](./media/application-proxy-network-topologies/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Cas d’utilisation 5

L’application se trouve dans un réseau d’entreprise dans l’Union européenne avec la plupart des utilisateurs aux États-Unis.

**Recommandation :** placez le connecteur près de l’application. Étant donné que les utilisateurs des États-Unis ont accès à une instance de proxy d’application qui se trouve dans la même région, le tronçon 1 n’est pas trop onéreux. Le tronçon 3 est optimisé. Toutefois, le tronçon 2 est généralement coûteux dans ce cas d’utilisation.

![Diagramme illustrant le contour des continents et comment les tronçons sont organisés dans ce cas d’utilisation](./media/application-proxy-network-topologies/application-proxy-pattern5a.png)

Envisagez d’utiliser ExpressRoute, comme décrit dans les sections précédentes sur les modèles 2 et 3.

![Diagramme illustrant le contour des continents et comment les tronçons sont organisés dans ce cas d’utilisation](./media/application-proxy-network-topologies/application-proxy-pattern5b.png)

Vous pouvez également envisager d’utiliser une autre variante dans cette situation. Si la plupart des utilisateurs de l’organisation sont aux États-Unis, il est probable que votre réseau « s’étende » aussi vers les États-Unis. Si tel est le cas, le connecteur peut être placé aux États-Unis et peut utiliser la ligne de réseau d’entreprise interne dédiée vers l’application dans l’Union européenne. Les tronçons 2 et 3 sont ainsi optimisés.

![Diagramme illustrant le contour des continents et comment les tronçons sont organisés dans ce cas d’utilisation](./media/application-proxy-network-topologies/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Étapes suivantes

- [Activer le proxy d’application](active-directory-application-proxy-enable.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)
- [Résoudre les problèmes rencontrés avec le proxy d’application](active-directory-application-proxy-troubleshoot.md)

