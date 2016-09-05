<properties 
   pageTitle="Traffic Manager - Méthodes de routage du trafic | Microsoft Azure"
   description="Cet article vous aide à comprendre les différentes méthodes de routage du trafic utilisées par Traffic Manager."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/25/2016"
   ms.author="sewhee" />

# Méthodes de routage du trafic de Traffic Manager

Cette page décrit les méthodes de routage du trafic prises en charge par Azure Traffic Manager. Elles sont utilisées pour diriger les utilisateurs finaux vers le point de terminaison de service approprié.

> [AZURE.NOTE] L’API Azure Resource Manager (ARM) pour Traffic Manager utilise une terminologie différente de l’API de gestion des services Microsoft Azure (Azure Service Management, ASM). Cette modification a été introduite suite à des commentaires pour améliorer la clarté et limiter les malentendus courants. Dans cette page, nous utilisons la terminologie ARM. Les différences sont les suivantes :

>- Dans ARM, nous utilisons le concept de « méthode de routage du trafic » pour décrire l’algorithme utilisé pour déterminer le point de terminaison spécifique vers lequel un utilisateur final doit être dirigé à un moment donné. Dans ASM, nous parlons de « méthode d’équilibrage de charge ».

>- Dans ARM, nous utilisons le concept de « pondération » pour faire référence à la méthode de routage du trafic qui répartit le trafic sur tous les points de terminaison disponibles en fonction du poids défini pour chaque point de terminaison. Dans ASM, nous parlons de « tourniquet (round-robin) ».
>- Dans ARM, nous utilisons le concept de « priorité » pour faire référence à la méthode de routage du trafic qui dirige tout le trafic vers le premier point de terminaison disponible dans une liste ordonnée. Dans ASM, nous parlons de « basculement ».

> Dans tous les cas, les seules différences sont les dénominations. Les fonctionnalités sont les mêmes.


Azure Traffic Manager prend en charge un certain nombre d’algorithmes pour déterminer la manière dont les utilisateurs finaux sont routés vers les différents points de terminaison de service. Il s’agit de méthodes de routage du trafic. La méthode de routage du trafic est appliquée à chaque requête DNS reçue, afin de déterminer le point de terminaison qui doit être retourné dans la réponse DNS.

Trois méthodes de routage du trafic sont disponibles dans Traffic Manager :

- **Priorité :** sélectionnez « Priority (Priorité) » si vous souhaitez utiliser un point de terminaison de service principal pour tout le trafic et disposer de sauvegardes au cas où les points de terminaison principaux ou de sauvegarde ne sont pas disponibles. Pour plus d’informations, consultez la section [Méthode de routage du trafic basé sur la priorité](#priority-traffic-routing-method).

- **Pondération :** sélectionnez « Weighted (Pondéré) » si vous souhaitez distribuer le trafic entrant sur un ensemble de points de terminaison, soit uniformément, soit en fonction du poids que vous définissez. Pour plus d’informations, consultez la section [Méthode de routage du trafic basé sur la pondération](#weighted-traffic-routing-method).

- **Performances** : sélectionnez « Performance » quand vos points de terminaison se trouvent sur des emplacements géographiques différents et que vous souhaitez que les utilisateurs finaux utilisent le point de terminaison « le plus proche » en termes de latence réseau la plus faible. Pour plus d’informations, consultez la section [Méthode de routage du trafic basé sur les performances](#performance-traffic-routing-method).

> [AZURE.NOTE] Tous les profils Traffic Manager comprennent une surveillance continue de l’intégrité des points de terminaison et un basculement de point de terminaison automatique. Le tout est pris en charge pour toutes les méthodes de routage du trafic. Pour plus d’informations, consultez la rubrique relative à la [surveillance des points de terminaison avec Traffic Manager](traffic-manager-monitoring.md).

Un profil Traffic Manager donné ne peut utiliser qu’une seule méthode de routage du trafic. Vous pouvez sélectionner une méthode de routage du trafic différente pour votre profil à tout moment. Les modifications sont appliquées dans la minute, et aucun temps d’arrêt ne survient. Les méthodes de routage du trafic peuvent être combinées dans des profils Traffic Manager imbriqués. Cela permet de créer des configurations de routage du trafic sophistiquées et flexibles pour répondre aux besoins plus importants et plus complexes des applications. Pour plus d’informations, consultez [Profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md).

## Méthode de routage du trafic basé sur la priorité

Souvent, une organisation souhaite assurer la fiabilité de ses services en fournissant un ou plusieurs services de sauvegarde en cas de défaillance du service principal. La méthode de routage de trafic basé sur la priorité permet aux clients Azure de facilement implémenter ce modèle de basculement.

![Méthode de routage du trafic Azure Traffic Manager « Priority »][1]

Le profil Traffic Manager est configuré avec une liste hiérarchisée de points de terminaison de service. Par défaut, tout le trafic de l’utilisateur final est envoyé vers le point de terminaison principal (à priorité la plus élevée). Si le point de terminaison principal n’est pas disponible (sur la base de l’état activé ou désactivé des points de terminaison configurés et de la surveillance des points de terminaison en cours), les utilisateurs sont dirigés vers le second point de terminaison. Si les points de terminaison de type principal et secondaire ne sont pas disponibles, le trafic est envoyé vers le troisième, et ainsi de suite.

La configuration des priorités de point de terminaison est exécutée différemment dans les API ARM (et le nouveau portail Azure) et dans les API ASM (et portail Classic) :

- Dans les API ARM, la priorité des points de terminaison est configurée de façon explicite, à l’aide de la propriété « priority » définie pour chaque point de terminaison. Cette propriété doit avoir une valeur comprise entre 1 et 1 000, les valeurs inférieures représentant une priorité plus élevée. Deux points de terminaison ne peuvent pas partager la même valeur de priorité. La propriété est facultative. Lorsqu’elle est omise, une priorité par défaut est utilisée en fonction de l’ordre des points de terminaison.

- Dans les API ASM, la priorité des points de terminaison est configurée implicitement, selon l’ordre dans lequel les points de terminaison sont répertoriés dans la définition de profil. À l’aide du portail Azure « Classic », vous pouvez également configurer l’ordre de basculement dans la page de configuration associée au profil.

## Méthode de routage du trafic basé sur la pondération

Une approche courante pour à la fois assurer une haute disponibilité et optimiser l’utilisation du service consiste à fournir un ensemble de points de terminaison et à distribuer le trafic sur tous les points de terminaison, soit uniformément, soit avec un poids prédéfini. Elle est prise en charge par la méthode de routage du trafic « Weighted ».

![Méthode de routage du trafic Azure Traffic Manager « Weighted »][2]

Dans la méthode de routage du trafic basé sur la pondération, un poids est affecté à chaque point de terminaison dans le cadre de la configuration du profil Traffic Manager. Chaque poids a pour valeur un entier compris entre 1 et 1 000. Ce paramètre est facultatif. S’il est omis un poids par défaut de « 1 » est utilisé.
  
Le trafic de l’utilisateur final est réparti sur tous les points de terminaison de service disponibles (sur la base de l’état activé ou désactivé des points de terminaison configurés et de la surveillance des points de terminaison en cours). Pour chaque requête DNS reçue, un point de terminaison disponible est choisi au hasard, avec une probabilité basée sur le poids attribué à ce point de terminaison et sur les autres points de terminaison disponibles.

L’utilisation d’une même pondération entre tous les points de terminaison entraîne une répartition du trafic équilibrée, idéale pour une utilisation cohérente sur un ensemble de points de terminaison identiques Si des poids supérieurs (ou inférieurs) sont utilisés sur certains points de terminaison, ces derniers sont retournés plus (ou moins) fréquemment dans les réponses DNS et reçoivent dont plus de trafic. Plusieurs scénarios utiles sont donc disponibles :

- Mise à niveau progressive d’une application : allouez un pourcentage de trafic à acheminer vers un nouveau point de terminaison, puis augmentez progressivement le trafic au fil du temps jusqu’à 100 %.

- Migration d’applications vers Azure : créez un profil avec des points de terminaison Azure et externes, et spécifiez la pondération du trafic acheminé vers chaque point de terminaison.

- Extension du cloud pour une capacité supplémentaire : étendez rapidement un déploiement local dans le cloud en le plaçant derrière un profil Traffic Manager. Quand vous avez besoin d'une capacité supplémentaire dans le cloud, vous pouvez ajouter ou activer des points de terminaison supplémentaires et spécifier quelle partie du trafic est destinée à chaque point de terminaison.

Le routage du trafic pondéré peut être configuré via le nouveau portail Azure, mais les poids ne peuvent pas être configurés par le biais du portail « Classic ». Il peut être également configuré par le biais d’ARM et d’ASM à l’aide d’Azure PowerShell, de l’interface de ligne de commande Azure et des API REST Azure.

Remarque : les réponses DNS sont mises en cache par les clients et par les serveurs DNS récursifs que ces clients utilisent pour envoyer leurs requêtes DNS. Il est important de comprendre l’impact potentiel de cette mise en cache sur les distributions de trafic pondérées. Si le nombre de clients et de serveurs DNS récursifs est élevé, comme c’est le cas pour les applications classiques sur Internet, la distribution du trafic fonctionne comme prévu. Toutefois, si le nombre de clients ou de serveurs DNS récursifs est peu élevé, cette mise en cache peut fausser de manière significative la distribution du trafic. Les cas d’utilisation courants dans lesquels ce problème peut se produire sont notamment :

- les environnements de développement et de test ;
- les communications d’application à application ;
- les applications ciblées vers un utilisateur proche qui partage une infrastructure DNS récursive commune, par exemple, les employés d’une organisation.

Ces effets de mise en cache DNS sont communs à tous les systèmes de routage du trafic basés sur DNS, pas seulement à Azure Traffic Manager. Dans certains cas, la solution peut constituer à effacer le cache DNS de manière explicite. Dans d’autres cas, une autre méthode de routage du trafic peut être plus appropriée.

## Méthode de routage du trafic basé sur les performances

La réactivité de nombreuses applications peut être améliorée grâce au déploiement de points de terminaison sur deux ou plusieurs emplacements dans le monde entier et au routage des utilisateurs finaux vers l’emplacement « le plus proche ». C’est l’objectif de la méthode de routage du trafic « Performance ».

![Méthode de routage du trafic Azure Traffic Manager « Performance »][3]

Pour optimiser la réactivité, le point de terminaison « le plus proche » n’est pas nécessairement le plus proche en termes de distance géographique. Au lieu de cela, la méthode de routage de trafic « Performance » détermine le point de terminaison le plus proche de l’utilisateur final en fonction de la latence du réseau. La proximité est déterminée par une table de latence Internet affichant la durée de la boucle entre des plages d’adresses IP et chaque centre de données Azure.

Traffic Manager examine chaque requête DNS entrante et recherche l’adresse IP source de cette requête dans la table de latence Internet. Il détermine ainsi la latence de cette adresse IP vers chaque centre de données Azure. Traffic Manager sélectionne parmi les points de terminaison disponibles (sur la base de l’état activé ou désactivé des points de terminaison configurés et de la surveillance des points de terminaison en cours) celui qui a la plus faible latence, puis renvoie ce point de terminaison dans la réponse DNS. L’utilisateur final est par conséquent dirigé vers le point de terminaison qui lui donne la plus faible latence et donc des performances optimales.

Comme expliqué dans [Fonctionnement de Traffic Manager](traffic-manager-how-traffic-manager-works.md), Traffic Manager ne reçoit pas de requêtes DNS directement des utilisateurs finaux, mais il les reçoit du service DNS récursif pour lequel elles sont configurées. Par conséquent, l’adresse IP utilisée pour déterminer le point de terminaison « le plus proche » n’est pas l’adresse IP de l’utilisateur final, mais celle de son service DNS récursif. Dans la pratique, cette adresse IP est un proxy adéquat pour l’utilisateur final à cet effet.

Pour tenir compte des modifications apportées à l’Internet au niveau mondial et de l’ajout de nouvelles régions Azure, Traffic Manager met régulièrement à jour la table de latence Internet qu’il utilise. Toutefois, les variations de performances en temps réel ou les chargements via Internet ne peuvent pas être pris en compte.

Le routage du trafic « Performance » ne prend pas en compte la charge sur un point de terminaison de service donné, bien que Traffic Manager surveille vos points de terminaison et ne les inclut pas dans les requêtes DNS s'ils sont indisponibles.

Points à noter :

- Si votre profil contient plusieurs points de terminaison dans la même région Azure, le trafic dirigé vers cette région est distribué uniformément entre les points de terminaison disponibles (sur la base de l’état activé ou désactivé des points de terminaison configurés et de la surveillance des points de terminaison en cours). Si vous préférez une distribution de trafic différente au sein d’une région, vous pouvez utiliser des [profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md).

- Si tous les points de terminaison activés dans une région Azure donnée sont détériorés (sur la base de la surveillance des points de terminaison en cours), le trafic pour ces points de terminaison est distribué entre tous les autres points de terminaison disponibles spécifiés dans le profil, et non vers les points de terminaison les plus proches suivants. Cela a pour but d'éviter un échec en cascade qui pourrait se produire en cas de surcharge du point de terminaison le plus proche. Si vous préférez définir l’ordre de basculement des point de terminaison, vous pouvez le faire à l’aide de [profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md).

- Si vous utilisez la méthode de routage du trafic basé sur les performances avec des points de terminaison externes ou des points de terminaison imbriqués, vous devez spécifier l’emplacement de ces points de terminaison. Sélectionnez la région Azure la plus proche de votre déploiement. Les options disponibles sont les régions Azure, car ce sont les emplacements pris en charge par la table de latence Internet.

- L’algorithme qui calcule le point de terminaison qui doit être renvoyé à un utilisateur final est déterministe et non aléatoire. Les requêtes DNS répétées à partir du même client sont dirigées vers le même point de terminaison. Toutefois, vous ne devez pas vous fier à la méthode de routage du trafic basé sur les performances pour toujours router un utilisateur donné vers un déploiement donné (qui peut être nécessaire, par exemple, si les données utilisateur pour cet utilisateur sont stockées dans un seul emplacement). La raison est que, lorsque les utilisateurs finaux se déplacent, ils utilisent généralement différents serveurs DNS récursifs et peuvent donc être routés vers un autre point de terminaison. Ils peuvent également être impactés par des mises à jour de la table de latence Internet.

- Lorsque la table de latence Internet est mise à jour, il se peut que certains clients soient dirigés vers un autre point de terminaison. Le nombre d’utilisateurs affectés est généralement minime et reflète un routage plus précis en fonction des données de latence actuelles. Ces mises à jour sont essentielles pour conserver l’exactitude du routage du trafic « Performance », car Internet évolue en permanence.


## Étapes suivantes

Découvrez comment développer des applications à haute disponibilité à l’aide de la [surveillance de points de terminaison Traffic Manager](traffic-manager-monitoring.md)

En savoir plus sur la [création d’un profil Traffic Manager](traffic-manager-manage-profiles.md)


<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png

<!---HONumber=AcomDC_0824_2016-->