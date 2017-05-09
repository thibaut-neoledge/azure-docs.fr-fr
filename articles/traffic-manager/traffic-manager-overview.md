---
title: "Présentation de Traffic Manager | Microsoft Docs"
description: "Cet article vous aidera à comprendre le fonctionnement de Traffic Manager, et de déterminer s’il est le choix de routage du trafic adapté à votre application"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 3f1f19f8d8a4f2e6e892ba3ede67f3749cedb11b
ms.contentlocale: fr-fr
ms.lasthandoff: 04/22/2017

---

# <a name="overview-of-traffic-manager"></a>Vue d’ensemble de Traffic Manager

Microsoft Azure Traffic Manager vous permet de contrôler la répartition du trafic utilisateur pour les points de terminaison de service dans différents centres de données. Les points de terminaison de service pris en charge par Traffic Manager incluent des machines virtuelles Azure, des applications web et des services cloud. Vous pouvez également utiliser Traffic Manager avec des points de terminaison externes non-Azure.

Traffic Manager utilise le DNS (Domain Name System) pour diriger les demandes des clients vers le point de terminaison approprié en fonction de la [méthode de routage du trafic](traffic-manager-routing-methods.md) et de l’intégrité des points de terminaison. Traffic Manager fournit un large éventail de méthodes de routage du trafic pour répondre à différents besoins d’application, la [surveillance](traffic-manager-monitoring.md) de l’intégrité des points de terminaison et le basculement automatique. Traffic Manager est résilient aux défaillances, notamment à l’échec d’une région Azure entière.

## <a name="traffic-manager-benefits"></a>Avantages de Traffic Manager

Traffic Manager peut vous aider à atteindre les objectifs suivants :

* **Améliorer la disponibilité des applications critiques**

    Traffic Manager vous permet de garantir une haute disponibilité de vos applications en surveillant vos points de terminaison et en fournissant un basculement automatique en cas de panne d’un point de terminaison.

* **Améliorer la réactivité des applications haute performance**

    Azure vous permet d'exécuter des sites web ou des services cloud dans des centres de données situés dans le monde entier. Traffic Manager améliore la réactivité de vos applications en dirigeant le trafic vers le point de terminaison affichant la latence réseau la plus faible pour le client.

* **Gérer les services sans les interrompre**

    Vous pouvez effectuer les opérations de maintenance planifiée sur vos applications sans temps d’arrêt. Traffic Manager dirige le trafic vers les autres points de terminaison pendant la maintenance.

* **Combiner des applications cloud et locales**

    Traffic Manager prend en charge des points de terminaison externes non Azure, ce qui permet de l’utiliser dans des déploiements de cloud hybride et locaux, notamment des scénarios tels que le « burst-to-cloud » la « migration vers le cloud » et le « basculement vers le cloud ».

* **Distribuer le trafic pour des déploiements vastes et complexes**

    En utilisant les [profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md), il est possible de combiner des méthodes de routage du trafic pour créer des règles flexibles et sophistiquées afin de répondre aux besoins des déploiements plus vastes et plus complexes.

## <a name="how-traffic-manager-works"></a>Fonctionnement de Traffic Manager

Azure Traffic Manager vous permet de contrôler la répartition du trafic entre les points de terminaison de votre application. Un point de terminaison est tout service côté Internet hébergé à l’intérieur ou à l’extérieur d’Azure.

Traffic Manager offre deux principaux avantages :

1. Distribution du trafic selon l’une des [méthodes de routage du trafic](traffic-manager-routing-methods.md)
2. [Analyse continue de l’intégrité des points de terminaison](traffic-manager-monitoring.md) et basculement automatique en cas d’échec des points de terminaison

Quand un client tente de se connecter à un service, il doit d’abord résoudre le nom DNS du service à une adresse IP. Le client se connecte ensuite à l’adresse IP pour accéder au service.

**L’essentiel ici est de comprendre que Traffic Manager fonctionne au niveau DNS.**  Traffic Manager utilise DNS pour diriger les clients vers des points de terminaison de service spécifiques en fonction des règles de la méthode de routage du trafic. Les clients se connectent **directement** au point de terminaison sélectionné. Traffic Manager n’est pas un proxy ou une passerelle. Traffic Manager ne voit pas le trafic entre le client et le service.

### <a name="traffic-manager-example"></a>Exemple Traffic Manager

Contoso Corp a développé un nouveau portail pour ses partenaires. L’URL de ce portail est https://partners.contoso.com/login.aspx. L’application est hébergée dans trois régions Azure. Pour améliorer la disponibilité et optimiser la performance globale, les clients utilisent Traffic Manager pour router le trafic client vers le point de terminaison disponible le plus proche.

Pour obtenir cette configuration :

* Ils déploient trois instances de leur service. Les noms DNS de ces déploiements sont « contoso-us.cloudapp.net », « contoso-eu.cloudapp.net » et « contoso-asia.cloudapp.net ».
* Ils créent ensuite un profil Traffic Manager nommé « contoso.trafficmanager.net », et le configurent pour utiliser la méthode de routage du trafic « Performance » sur les trois points de terminaison.
* Pour finir, ils configurent leur nom de domaine personnel, « partners.contoso.com » pour pointer vers « contoso.trafficmanager.net », en utilisant un enregistrement CNAME DNS.

![Configuration DNS de Traffic Manager][1]

> [!NOTE]
> Lorsque vous utilisez un domaine personnel avec Azure Traffic Manager, vous devez utiliser un enregistrement CNAME pour pointer votre nom de domaine personnel vers votre nom de domaine Traffic Manager. Les normes DNS ne vous permettent pas de créer un enregistrement CNAME au « sommet » (ou à la racine) d’un domaine. Par conséquent, vous ne pouvez pas créer d’enregistrement CNAME pour « contoso.com » (parfois appelé un domaine « nu »). Vous pouvez uniquement créer un enregistrement CNAME pour un domaine sous « contoso.com », tel que « www.contoso.com ». Pour contourner cette limitation, nous vous recommandons d’utiliser une simple redirection HTTP pour diriger les demandes pour « contoso.com » vers un autre nom, tel que « www.contoso.com ».

### <a name="how-clients-connect-using-traffic-manager"></a>Connexion des clients à l’aide de Traffic Manager

Dans la continuation de l’exemple précédent, quand un client demande la page https://partners.contoso.com/login.aspx, il accomplit les étapes suivantes pour résoudre le nom DNS et établir une connexion :

![Établissement de la connexion à l’aide de Traffic Manager][2]

1. Le client envoie une requête DNS à son service DNS récursif configuré pour résoudre le nom « partners.contoso.com ». Un service DNS récursif, parfois appelé service « DNS local », n’héberge pas directement de domaines DNS. Au lieu de cela, le client déleste le travail de contacter les divers services DNS faisant autorité sur Internet, nécessaire pour résoudre un nom DNS.
2. Pour résoudre le nom DNS, le service DNS récursif recherche les serveurs de noms pour le domaine « contoso.com ». Il contacte ensuite ces serveurs de noms pour demander l’enregistrement DNS « partners.contoso.com ». Les serveurs DNS contoso.com retournent l’enregistrement CNAME qui pointe vers contoso.trafficmanager.net.
3. Ensuite, le service DNS récursif recherche les serveurs de noms pour le domaine « trafficmanager.net », qui sont fournis par le service Azure Traffic Manager. Il envoie alors une demande pour l’enregistrement DNS « contoso.trafficmanager.net » à ces serveurs DNS.
4. Les serveurs de noms Traffic Manager reçoivent la demande. Ils choisissent un point de terminaison en fonction des critères suivants :

    - l’état configuré de chaque point de terminaison (les points de terminaison désactivés ne sont pas retournés) ;
    - l’état actuel de chaque point de terminaison, tel qu’il ressort des contrôles d’intégrité de Traffic Manager (pour plus d’informations, voir la rubrique relative à la [surveillance des points de terminaison avec Traffic Manager](traffic-manager-monitoring.md)) ;
    - la méthode de routage du trafic choisie (pour plus d’informations, voir [Méthodes de routage de Traffic Manager](traffic-manager-routing-methods.md)).

5. Le point de terminaison choisi est retourné en tant qu’autre enregistrement CNAME DNS. Dans ce cas, supposons que contoso-us.cloudapp.net est retourné.
6. Ensuite, le service DNS récursif recherche les serveurs de noms pour le domaine « cloudapp.net ». Il contacte ces serveurs de noms pour demander l’enregistrement DNS « contoso-us.cloudapp.net ». Un enregistrement DNS « A » contenant l’adresse IP du point de terminaison de service basé aux États-Unis est retourné.
7. Le service DNS récursif consolide les résultats et renvoie une seule réponse DNS au client.
8. Le client reçoit les résultats DNS et se connecte à l’adresse IP donnée. Notez qu’il se connecte directement au point de terminaison du service d’application, et non via Traffic Manager. Puisqu’il s’agit d’un point de terminaison HTTPS, le client exécute la négociation SSL/TLS nécessaire, puis soumet une demande HTTP GET pour la page « /login.aspx ».

Le service DNS récursif met en cache les réponses DNS qu’il reçoit. Le programme de résolution DNS sur l’appareil client met également en cache le résultat. La mise en cache permet que les requêtes DNS suivantes reçoivent une réponse plus rapidement, en utilisant les données du cache au lieu d’interroger d’autres serveurs de noms. La durée du cache est déterminée par la propriété « time-to-live » (TTL) de chaque enregistrement DNS. Des valeurs plus courtes entraînent une expiration plus rapide du cache, et donc davantage d’allers-retours avec les serveurs de noms Traffic Manager. Des valeurs plus longues signifient que davantage de temps peut être nécessaire pour diriger le trafic à l’écart d’un point de terminaison défaillant. Traffic Manager vous permet de configurer la durée de vie utilisée dans les réponses DNS de Traffic Manager, ce qui vous permet de choisir la valeur qui réponde au mieux aux besoins de votre application.

## <a name="pricing"></a>Tarification

Pour des informations sur les prix, consultez [Tarification Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faq"></a>Forum Aux Questions

Pour connaître les questions fréquemment posées, consultez la page [Forum Aux Questions (FAQ) relatif à Traffic Manager](traffic-manager-FAQs.md).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [basculement automatique et la surveillance des points de terminaison](traffic-manager-monitoring.md)de Traffic Manager.

En savoir plus sur les [méthodes de routage du trafic](traffic-manager-routing-methods.md)de Traffic Manager.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png


