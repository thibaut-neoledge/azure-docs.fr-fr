<properties 
   pageTitle="Présentation de Traffic Manager | Microsoft Azure"
   description="Cet article vous aidera à comprendre le fonctionnement de Traffic Manager, et de déterminer s’il est le choix de routage du trafic adapté à votre application"
   services="traffic-manager"
   documentationCenter=""
   authors="jtuliani"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/09/2016"
   ms.author="jtuliani" />

# Qu’est-ce que Traffic Manager ?

Microsoft Azure Traffic Manager vous permet de contrôler la répartition du trafic utilisateur vers vos points de terminaison de service exécutés dans différents centres de données dans le monde entier.

Les points de terminaison de service pris en charge par le Traffic Manager incluent des machines virtuelles Azure, des applications Web et des services cloud. Vous pouvez également utiliser Traffic Manager avec des points de terminaison externes non-Azure.

Traffic Manager fonctionne en utilisant le DNS (Domain Name System) pour diriger les requêtes de l’utilisateur final vers le point de terminaison approprié en fonction de la méthode de routage du trafic configurée et de la vue actuelle de l’intégrité du point de terminaison. Les clients se connectent ensuite directement au point de terminaison de service approprié.

Traffic Manager prend en charge un [large éventail de méthodes de routage du trafic](traffic-manager-routing-methods.md) pour répondre à différents besoins d’application. Traffic Manager fournit des [contrôles d’intégrité de point de terminaison et un basculement de point de terminaison automatique](traffic-manager-monitoring.md), ce qui vous permet de créer des applications à haute disponibilité et résistantes aux défaillances, notamment en cas de défaillance d’une région Azure entière.

## Avantages de Traffic Manager

Traffic Manager peut vous aider à atteindre les objectifs suivants :

- **Améliorer la disponibilité des applications critiques** : Traffic Manager vous permet de garantir une haute disponibilité de vos applications critiques en surveillant vos points de terminaison dans et en fournissant un basculement automatique en cas de panne d’un point de terminaison.
- **Améliorer la réactivité des applications hautes performances** : Azure vous permet d’exécuter des services cloud ou des sites web dans différents centres de données à travers le monde. Traffic Manager peut améliorer la réactivité de vos applications en dirigeant les utilisateurs finaux vers le point de terminaison affichant la latence réseau la plus faible par rapport au client.
- **Mettre à niveau et exécuter la maintenance du service sans temps d’arrêt** : vous pouvez effectuer la mise à niveau en toute transparence et exécuter d’autres opérations de maintenance planifiées sur vos applications sans temps d’arrêt pour les utilisateurs finaux en utilisant Traffic Manager pour diriger le trafic vers d’autres points de terminaison lorsque la maintenance est en cours d’exécution.
- **Combiner des applications locales et basées sur le cloud** : Traffic Manager prend en charge des points de terminaison externes non Azure, ce qui permet de l’utiliser dans des déploiements de cloud hybride et locaux, notamment des scénarios tels que le « burst-to-cloud » la « migration vers le cloud » et le « basculement vers le cloud ».
- **Répartir le trafic pour les déploiements à grande échelle et complexes** : les méthodes de routage de trafic peuvent être combinées à l’aide de [profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md) pour créer des configurations de routage du trafic sophistiquées et flexibles permettant de répondre aux besoins de déploiements plus vastes et plus complexes. 

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## Étapes suivantes

- En savoir plus sur le [fonctionnement de Traffic Manager](traffic-manager-how-traffic-manager-works.md).

- Découvrez comment développer des applications à haute disponibilité à l’aide de la [surveillance de points de terminaison Traffic Manager](traffic-manager-monitoring.md).

- En savoir plus sur les [méthodes de routage du trafic](traffic-manager-routing-methods.md) prises en charge par Traffic Manager.

- [Créer un profil Traffic Manager](traffic-manager-manage-profiles.md).
 

<!---HONumber=AcomDC_0615_2016-->