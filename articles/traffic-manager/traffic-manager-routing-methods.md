---
title: "Traffic Manager - Méthodes de routage du trafic | Microsoft Docs"
description: "Cet article vous aide à comprendre les différentes méthodes de routage du trafic utilisées par Traffic Manager."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: db1efbf6-6762-4c7a-ac99-675d4eeb54d0
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 11a120338a9f76bfb0a56a70d0c566625bc518b9
ms.openlocfilehash: ee3265032a839b1c35821e60e1143ae772389804
ms.lasthandoff: 02/27/2017

---

# <a name="traffic-manager-traffic-routing-methods"></a>Méthodes de routage du trafic de Traffic Manager

Azure Traffic Manager prend en charge trois méthodes de routage du trafic pour déterminer comment router le trafic réseau vers les différents points de terminaison de service. Traffic Manager applique la méthode de routage du trafic à chaque requête DNS qu’il reçoit. La méthode de routage du trafic détermine le point de terminaison retourné dans la réponse DNS.

La prise en charge d’Azure Resource Manager pour Traffic Manager utilise une terminologie différente de celle du modèle de déploiement Classic. Le tableau suivant montre les différences entre les termes propres à Resource Manager et à Classic :

| Terme Resource Manager | Terme Classic |
| --- | --- |
| Méthode de routage du trafic |Méthode d’équilibrage de charge |
| Méthode de priorité |Méthode de basculement |
| Méthode pondérée |Méthode Tourniquet (round robin) |
| Méthode Performance |Méthode Performance |

Nous nous sommes appuyés sur les commentaires des clients pour modifier la terminologie afin d’améliorer la perception et de réduire les malentendus courants. Les fonctionnalités sont les mêmes.

Trois méthodes de routage du trafic sont disponibles dans Traffic Manager :

* **Priorité :** sélectionnez « Priority (Priorité) » si vous souhaitez utiliser un point de terminaison de service principal pour tout le trafic et disposer de sauvegardes au cas où les points de terminaison principaux ou de sauvegarde ne sont pas disponibles.
* **Pondération :** sélectionnez « Weighted (Pondéré) » si vous souhaitez distribuer le trafic entrant sur un ensemble de points de terminaison, soit uniformément, soit en fonction du poids que vous définissez.
* **Performances :** sélectionnez « Performance » quand vos points de terminaison se trouvent sur des emplacements géographiques différents et que vous souhaitez que les utilisateurs finaux utilisent le point de terminaison « le plus proche » en termes de latence réseau la plus faible.

Tous les profils Traffic Manager incluent une surveillance de l’intégrité des points de terminaison et un basculement de point de terminaison automatique. Pour plus d’informations, consultez la rubrique relative à la [surveillance des points de terminaison avec Traffic Manager](traffic-manager-monitoring.md). Un profil Traffic Manager donné ne peut utiliser qu’une seule méthode de routage du trafic. Vous pouvez sélectionner une méthode de routage du trafic différente pour votre profil à tout moment. Les modifications sont appliquées dans la minute, sans aucun temps d’arrêt. Les méthodes de routage du trafic peuvent être combinées dans des profils Traffic Manager imbriqués. Une imbrication permet de créer des configurations de routage du trafic sophistiquées et flexibles répondant aux besoins d’applications complexes plus importantes. Pour plus d’informations, consultez [Profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md).

## <a name="priority-traffic-routing-method"></a>Méthode de routage du trafic basé sur la priorité

Souvent, une organisation souhaite assurer la fiabilité de ses services en déployant un ou plusieurs services de sauvegarde utilisables en cas de défaillance du service principal. La méthode de routage du trafic « Priorité » permet aux clients Azure d’implémenter facilement ce modèle de basculement.
![Méthode de routage du trafic « Priorité » d’Azure Traffic Manager][1]

Le profil Traffic Manager contient une liste hiérarchisée de points de terminaison de service. Par défaut, Traffic Manager envoie tout le trafic vers le point de terminaison principal (priorité la plus élevée). Si le point de terminaison principal n’est pas disponible, Traffic Manager route le trafic vers le deuxième point de terminaison. Si les points de terminaison de type principal et secondaire ne sont pas disponibles, le trafic est envoyé vers le troisième, et ainsi de suite. La disponibilité du point de terminaison est basée sur l’état configuré (activé ou désactivé) et la surveillance du point de terminaison en cours.

### <a name="configuring-endpoints"></a>Configuration de points de terminaison

Avec Azure Resource Manager, vous configurez la priorité de point de terminaison explicitement à l’aide de la propriété « priority » pour chaque point de terminaison. Cette propriété est une valeur comprise entre 1 et 1000. Plus la valeur est basse, plus la priorité est élevée. Des points de terminaison ne peuvent pas partager des valeurs de priorité. La définition de la propriété est facultative. En cas d’omission, une priorité par défaut basée sur l’ordre du point de terminaison est utilisée.

Avec l’interface classique, la priorité du point de terminaison est configurée de façon implicite. La priorité est basée sur l’ordre dans lequel les points de terminaison sont répertoriés dans la définition du profil.

## <a name="weighted-traffic-routing-method"></a>Méthode de routage du trafic basé sur la pondération
La méthode de routage du trafic « Pondéré » vous permet de répartir le trafic uniformément ou d’utiliser une pondération prédéfinie.

![Méthode de routage du trafic « Pondéré » d’Azure Traffic Manager][2]

Dans la méthode de routage du trafic Pondéré, vous affectez une pondération à chaque point de terminaison dans la configuration du profil Traffic Manager. La pondération est un entier compris entre 1 et 1000. Ce paramètre est facultatif. En cas d’omission, Traffic Manager utilise la pondération par défaut « 1 ».

Pour chaque requête DNS reçue, Traffic Manager choisit au hasard un point de terminaison disponible. La probabilité de choisir un point de terminaison dépend des pondérations assignées à tous les points de terminaison disponibles. L’utilisation de la même pondération pour tous les points de terminaison produit une distribution uniforme du trafic. L’utilisation de pondérations supérieures ou inférieures sur certains points de terminaison a pour effet que ceux-ci sont retournés plus ou moins fréquemment dans les réponses DNS.

La méthode pondérée permet des scénarios utiles :

* Mise à niveau progressive d’une application : allouez un pourcentage de trafic à acheminer vers un nouveau point de terminaison, puis augmentez progressivement le trafic au fil du temps jusqu’à 100 %.
* Migration d’application vers Azure : créez un profil avec des points de terminaison Azure et externes. Ajustez la pondération des points de terminaison pour privilégier les nouveaux points de terminaison.
* Extension du cloud pour une capacité supplémentaire : étendez rapidement un déploiement local dans le cloud en le plaçant derrière un profil Traffic Manager. Quand vous avez besoin d'une capacité supplémentaire dans le cloud, vous pouvez ajouter ou activer des points de terminaison supplémentaires et spécifier quelle partie du trafic est destinée à chaque point de terminaison.

Le nouveau portail Azure prend en charge la configuration de routage du trafic Pondéré. La pondération ne peut pas être configurée dans le portail Classic. Vous pouvez également configurer les pondérations à l’aide du Gestionnaire des ressources et des versions classiques d’Azure PowerShell, de CLI et des API REST.

Il est important de comprendre que les réponses DNS sont mises en cache par les clients et les serveurs DNS récursifs que les clients utilisent pour résoudre les noms DNS. Cette mise en cache peut avoir une incidence sur les distributions de trafic pondérées. Lorsque le nombre de clients et de serveurs DNS récursifs est élevé, la distribution du trafic fonctionne comme prévu. En revanche, si le nombre de clients ou de serveurs DNS récursifs est peu élevé, la mise en cache peut fausser sensiblement la distribution du trafic.

Les cas d’utilisation courants sont les suivants :

* les environnements de développement et de test ;
* les communications d’application à application ;
* les applications destinées à une base étroite d’utilisateurs qui partagent une infrastructure DNS récursive commune (par exemple, des employés d’une entreprise se connectant via un proxy).

Ces effets de mise en cache DNS sont communs à tous les systèmes de routage du trafic basés sur DNS, pas seulement à Azure Traffic Manager. Dans certains cas, la solution peut constituer à effacer le cache DNS de manière explicite. Dans d’autres cas, une autre méthode de routage du trafic peut être plus appropriée.

## <a name="performance-traffic-routing-method"></a>Méthode de routage du trafic basé sur les performances

Un déploiement de points de terminaison en deux emplacements ou plus dans le monde peut améliorer la réactivité de nombreuses applications en routant le trafic vers l’emplacement « le plus proche » de vous. La méthode de routage du trafic « Performance » offre cette possibilité.

![Méthode de routage du trafic « Performance » d’Azure Traffic Manager][3]

Le point de terminaison « le plus proche » n’est pas nécessairement le plus proche en termes de distance géographique. Au lieu de cela, la méthode de routage du trafic « Performance » détermine le point de terminaison le plus proche en mesurant le temps de réponse (ou latence) du réseau. Traffic Manager tient à jour une Table de latence Internet pour suivre le temps d’aller-retour entre les plages d’adresses IP et chaque centre de données Azure.

Traffic Manager recherche l’adresse IP source de la demande DNS entrante dans la Table de latence Internet. Traffic Manager choisit un point de terminaison disponible dans le centre de données Azure dont la latence est la plus faible pour la plage d’adresses IP, puis renvoie ce point de terminaison dans la réponse DNS.

Comme expliqué dans [Fonctionnement de Traffic Manager](traffic-manager-how-traffic-manager-works.md), Traffic Manager ne reçoit pas de requêtes DNS provenant directement de clients. Au lieu de cela, les requêtes DNS proviennent du service DNS récursif que les clients sont configurés pour utiliser. Par conséquent, l’adresse IP utilisée pour déterminer le point de terminaison « le plus proche » n’est pas l’adresse IP du client, mais celle du service DNS récursif. Dans la pratique, cette adresse IP est un bon proxy pour le client.


Traffic Manager met régulièrement à jour la Table de latence Internet pour refléter les modifications de l’Internet global et des nouvelles régions Azure. Toutefois, les performances des applications varient en fonction des variations en temps réel de la charge sur Internet. Le routage du trafic Performance ne surveille pas la charge sur un point de terminaison de service donné. En revanche, si un point de terminaison devient indisponible, Traffic Manager ne l’inclut pas dans les réponses aux requêtes DNS.


Points à noter :

* Si votre profil contient plusieurs points de terminaison situés dans la même région Azure, Traffic Manager répartit uniformément le trafic entre les points de terminaison disponibles dans cette région. Si vous préférez une distribution de trafic différente au sein d’une région, vous pouvez utiliser des [profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md).
* Si tous les points de terminaison activés dans une région Azure donnée sont détériorés, Traffic Manager distribue le trafic à tous les autres points de terminaison disponibles plutôt qu’au point de terminaison suivant le plus proche. Cette logique évite toute défaillance en cascade résultant d’une surcharge du point de terminaison le plus proche suivant. Si vous souhaitez définir une séquence de basculement préférée, utilisez des [profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md).
* Si vous utilisez la méthode de routage du trafic basé sur les performances avec des points de terminaison externes ou des points de terminaison imbriqués, vous devez spécifier l’emplacement de ces points de terminaison. Choisissez la région Azure la plus proche de votre déploiement. Ces emplacements sont les valeurs prises en charge par la Table de latence Internet.
* L’algorithme qui choisit le point de terminaison est déterministe. Des requêtes DNS répétées à partir du même client sont dirigées vers le même point de terminaison. En règle générale, les clients utilisent des serveurs DNS récursifs différents quand ils se déplacent. Le client peut être routé vers un point de terminaison différent. Le routage peut également être affecté par des mises à jour de la Table de latence Internet. Par conséquent, la méthode de routage du trafic Performance ne garantit pas qu’un client est toujours routé vers le même point de terminaison.
* Lorsque la Table de latence Internet change, il se peut que certains clients soient dirigés vers un point de terminaison différent. La précision de ce changement de routage varie en fonction des données de latence en cours. Ces mises à jour sont essentielles pour conserver l’exactitude du routage du trafic « Performance », car Internet évolue en permanence.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment développer des applications à haute disponibilité à l’aide de la [surveillance de points de terminaison Traffic Manager](traffic-manager-monitoring.md)

En savoir plus sur la [création d’un profil Traffic Manager](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png

