---
title: "Optimiser le routage ExpressRoute : Azure | Microsoft Docs"
description: "Cette page fournit des détails sur l’optimisation du routage lorsque vous avez le choix entre plusieurs circuits ExpressRoute de connexion entre Microsoft et votre réseau professionnel."
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
ms.assetid: fca53249-d9c3-4cff-8916-f8749386a4dd
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/06/2017
ms.author: charwen
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: c3a85b9445d69330c3f6c7d298169efddb6ecca0
ms.lasthandoff: 04/07/2017


---
# <a name="optimize-expressroute-routing"></a>Optimiser le routage ExpressRoute
En présence de plusieurs circuits ExpressRoute, vous pouvez vous connecter à Microsoft par le biais de plusieurs chemins d’accès. Par conséquent, le routage pourrait ne pas être optimal. Autrement dit, votre trafic peut emprunter un chemin d’accès plus long pour atteindre Microsoft ; Microsoft peut faire de même pour atteindre votre réseau. Plus le chemin d’accès réseau est long, plus la latence est élevée. La latence a un impact direct sur les performances d’application ainsi que sur l’expérience utilisateur. Cet article aborde ce problème et explique comment optimiser le routage à l’aide des technologies de routage standard.

## <a name="suboptimal-routing-from-customer-to-microsoft"></a>Routage non optimal entre le client et Microsoft
Voici un exemple de problème de routage. Imaginez que vous disposez de deux bureaux aux États-Unis, un à Los Angeles et un à New York. Vos bureaux sont connectés sur un réseau étendu (WAN), qui peut être votre propre réseau principal ou le VPN IP de votre fournisseur de services. Vous avez deux circuits ExpressRoute également connectés sur le réseau étendu, l’un dans l’Ouest des États-Unis et l’autre dans l’Est. Vous avez bien évidemment deux chemins d’accès pour vous connecter au réseau Microsoft. Imaginez à présent que vous déployiez un service Azure (par exemple, Azure App Service) dans l’Ouest et dans l’Est des États-Unis. Vous avez pour objectif de connecter vos utilisateurs de Los Angeles à Azure dans l’Ouest des États-Unis et les utilisateurs de New York à Azure dans l’Est des États-Unis, car selon votre administrateur de service, les utilisateurs de chaque bureau doivent accéder aux services Azure les plus proches pour une expérience optimale. Malheureusement, le plan fonctionne bien pour les utilisateurs de la côte Est, mais pas pour ceux de la côte Ouest. La cause du problème est la suivante. Sur chaque circuit ExpressRoute, nous publions le préfixe pour Azure dans l’Est des États-Unis (23.100.0.0/16) et dans l’Ouest des États-Unis (13.100.0.0/16). Si vous ne savez pas à quelle région correspond chaque préfixe, vous ne pourrez pas le traiter différemment. Votre réseau WAN peut penser que les deux préfixes sont plus proches de l’Est que de l’Ouest des États-Unis et donc router les utilisateurs des deux bureaux vers le circuit ExpressRoute dans l’Est des États-Unis. Au final, vous risquez d’avoir des utilisateurs mécontents dans vos bureaux de Los Angeles.

![Scénario ExpressRoute n° 1 : routage non optimal entre le client et Microsoft](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution-use-bgp-communities"></a>Solution : utilisez des communautés BGP
Pour optimiser le routage pour les utilisateurs des deux bureaux, vous devez savoir faire la différence entre le préfixe Azure de l’Est et le préfixe Azure de l’Ouest. Nous encodons ces informations à l’aide des [valeurs de communauté BGP](expressroute-routing.md). Nous avons affecté une valeur de Communauté BGP unique à chaque région Azure, par exemple : « 12076:51004 » pour l’Est des États-Unis et « 12076:51006 » pour l’Ouest des États-Unis. À présent que vous savez à quelles régions Azure correspondent les préfixes, vous pouvez configurer les préférences de circuit ExpressRoute. Étant donné que nous utilisons le protocole BGP pour échanger des informations de routage, vous pouvez utiliser les préférences locales du protocole BGP pour influencer le routage. Dans notre exemple, vous pouvez affecter, dans l’Ouest des États-Unis, une valeur de préférence locale supérieure à celle de l’Est des États-Unis (13.100.0.0/16) et vice-versa dans l’Est des États-Unis (23.100.0.0/16). Cette configuration permet de garantir que, lorsque les deux chemins d’accès à Microsoft sont disponibles, vos utilisateurs de Los Angeles prendront le circuit ExpressRoute dans l’Ouest des États-Unis pour se connecter à Azure dans cette région, tandis que vos utilisateurs de New York prendront le circuit ExpressRoute de l’Est des États-Unis vers Azure dans cette région. Le routage est optimisé des deux côtés. 

![Solution ExpressRoute n° 1 : utilisez des communautés BGP](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

> [!NOTE]
> La même technique peut être appliquée pour le routage entre le client et le réseau virtuel Azure, en utilisant les préférences locales. Nous ne balisons pas la valeur de communauté BGP sur les préfixes publiés à partir d’Azure sur votre réseau. Toutefois, étant donné que vous savez à quel déploiement de réseau virtuel correspond quel bureau, vous pouvez configurer vos routeurs en conséquence pour privilégier un circuit ExpressRoute plutôt qu’un autre.
>
>

## <a name="suboptimal-routing-from-microsoft-to-customer"></a>Routage non optimal entre Microsoft et le client
Voici un autre exemple dans lequel les connexions en provenance de Microsoft prennent un chemin plus long pour atteindre votre réseau. Dans ce cas, vous utilisez des serveurs Exchange locaux et Exchange Online dans un [environnement hybride](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx). Vos bureaux sont connectés à un réseau étendu. Vous publiez vers Microsoft les préfixes des serveurs locaux de vos deux bureaux par le biais des deux circuits ExpressRoute. Dans certains cas, tels que la migration de boîte aux lettres, Exchange Online lance les connexions aux serveurs locaux. Malheureusement, la connexion à votre bureau de Los Angeles est routée au circuit ExpressRoute de l’Est des États-Unis, puis retraverse l’ensemble du pays avant d’atteindre la côte ouest. L’origine du problème est similaire au premier cas. En l’absence d’indicateur, le réseau Microsoft ne peut pas déterminer quel préfixe est le plus proche de la côte Ouest et de la côte Est. Malheureusement, il choisit le mauvais chemin vers votre bureau de Los Angeles.

![Scénario ExpressRoute n° 2 : routage non optimal entre Microsoft et le client](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution-use-as-path-prepending"></a>Solution : ajoutez le préfixe AS PATH
Il existe deux solutions à ce problème. La première consiste simplement à publier votre préfixe local pour le bureau de Los Angeles (177.2.0.0/31) sur le circuit ExpressRoute dans l’Ouest des États-Unis et celui pour le bureau de New York (177.2.0.2/31) sur le circuit ExpressRoute dans l’Est des États-Unis. Par conséquent, Microsoft ne dispose que d’un seul chemin d’accès pour se connecter à chacun de vos sites. Il n’existe aucune ambiguïté et le routage est optimisé. Avec cette conception, vous devez réfléchir à votre stratégie de basculement. Dans le cas où le chemin d’accès à Microsoft via ExpressRoute est interrompu, vous devez vous assurer qu’Exchange Online peut toujours se connecter à vos serveurs locaux. 

La deuxième solution est de continuer à publier les deux préfixes sur les deux circuits ExpressRoute, tout en nous indiquant quel préfixe correspond à chacun de vos bureaux. Étant donné que nous prenons en charge l’ajout de préfixe AS PATH BGP, vous pouvez configurer l’AS PATH pour influencer le routage. Dans cet exemple, vous pouvez allonger le chemin AS PATH pour 172.2.0.0/31 dans l’Est des États-Unis pour que nous privilégiions le circuit ExpressRoute dans l’Ouest pour le trafic destiné à ce préfixe ; notre réseau pensera que le chemin d’accès pour ce préfixe est plus court dans l’Ouest. De même, vous pouvez allonger le chemin AS PATH pour 172.2.0.2/31 dans l’Ouest des États-Unis pour que nous privilégiions le circuit ExpressRoute dans l’Est des États-Unis. Le routage est optimisé pour les deux bureaux. Grâce à cette conception, si un circuit ExpressRoute est défaillant, Exchange Online peut toujours vous joindre via un autre circuit ExpressRoute ainsi que par le biais de votre réseau étendu. 

> [!IMPORTANT]
> Nous supprimons les numéros AS privés dans le chemin AS PATH pour les préfixes reçus sur l’homologation Microsoft. Vous devez ajouter des numéros AS publics au chemin AS PATH pour influencer le routage pour l’homologation Microsoft.
> 
> 

![Solution ExpressRoute n° 2 : ajoutez le préfixe AS PATH](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

> [!NOTE]
> Bien que les exemples fournis ici s’appliquent aux homologations Microsoft et publiques, nous prenons en charge les mêmes fonctionnalités pour l’homologation privée. En outre, le préfixe AS Path fonctionne au sein d’un même circuit ExpressRoute pour influencer la sélection des chemins principaux et secondaires.
> 
> 

## <a name="suboptimal-routing-between-virtual-networks"></a>Routage non optimal entre des réseaux virtuels
Avec ExpressRoute, vous pouvez établir une communication réseau virtuel-réseau virtuel (également appelée « VNet ») en les reliant à un circuit ExpressRoute. Lorsque vous les reliez à plusieurs circuits ExpressRoute, un routage non optimal peut se produire entre les réseaux virtuels. Prenons un exemple. Vous disposez de deux circuits ExpressRoute, l’un dans l’Ouest des États-Unis et l’autre dans l’Est des États-Unis. Dans chaque région, vous avez deux réseaux virtuels. Vos serveurs web sont déployés sur un réseau virtuel et les serveurs d’applications sur l’autre. À des fins de redondance, vous reliez les deux réseaux virtuels de chaque région à la fois au circuit ExpressRoute local et au circuit ExpressRoute distant. Comme illustré ci-dessous, chaque réseau virtuel est doté de deux chemins d’accès menant vers l’autre réseau virtuel. Les réseaux virtuels ne font pas la distinction entre le circuit ExpressRoute local et le circuit ExpressRoute distant. Par conséquent, étant donné qu’ils appliquent une stratégie de routage ECMP (Equal-cost multi-path routing) pour équilibrer la charge du trafic entre les réseaux virtuels, certains flux de trafic emprunteront le chemin le plus long et seront routés au niveau du circuit ExpressRoute distant.

![Scénario ExpressRoute n° 3 : routage non optimal entre réseaux virtuels](./media/expressroute-optimize-routing/expressroute-case3-problem.png)

### <a name="solution-assign-a-high-weight-to-local-connection"></a>Solution : attribuer plus de poids à la connexion locale
La solution est simple. Étant donné que vous savez où se trouvent les réseaux virtuels et les circuits, vous pouvez nous indiquer le chemin à privilégier pour chaque réseau virtuel. Spécifiquement pour cet exemple, vous attribuez plus de poids à la connexion locale qu’à la connexion à distance (consultez l’exemple de configuration [ici](expressroute-howto-linkvnet-arm.md#modify-a-virtual-network-connection)). Lorsqu’un réseau virtuel reçoit le préfixe de l’autre réseau virtuel sur plusieurs connexions, il préfère la connexion avec le plus de poids pour envoyer le trafic destiné à ce préfixe.

![Solution ExpressRoute n° 3 : attribuer plus de poids à la connexion locale](./media/expressroute-optimize-routing/expressroute-case3-solution.png)

> [!NOTE]
> Vous pouvez également modifier entre le réseau virtuel et votre réseau sur site, si vous disposez de plusieurs circuits ExpressRoute, en configurant le poids attribué à une connexion au lien d’ajouter un préfixe AS PATH, technique décrite dans le second scénario ci-dessus. Pour chaque préfixe, nous examinerons toujours le poids attribué à la connexion avant la longueur du chemin AS pour décider du mode d’envoi du trafic.
>
>

