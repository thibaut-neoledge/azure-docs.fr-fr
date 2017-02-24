---
title: "Considérations sur la topologie du réseau lors de l’utilisation du proxy d’application Azure AD | Microsoft Docs"
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
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1fdc00865f31352026a64c2769d9d65047bb2fa6
ms.openlocfilehash: 26ec9167f1f883c3c71947a55142020fb48e206a


---

# <a name="network-topology-considerations-when-using-azure-ad-application-proxy"></a>Considérations sur la topologie du réseau lors de l’utilisation du proxy d’application Azure AD
> [!NOTE]
> Le proxy d’application est une fonctionnalité qui n’est disponible que si vous effectuez une mise à niveau vers l’édition Premium ou De base d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).
> 

Cet article explique les considérations de topologie réseau lors de l’utilisation de l’application de proxy d’application Azure AD pour la publication et l’accès à distance de vos applications. 

## <a name="traffic-flow"></a>Flux de trafic

Lorsqu’une application est publiée via le proxy d’application Azure AD, tout le trafic des utilisateurs pour les flux des applications principales cibles circule via les tronçons suivants :

* Tronçon 1 : Utilisateur vers point de terminaison public du service de proxy d’application Azure AD sur Azure
* Tronçon 2 : Service de proxy d’application vers le connecteur
* Tronçon 3 : Connecteur vers application cible

 ![Fournisseurs de cloud IaaS AzureAD multiples](./media/application-proxy-network-topologies/application-proxy-three-hops.png)

## <a name="tenant-location-and-app-proxy-service"></a>Emplacement du client et service de proxy d’application

Lorsque vous vous inscrivez pour un client Azure AD, la région de votre client (États-Unis, EMEA, Asie-Pacifique, etc.) est déterminée en fonction du pays que vous spécifiez. Lorsque vous activez le proxy d’application, les instances du service de proxy d’application de votre client sont affichées dans la même région que votre client Azure AD, ou la région la plus proche de celui-ci. 

Par exemple, si la région de votre client Azure AD est l’Union européenne (UE), tous vos connecteurs de proxy d’application Azure AD seront connectés aux instances du service de proxy d’application dans les centres de données Azure de l’Union européenne. Cela signifie également que tous vos utilisateurs passeront par les instances du service de proxy d’application à cet emplacement, lorsque des tentatives d’accès aux applications publiées.

## <a name="considerations-for-reducing-latency"></a>Considérations relatives à la réduction de la latence

Toutes les solutions de proxy ajouteront de la latence à votre connexion réseau. Quel que soit le proxy ou la solution VPN que vous choisissez comme solution d’accès à distance, cela comprendra toujours un ensemble de serveurs permettant la connexion à votre réseau d’entreprise. 

Les entreprises incluent généralement des points de terminaison de serveur dans la zone démilitarisée (DMZ) de leur réseau. Mais avec le proxy d’application Azure AD, aucune zone DMZ n’est requise.  En effet, le trafic du proxy d’application passe par le service de proxy dans le cloud, tandis que les connecteurs se trouvent sur votre réseau d’entreprise.

### <a name="connector-placement"></a>Placement d’un connecteur

Le service de proxy d’application choisit l’emplacement des instances pour vous, en fonction de votre emplacement de client. Par conséquent, vous pouvez décider de l’emplacement auquel installer le connecteur, ce qui vous donne la possibilité de définir les caractéristiques de latence de bout en bout de votre trafic réseau.

Lorsque vous configurez le proxy d’application de service, voici quelques questions que vous devez vous poser :

* Où se trouve l’application ?
* Où se trouvent la plupart des utilisateurs qui accèdent à l’application ?
* Où se trouve l’instance de proxy d’application (cela dépend de votre client) ?
* Disposez-vous déjà d’une connexion réseau dédiée vers les centres de données Azure (comme Express Route ou une configuration de réseau privé virtuel similaire) ?

Le placement du connecteur déterminera la latence des tronçons #2 et #3. Lorsque vous évaluez le placement du connecteur, vous devez tenir compte des facteurs suivants :

* Le connecteur a besoin d’une ligne de vue sur un centre de données pour effectuer des opérations de délégation Kerberos contrainte (KCD) lorsque vous souhaitez utiliser l’authentification unique (SSO) pour les applications principales.
* Le connecteur est généralement installé plus près de l’application, afin de réduire la latence du connecteur à l’application.

### <a name="general-approach-to-minimize-latency"></a>Approche générale pour la réduction de la latence

Vous pouvez essayer de réduire la latence du trafic de bout en bout en optimisant chaque tronçon réseau, afin que le trafic transite de façon directe.

Chaque tronçon peut être optimisé en :

* Réduisant la distance entre les deux extrémités du tronçon.
* Choisissant le réseau approprié à parcourir. Par exemple, parcourir un réseau privé au lieu de l’Internet public peut être plus rapide en raison des liaisons dédiées.
 
Si vous disposez d’une liaison VPN/Express Route dédiée entre Azure et votre réseau d’entreprise, vous souhaiterez peut-être en tirer parti.

## <a name="focus-your-optimizing-strategy"></a>Concentrer votre stratégie d’optimisation

Étant donné que vos utilisateurs peuvent accéder aux applications à distance sur Internet, vous devez toujours vous concentrer sur l’optimisation des tronçons 2 et 3. Voici quelques modèles courants que vous pouvez incorporer.

### <a name="pattern-1-optimize-hop-3"></a>Modèle #1 : Optimisation du tronçon #3 :

Pour optimiser le tronçon 3, le connecteur est placé près de l’application cible dans le réseau du client. L’avantage de cette procédure est que le connecteur est susceptible d’avoir besoin d’une ligne de vue vers le contrôleur de domaine, comme indiqué ci-dessus. Cette approche est généralement suffisante pour la plupart des clients et des scénarios. La plupart de nos clients suivent ce modèle.

 ![Fournisseurs de cloud IaaS AzureAD multiples](./media/application-proxy-network-topologies/application-proxy-hop3.png)


> [!NOTE]
Il existe certains scénarios pour lesquels vous devez optimiser les tronçons #2 et #3 pour obtenir les caractéristiques de latence que vous souhaitez. Par exemple, si vous avez une installation VPN ou ExpressRoute entre votre réseau et le centre de données, ce scénario vous permet d’optimiser le tronçon #2, en plus du tronçon #3.
>

### <a name="pattern-2-take-advantage-of-expressroute-with-public-peering"></a>Modèle #2 : Tirer parti d’ExpressRoute avec l’homologation publique

Si vous avez une installation ExpressRoute avec l’homologation publique, nous tirerons parti de la connexion ExpressRoute, plus rapide, pour le tronçon #2. Le tronçon #3 est déjà optimisé en plaçant le connecteur à proximité de l’application dans le réseau du client.

![Fournisseurs de cloud IaaS AzureAD multiples](./media/application-proxy-network-topologies/application-proxy-expressroute-public.png)

### <a name="pattern-3-taking-advantage-expressroute-with-private-peering"></a>Modèle #3 : Tirer parti d’ExpressRoute avec l’homologation privée

Si vous avez une installation VPN ou ExpressRoute dédiée avec l’homologation privée entre Azure et votre réseau d’entreprise sur lequel l’application est installée, vous avez une autre option. Dans cette configuration, le réseau virtuel dans Azure est généralement considéré comme l’extension du réseau de l’entreprise. Par conséquent, vous pouvez installer le connecteur dans le centre de données Azure tout en répondant aux exigences de faible latence de la connexion connecteur à application pour le tronçon #3. 

La latence n’est pas compromise, car le trafic circule sur une connexion dédiée. Toutefois, vous avez l’avantage supplémentaire d’améliorer la latence du tronçon #2. En effet, la connexion au service-à-connecteur du proxy (tronçon #2) est un tronçon plus court, car le connecteur est installé dans un centre de données Azure proche de votre client AAD (et par conséquent, du proxy d’application).

![Fournisseurs de cloud IaaS AzureAD multiples](./media/application-proxy-network-topologies/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Autres approches

Cet article portait jusqu’ici sur l’emplacement d’un connecteur. Toutefois, si le déplacement de l’application est une option pour vous (par exemple, dans Azure ou un autre environnement hébergé), alors le placement de l’application peut être modifié pour obtenir de meilleures caractéristiques de latence. 

De plus en plus d’organisations déplacent leurs réseaux dans des environnements hébergés. Cela leur permet de placer leurs applications dans l’environnement hébergé qui fait également partie de leur réseau d’entreprise tout en restant au sein du domaine. Dans ce cas, les modèles ci-dessus peuvent être appliqués au nouvel emplacement de l’application.

Envisagez d’utiliser des groupes de connecteurs pour les applications cibles qui se trouvent dans des réseaux et des emplacements différents. Si vous envisagez cette option, consultez la page [Services de domaine Azure AD](https://azure.microsoft.com/en-us/services/active-directory-ds). 

## <a name="common-scenarios"></a>Scénarios courants

Dans cette section, nous allons étudier quelques cas d’utilisation. Pour tous les cas d’utilisation ci-dessous, supposons que le client Azure AD (et par conséquent le point de terminaison du service de proxy) se trouve aux États-Unis. Dans les autres régions du monde, les mêmes considérations s’appliquent généralement.

### <a name="use-case-1"></a>Cas d’utilisation 1

L’application se trouve dans un réseau client aux États-Unis avec des utilisateurs dans la même région. Aucun ExpressRoute ou VPN n’existe entre le centre de données Azure et le réseau d’entreprise.

**Recommandation :** suivez le cas d’utilisation #1 ci-dessus. Pour une latence améliorée, envisagez l’utilisation d’ExpressRoute, si nécessaire, voir les <cas d’utilisation #3 et #4>.

Il s’agit d’un modèle simple. Le modèle le plus courant consiste à optimiser le tronçon #3, où le connecteur est placé à proximité de l’application. C’est également un choix naturel, étant donné que le connecteur est généralement installé avec la ligne de vue vers l’application et le centre de données pour effectuer des opérations de KCD.
Ce cas d’utilisation suit le modèle #1 ci-dessous.

![Fournisseurs de cloud IaaS AzureAD multiples](./media/application-proxy-network-topologies/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Cas d’utilisation 2

L’application se trouve dans un réseau client aux États-Unis avec des utilisateurs répartis dans le monde. Aucun ExpressRoute ou VPN n’existe entre le centre de données Azure et le réseau d’entreprise.

**Recommandation :** suivez le cas d’utilisation #2 ci-dessus. Pour une latence améliorée, envisagez l’utilisation d’ExpressRoute, si nécessaire, voir les (cas d’utilisation #3 et #4).

Encore une fois, le modèle le plus courant consiste à optimiser le tronçon #3, où le connecteur est placé à proximité de l’application, pour les raisons indiquées plus haut. Le tronçon #3 n’est généralement pas coûteux, si le tout est contenu dans la même région. Toutefois, le tronçon #1 peut être plus coûteux selon l’emplacement de l’utilisateur, car tous les utilisateurs accèderont à l’instance du proxy d’application aux États-Unis. Il est important de noter que toutes les solutions de proxy auront des caractéristiques similaires en ce qui concerne les utilisateurs répartis globalement.

Ce cas d’utilisation suit le modèle #2 ci-dessous.

![Fournisseurs de cloud IaaS AzureAD multiples](./media/application-proxy-network-topologies/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Cas d’utilisation 3

L’application se trouve dans un réseau client aux États-Unis. ExpressRoute avec l’homologation publique existe entre Azure et le réseau d’entreprise.

**Recommandation :** placez le connecteur aussi près que possible de l’application. Le système utilisera automatiquement ExpressRoute pour le tronçon #2. Cela suit le modèle #2 décrit ci-dessus.

Si la liaison ExpressRoute utilise l’homologation publique, alors le trafic entre le proxy et le connecteur suivra ce lien et le tronçon #2 aura une latence optimisée.

Ce cas d’utilisation suit le modèle #3 ci-dessous.

![Fournisseurs de cloud IaaS AzureAD multiples](./media/application-proxy-network-topologies/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Cas d’utilisation 4

L’application se trouve dans un réseau client aux États-Unis. ExpressRoute avec l’homologation privée existe entre Azure et le réseau d’entreprise.

**Recommandation :** placez le connecteur dans le centre de données Azure qui est connecté au réseau d’entreprise via l’homologation privée ExpressRoute. Cela suit le modèle #3 décrit ci-dessus.

Le connecteur peut être placé dans le centre de données Azure. Dans la mesure où il a toujours une ligne de vue à l’application et le centre de données via le réseau privé, saut #3 reste optimisé. Toutefois, cette configuration optimise davantage le tronçon #2.

Ce cas d’utilisation suit le modèle #4 ci-dessous.

![Fournisseurs de cloud IaaS AzureAD multiples](./media/application-proxy-network-topologies/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Cas d’utilisation 5

L’application se trouve dans un réseau client en Union européenne avec la plupart des utilisateurs aux États-Unis.

**Recommandation :** placez le connecteur près de l’application. Pour les raisons évoquées ci-dessus, il s’agit de la meilleure solution. Étant donné que les utilisateurs des États-Unis ont accès à une instance de proxy d’application qui se trouve dans la même région, le tronçon #1 n’est pas trop onéreux. Hop #3 est optimisé. Toutefois, le tronçon #2 est généralement coûteux dans ce cas d’utilisation.

Ce cas d’utilisation suit le modèle #5a ci-dessous.

![Fournisseurs de cloud IaaS AzureAD multiples](./media/application-proxy-network-topologies/application-proxy-pattern5a.png)

Envisagez l’utilisation d’ExpressRoute comme indiqué dans les modèles #2 et #3 ci-dessus. Ici, vous voyez le modèle #2 appliqué.

Ce cas d’utilisation suit le modèle #5b ci-dessous.

![Fournisseurs de cloud IaaS AzureAD multiples](./media/application-proxy-network-topologies/application-proxy-pattern5b.png)

Une autre variante de ce cas d’utilisation que vous pouvez également envisager est décrite ci-dessous.

Si la plupart des utilisateurs de l’organisation sont aux États-Unis, il est probable que votre réseau « s’étende » aussi vers les États-Unis. Si tel est le cas, le connecteur peut être placé aux États-Unis et peut tirer parti de la ligne de réseau d’entreprise interne dédiée vers l’application dans l’Union européenne. De cette façon, les tronçons #2 et #3 sont optimisés.

Ce cas d’utilisation suit le modèle #5c ci-dessous.

![Fournisseurs de cloud IaaS AzureAD multiples](./media/application-proxy-network-topologies/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Étapes suivantes
[Activer le proxy d’application](active-directory-application-proxy-enable.md)<br>
[Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)<br>
[Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)<br>
[Résoudre les problèmes rencontrés avec le proxy d’application](active-directory-application-proxy-troubleshoot.md)





<!--HONumber=Feb17_HO1-->


