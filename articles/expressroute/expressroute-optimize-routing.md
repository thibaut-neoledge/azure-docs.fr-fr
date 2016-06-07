<properties
   pageTitle="Optimiser le routage ExpressRoute | Microsoft Azure"
   description="Cette page fournit des détails sur comment optimiser le routage lorsqu’un client a le choix entre plusieurs circuits ExpressRoute qui se connectent entre Microsoft et le réseau professionnel du client."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/26/2016"
   ms.author="charwen"/>

# Optimiser le routage ExpressRoute
En présence de plusieurs circuits ExpressRoute, vous pouvez vous connecter à Microsoft par le biais de plusieurs chemins d’accès. Par conséquent, le routage pourrait ne pas être optimal. Autrement dit, votre trafic peut emprunter un chemin d’accès plus long pour atteindre Microsoft ; Microsoft peut faire de même pour atteindre votre réseau. Plus le chemin d’accès réseau est long, plus la latence est élevée. La latence a un impact direct sur les performances d’application ainsi que sur l’expérience utilisateur. Cet article aborde ce problème et explique comment optimiser le routage à l’aide des technologies de routage standard.

## Cas 1 de routage non optimal
Voici un exemple de problème de routage. Imaginez que vous disposez de deux bureaux aux États-Unis, un à Los Angeles et un à New York. Vos bureaux sont connectés sur un réseau étendu (WAN), qui peut être votre propre réseau principal ou le VPN IP de votre fournisseur de services. Vous avez deux circuits ExpressRoute également connectés sur le réseau étendu, l’un dans l’Ouest des États-Unis et l’autre dans l’Est. Vous avez bien évidemment deux chemins d’accès pour vous connecter au réseau Microsoft. Imaginez à présent que vous déployiez un service Azure (par exemple, Azure App Service) dans l’Ouest et dans l’Est des États-Unis. Vous avez pour objectif de connecter vos utilisateurs de Los Angeles à Azure dans l’Ouest des États-Unis et les utilisateurs de New York à Azure dans l’Est des États-Unis, car selon votre administrateur de service, les utilisateurs de chaque bureau doivent accéder aux services Azure les plus proches pour une expérience optimale. Malheureusement, le plan fonctionne bien pour les utilisateurs de la côte Est, mais pas pour ceux de la côte Ouest. La cause du problème est la suivante. Sur chaque circuit ExpressRoute, nous publions le préfixe pour Azure dans l’Est des États-Unis (23.100.0.0/16) et dans l’Ouest des États-Unis (13.100.0.0/16). Si vous ne savez pas à quelle région correspond chaque préfixe, vous ne pourrez pas le traiter différemment. Votre réseau WAN peut penser que les deux préfixes sont plus proches de l’Est que de l’Ouest des États-Unis et donc router les utilisateurs des deux bureaux vers le circuit ExpressRoute dans l’Est des États-Unis. Au final, vous risquez d’avoir des utilisateurs mécontents dans vos bureaux de Los Angeles.

![](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### Solution : utilisez des communautés BGP
Pour optimiser le routage pour les utilisateurs des deux bureaux, vous devez savoir faire la différence entre le préfixe Azure de l’Est et le préfixe Azure de l’Ouest. Nous encodons ces informations à l’aide des [valeurs de communauté BGP](expressroute-routing.md). Nous avons affecté une valeur de Communauté BGP unique à chaque région Azure, par exemple : « 12076:51004 » pour l’Est des États-Unis et « 12076:51006 » pour l’Ouest des États-Unis. À présent que vous savez à quelles régions Azure correspondent les préfixes, vous pouvez configurer les préférences de circuit ExpressRoute. Étant donné que nous utilisons le protocole BGP pour échanger des informations de routage, vous pouvez utiliser les préférences locales du protocole BGP pour influencer le routage. Dans notre exemple, vous pouvez affecter, dans l’Ouest des États-Unis, une valeur de préférence locale supérieure à celle de l’Est des États-Unis (13.100.0.0/16) et vice-versa dans l’Est des États-Unis (23.100.0.0/16). Cette configuration permet de garantir que, lorsque les deux chemins d’accès à Microsoft sont disponibles, vos utilisateurs de Los Angeles prendront le circuit ExpressRoute dans l’Ouest des États-Unis pour se connecter à Azure dans cette région, tandis que vos utilisateurs de New York prendront le circuit ExpressRoute de l’Est des États-Unis vers Azure dans cette région. Le routage est optimisé des deux côtés.

![](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

## Cas 2 de routage non optimal
Voici un autre exemple dans lequel les connexions en provenance de Microsoft prennent un chemin plus long pour atteindre votre réseau. Dans ce cas, vous utilisez des serveurs Exchange locaux et Exchange Online dans un [environnement hybride](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx). Vos bureaux sont connectés à un réseau étendu. Vous publiez vers Microsoft les préfixes des serveurs locaux de vos deux bureaux par le biais des deux circuits ExpressRoute. Dans certains cas, tels que la migration de boîte aux lettres, Exchange Online lance les connexions aux serveurs locaux. Malheureusement, la connexion à votre bureau de Los Angeles est routée au circuit ExpressRoute de l’Est des États-Unis, puis retraverse l’ensemble du pays avant d’atteindre la côte ouest. L’origine du problème est similaire au premier cas. En l’absence d’indicateur, le réseau Microsoft ne peut pas déterminer quel préfixe est le plus proche de la côte Ouest et de la côte Est. Malheureusement, il choisit le mauvais chemin vers votre bureau de Los Angeles.

![](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### Solution : ajoutez le préfixe AS PATH
Il existe deux solutions à ce problème. La première consiste simplement à publier votre préfixe local pour le bureau de Los Angeles (177.2.0.0/31) sur le circuit ExpressRoute dans l’Ouest des États-Unis et celui pour le bureau de New York (177.2.0.2/31) sur le circuit ExpressRoute dans l’Est des États-Unis. Par conséquent, Microsoft ne dispose que d’un seul chemin d’accès pour se connecter à chacun de vos sites. Il n’existe aucune ambiguïté et le routage est optimisé. Avec cette conception, vous devez réfléchir à votre stratégie de basculement. Dans le cas où le chemin d’accès à Microsoft via ExpressRoute est interrompu, vous devez vous assurer qu’Exchange Online peut toujours se connecter à vos serveurs locaux.

La deuxième solution est de continuer à publier les deux préfixes sur les deux circuits ExpressRoute, tout en nous indiquant quel préfixe correspond à chacun de vos bureaux. Étant donné que nous prenons en charge l’ajout de préfixe AS PATH BGP, vous pouvez configurer l’AS PATH pour influencer le routage. Dans cet exemple, vous pouvez allonger le chemin AS PATH pour 172.2.0.0/31 dans l’Est des États-Unis pour que nous privilégiions le circuit ExpressRoute dans l’Ouest pour le trafic destiné à ce préfixe ; notre réseau pensera que le chemin d’accès pour ce préfixe est plus court dans l’Ouest. De même, vous pouvez allonger le chemin AS PATH pour 172.2.0.2/31 dans l’Ouest des États-Unis pour que nous privilégiions le circuit ExpressRoute dans l’Est des États-Unis. Le routage est optimisé pour les deux bureaux. Grâce à cette conception, si un circuit ExpressRoute est défaillant, Exchange Online peut toujours vous joindre via un autre circuit ExpressRoute ainsi que par le biais de votre réseau étendu.

>[AZURE.IMPORTANT] Nous supprimons les numéros AS privés dans le chemin AS PATH pour les préfixes reçus sur l’homologation Microsoft. Vous devez ajouter des numéros AS publics au chemin AS PATH pour influencer le routage pour l’homologation Microsoft.

![](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

<!---HONumber=AcomDC_0601_2016-->