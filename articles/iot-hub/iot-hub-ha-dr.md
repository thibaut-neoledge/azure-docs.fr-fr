<properties
 pageTitle="Haute disponibilité et récupération d’urgence IoT Hub | Microsoft Azure"
 description="Décrit les options qui vous aident à créer des solutions IoT à haute disponibilité dotées de fonctionnalités de récupération d’urgence."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="01/20/2016"
 ms.author="elioda"/>

# Haute disponibilité et récupération d’urgence IoT Hub :

En tant que service Azure, IoT Hub offre la haute disponibilité à l’aide de redondances au niveau de la région Azure, sans aucun travail supplémentaire requis par la solution. En outre, Azure offre certaines fonctions qui aident à construire des solutions avec des fonctionnalités de récupération d’urgence ou disponibilité inter-régions si nécessaire. Vous devez concevoir et préparer vos solutions de manière à tirer parti de ces fonctionnalités afin de fournir une haute disponibilité globale inter-régions aux appareils et à l’utilisateur. L’article [Guide technique pour la continuité des activités Azure][] décrit les fonctionnalités intégrées Azure permettant la continuité d’activité et la récupération d’urgence. Le document [Récupération d’urgence et haute disponibilité des applications Azure][] contient des recommandations en matière d’architecture sur les stratégies pour les applications Azure permettant de bénéficier de la haute disponibilité et de la récupération d’urgence.

## Basculement régional avec IoT Hub

Un traitement complet des topologies de déploiement dans les solutions IoT est hors du propos de cet article,mais, pour les besoins de la haute disponibilité et de la récupération d’urgence, nous allons prendre en compte le modèle de déploiement *basculement régional*.

Dans un modèle de basculement régional, le serveur principal de solution s’exécute principalement sur un site de centre de données, mais un concentrateur IoT et le serveur principal supplémentaire seront déployés dans un autre emplacement de centre de données à des fins de basculement, au cas où le concentrateur IoT du centre de données principal subirait une panne ou si la connectivité réseau du centre de données principal était coupée. Les périphériques utilisent un point de terminaison secondaire à chaque fois que la passerelle principale ne peut pas être atteinte. Grâce à la capacité de basculement entre régions, il est possible d’améliorer la disponibilité de la solution au-delà de la haute disponibilité au niveau d’une seule région.

À un niveau élevé, pour implémenter un modèle de basculement régional IoT Hub, vous aurez besoin des éléments suivants.

* **Un concentrateur IoT secondaire et un périphérique logique de routage secondaire** : en cas d’interruption de service dans votre région principale, les périphériques doivent commencer à se connecter à votre région secondaire. Étant donné l’état de la plupart des services impliqués, il est courant pour les administrateurs de solution de déclencher le processus de basculement inter-régions. La meilleure façon de communiquer le nouveau point de terminaison aux appareils tout en conservant le contrôle du processus consiste à consulter régulièrement un service de *conciergerie* pour connaître le point de terminaison actif en cours. Le service de concierge peut être une simple application web répliquée accessible à l’aide de techniques de redirection DNS (par exemple, l’utilisation d’[Azure Traffic Manager][]).
* **Réplication du registre d’identité** : pour être utilisable, le concentrateur IoT secondaire doit contenir toutes les identités d’appareils devant être en mesure de se connecter à la solution. La solution doit conserver des sauvegardes géo-répliquées d’identités d’appareils et les télécharger dans le concentrateur IoT secondaire avant de basculer le point de terminaison actif des appareils. La fonctionnalité d’exportation d’identité de périphérique IoT Hub est très utile dans ce contexte. Pour plus d’informations, consultez [Guide du développeur IoT Hub - Registre d’identité][].
* **Fusion logique** : lorsque la région principale redevient disponible, l’ensemble des états et des données qui ont été créés dans le site secondaire doit revenir à la région primaire. Cette opération est fonction des identités d’appareil et des métadonnées d’application, qui doivent être fusionnées avec le concentrateur IoT principal et d’autres magasins spécifiques à l’application dans la région primaire. Pour simplifier cette opération, il est généralement recommandé d’utiliser des opérations idempotentes. Cela réduit les effets secondaires d’une éventuelle distribution continue d’événements, mais également ceux des doublons ou de la livraison d’événements hors service. En outre, la logique d’application doit être conçue pour tolérer les éventuelles incohérences ou de légers retards. Cela est dû au temps additionnel que le système prend pour se réparer ou en fonction des objectifs de points de récupération (RPO). L’article qui suit fournit d’autres recommandations sur ce sujet : [Prévention de défaillance : Guide des architectures cloud résilientes][].

## Étapes suivantes

Suivez ces liens pour en savoir plus sur Azure IoT Hub :

- [Prise en main des IoT Hubs (didacticiel).][lnk-get-started]
- [Qu’est-ce qu’Azure IoT Hub ?][]

[Guide technique pour la continuité des activités Azure]: https://msdn.microsoft.com/library/azure/hh873027.aspx
[Récupération d’urgence et haute disponibilité des applications Azure]: https://msdn.microsoft.com/library/azure/dn251004.aspx
[Prévention de défaillance : Guide des architectures cloud résilientes]: https://msdn.microsoft.com/library/azure/jj853352.aspx
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[Guide du développeur IoT Hub - Registre d’identité]: iot-hub-devguide.md#identityregistry

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Qu’est-ce qu’Azure IoT Hub ?]: iot-hub-what-is-iot-hub.md

<!---HONumber=AcomDC_0121_2016-->