---
title: Considérations sur les performances d’Azure Traffic Manager | Microsoft Docs
description: Comprendre les performances sur Traffic Manager et comment tester les performances de votre site Web lors de l’utilisation de Traffic Manager
services: traffic-manager
documentationcenter: ''
author: sdwheeler
manager: carmonm
editor: joaoma

ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/10/2016
ms.author: sewhee

---
# Considérations sur les performances de Traffic Manager
Cette page explique les considérations relatives aux performances de Traffic Manager. Imaginons la situation suivante : vous avez un site web dans la région des États-Unis et un autre en Asie ; la vérification de l’intégrité des sondes de Traffic Manager échoue sur l’un d’eux ; tous vos utilisateurs sont redirigés vers la région saine ; ce comportement peut apparaître comme un problème de performances, mais il serait normal compte tenu de la distance de la demande de l’utilisateur.

## Remarque importante sur le fonctionnement de Traffic Manager
* Traffic Manager ne fait pour l’essentiel qu’une seule chose : la résolution DNS. Cela signifie que le seul impact sur les performances que Traffic Manager peut avoir sur votre site Web est la recherche DNS initiale.
* Point de clarification sur la recherche DNS de Traffic Manager. Traffic Manager renseigne et met régulièrement à jour les serveurs racine Microsoft DNS normaux en fonction de votre stratégie et des résultats de la sonde. Donc, même pendant la recherche DNS initiale, il n’y a aucune implication de Traffic Manager, dans la mesure où la requête DNS est gérée par les serveurs racine Microsoft DNS normaux. Si Traffic Manager s’arrête (autrement dit, si une défaillance survient dans les machines virtuelles exécutant la détection de la stratégie et la mise à jour DNS), il y n’a aucun impact sur votre nom DNS Traffic Manager dans la mesure où les entrées des serveurs DNS Microsoft seront toujours conservées. La seule conséquence sera que la détection et la mise à jour basées sur la stratégie ne se produiront pas (si votre site principal tombe en panne, Traffic Manager ne sera pas en mesure de mettre à jour DNS pour pointer vers votre site de basculement).
* Le trafic N’est PAS transmis via Traffic Manager. Il n’existe pas de serveurs Traffic Manager agissant comme intermédiaire entre vos clients et votre service hébergé Azure. Une fois terminée la recherche DNS, Traffic Manager est totalement supprimé de la communication entre le client et le serveur.
* La recherche DNS est très rapide, et est mise en cache. La recherche DNS initiale dépend du client et de ses serveurs DNS configurés : généralement, un client peut effectuer une recherche DNS en 50 ms environ (voir http://www.solvedns.com/dns-comparison/). Une fois effectuée la première recherche, les résultats sont mis en cache pendant la durée de vie (TTL) du DNS, soit pour Traffic Manager une valeur par défaut de 300 secondes.
* La stratégie Traffic Manager que vous choisissez (performances, basculement, tourniquet) n’a aucune influence sur les performances du DNS. Votre stratégie de performances peut affecter négativement l’expérience de votre utilisateur, si, par exemple, vous dirigez des utilisateurs des États-Unis vers un service hébergé en Asie, mais ce problème de performances n’est pas provoqué par Traffic Manager.

## Test des performances de Traffic Manager
Il existe quelques sites web accessibles publiquement que vous pouvez utiliser pour déterminer les performances et le comportement de Traffic Manager. Ces sites sont utiles pour déterminer la latence DNS et les services hébergés vers lesquels vos utilisateurs à travers le monde sont dirigés. N’oubliez pas que la plupart de ces outils ne mettent pas en cache les résultats DNS. Par conséquent, l’exécution des tests à plusieurs reprises affichera la recherche DNS complète, tandis que les clients se connectant à votre point de terminaison Traffic Manager ne verront l’impact sur les performances de la recherche DNS complète qu’une seule fois pendant la durée de vie.

## Exemples d’outils pour mesurer les performances
L’un des outils les plus simples est WebSitePulse. Entrez l’URL et vous verrez les statistiques telles que le temps de résolution DNS, le premier octet, le dernier octet et autres statistiques de performances. Vous pouvez choisir entre trois emplacements différents à partir desquels tester votre site. Dans cet exemple, vous allez voir que la première exécution montre que la première recherche DNS dure 0,204 s. La deuxième fois que nous exécutons ce test sur le même point de terminaison Traffic Manager, la recherche DNS prend 0,002 s, puisque les résultats sont déjà mis en cache.

http://www.websitepulse.com/help/tools.php

![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

Durée DNS avec mise en cache :

![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

Un autre outil très utile pour obtenir un temps de résolution DNS à partir de plusieurs zones géographiques simultanément est l’outil Check Website de Watchmouse. Entrez l’URL et vous verrez le temps de résolution DNS, l’heure de la connexion et la vitesse à partir de plusieurs emplacements de zones géographiques. Cette solution est également pratique pour tester la stratégie des performances de Traffic Manager et voir vers quel service hébergé vos différents utilisateurs à travers le monde sont dirigés.

http://www.watchmouse.com/en/checkit.php

![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

http://tools.pingdom.com/ : teste un site web et fournit des statistiques de performances pour chaque élément de la page sur un graphique visuel. Si vous basculez vers l’onglet Analyse de la page, vous pouvez voir le pourcentage de temps consacré à la recherche DNS.

http://www.whatsmydns.net/ : le site effectue une recherche DNS à partir de 20 emplacements de zones géographiques différents et affiche les résultats sur une carte. Il s’agit d’une excellente représentation visuelle pour aider à déterminer à quel service hébergé vos clients se connectent.

http://www.digwebinterface.com : semblable au site watchmouse, mais celui-ci affiche plus d’informations DNS détaillées, notamment les enregistrements CNAME et A. Veillez à cocher « Coloriser la sortie » et « Statistiques » sous les options et sélectionnez « Tous » sous Noms de serveurs.

## Étapes suivantes
[À propos des méthodes de routage du trafic de Traffic Manager](traffic-manager-routing-methods.md)

[Tester vos paramètres de Traffic Manager](traffic-manager-testing-settings.md)

[Opérations sur Traffic Manager (Référence sur l’API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Applets de commande Azure Traffic Manager](http://go.microsoft.com/fwlink/p/?LinkId=400769)

<!---HONumber=AcomDC_0824_2016-->