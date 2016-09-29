<properties
   pageTitle="Introduction à Application Gateway | Microsoft Azure"
   description="Cette page offre une vue d’ensemble du service Application Gateway pour l'équilibrage de charge de couche 7, notamment les tailles de passerelle, l’équilibrage de charge HTTP, l’affinité de session basée sur les cookies et le déchargement SSL."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="gwallace"/>

# Vue d’ensemble d’Application Gateway

Microsoft Azure Application Gateway propose un service d’équilibrage de charge HTTP géré par Azure et basé sur l’équilibrage de charge de couche 7. Plus simplement, Application Gateway fonctionne en acceptant le trafic et en se basant sur des règles définies avec lui, pour acheminer le trafic vers les instances back-end appropriées.

L’équilibrage de la charge d’application permet aux administrateurs informatiques et aux développeurs de créer des règles de routage pour le trafic réseau basé sur le protocole HTTP. Le service Application Gateway est hautement disponible et contrôlé. Pour connaître le contrat SLA et les prix appliqués, consultez les pages [SLA](https://azure.microsoft.com/support/legal/sla/) et [Prix appliqués](https://azure.microsoft.com/pricing/details/application-gateway/).

Application Gateway prend actuellement en charge la remise d’application de couche 7 avec les fonctionnalités suivantes :

- **Équilibrage de charge HTTP** : la fonctionnalité principale de la passerelle Application Gateway est de fournir un service d’équilibrage de charge. L’équilibrage de charge s’effectue à la couche 7 et est utilisé pour le trafic HTTP uniquement.
- **Affinité de session basée sur les cookies** : cette fonctionnalité est utile lorsque vous souhaitez conserver une session utilisateur sur le même serveur principal. En utilisant les cookies, la passerelle Application Gateway peut par la suite diriger le trafic pour traitement à partir d’une session utilisateur vers le même serveur principal. Cette fonctionnalité est importante lorsqu’un élément est enregistré localement sur le serveur principal pour une session utilisateur.
- **[Déchargement SSL (Secure Sockets Layer)](application-gateway-ssl-arm.md)** : cette fonctionnalité supprime la tâche coûteuse de déchiffrement du trafic HTTPS de vos serveurs web. En arrêtant la connexion SSL au niveau de la passerelle Application Gateway et en transmettant la requête non chiffrée au serveur web, ce dernier n’est plus chargé du déchiffrement. La passerelle Application Gateway chiffre de nouveau la réponse avant de la renvoyer au client. Cette fonctionnalité est utile lorsque le serveur principal se trouve dans le même réseau virtuel sécurisé que la passerelle Application Gateway dans Azure.
- **[Routage du contenu en fonction de l’URL](application-gateway-url-route-overview.md)** : cette fonctionnalité offre la possibilité d’utiliser différents serveurs principaux pour plusieurs trafics. Le trafic d’un CDN ou d’un dossier sur le serveur web peut être acheminé vers un autre serveur principal, ce qui réduit la charge inutile sur les serveurs principaux qui n’ont pas besoin d’un contenu spécifique.
- **[Routage multi-sites](application-gateway-multi-site-overview.md)** : la passerelle Application Gateway permet de consolider jusqu’à 20 sites Web sur une passerelle unique.
- **[Prise en charge de WebSocket](application-gateway-websocket.md)** : une autre fonctionnalité intéressante de la passerelle Application Gateway est la prise en charge native de WebSocket.


## Équilibrage de charge de couche 7 HTTP

La passerelle Application Gateway applique ensuite les règles de routage au trafic HTTP, fournissant ainsi un équilibrage de la charge de niveau 7 (HTTP). Quand vous créez une passerelle Application Gateway, un point de terminaison (VIP) est associé et utilisé comme adresse IP publique pour le trafic réseau en entrée. Azure fournit un équilibrage de la charge de couche 4 via l'équilibreur de charge Azure fonctionnant au niveau du transport (TCP/UDP) et via l'équilibrage de la charge de tout le trafic réseau entrant pour le service Application Gateway. La passerelle Application Gateway achemine le trafic HTTP en fonction de sa configuration, qu’il s’agisse d’un ordinateur virtuel, d’un service cloud, d’une application web ou d’une adresse IP externe.

L'équilibrage de charge de couche 7 HTTP est utile pour :

- Les applications pour lesquelles les requêtes provenant d’une même session utilisateur/client doivent atteindre la même machine virtuelle back-end. Exemples d’applications : panier d’achat et serveurs de messagerie.
- Les applications qui veulent libérer les batteries de serveurs web de la surcharge de terminaison SSL.
- Les applications, telles qu’un réseau de distribution de contenu, qui exigent le routage ou l’équilibrage de charge sur différents serveurs principaux des multiples requêtes HTTP sur une même connexion TCP de longue durée.
- Les applications qui prennent en charge le trafic WebSocket, mais qui ont un point de terminaison http pour la surveillance.

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## Instances et tailles de passerelle

Application Gateway est actuellement disponible en 3 tailles : Petit, Moyen et Grand. Les instances de petite taille sont conçues pour les scénarios de développement et de test.

Vous pouvez créer jusqu'à 50 passerelles d’application par abonnement et chacune peut contenir jusqu’à 10 instances. L’équilibrage de charge Application Gateway sous forme de service géré par Azure permet l’approvisionnement d’un équilibreur de charge de couche 7 derrière l’équilibreur de charge logiciel Azure.

Le tableau suivant présente un débit moyen de performances pour chaque instance d'Application Gateway :

| Réponse de la page principale | Petite | Moyenne | Grande|
|---|---|---|---|
| 6 K | 7,5 Mbits/s | 13 Mbits/s | 50 Mbits/s |
|100 K | 35 Mbits/s | 100 Mbits/s| 200 Mbits/s |

>[AZURE.NOTE] Ces valeurs sont des valeurs approximatives pour un débit de passerelle d’application. Le débit réel dépend de divers détails d’environnement, tels que la taille de page moyenne, l’emplacement des instances de serveur principal et le temps de traitement d’une page par le serveur.

## Surveillance de l’intégrité

Azure Application Gateway surveille automatiquement l’intégrité des instances de serveur principal par le biais de sondes d’intégrité de base ou personnalisées. Pour plus d’informations, consultez [Vue d’ensemble de l’analyse d’intégrité Application Gateway](application-gateway-probe-overview.md).

## Configuration et gestion

Le point de terminaison de la passerelle Application Gateway peut être une adresse IP publique, une adresse IP privée ou les deux. La passerelle Application Gateway est configurée à l’intérieur d’un réseau virtuel dans son propre sous-réseau. Le sous-réseau créé ou utilisé pour la passerelle Application Gateway ne peut pas contenir d’autres types de ressources. Les seules ressources autorisées dans le sous-réseau sont d’autres passerelles Application Gateway.

Vous pouvez créer et gérer une passerelle Application Gateway à l’aide des API REST, des applets de commande PowerShell, de la ligne de commande Azure ou du [portail Azure](https://portal.azure.com/).

## Étapes suivantes

À présent que vous êtes familiarisé avec Application Gateway, vous pouvez [créer une passerelle d’application](application-gateway-create-gateway-portal.md) ou [configurer une passerelle d’application pour le déchargement SSL](application-gateway-ssl-arm.md) de manière à équilibrer les charges des connexions HTTPS.

Pour savoir comment créer une passerelle d’application avec le routage de contenu basé sur une URL, consultez [Créer une passerelle d’application à l’aide du routage en fonction de l’URL](application-gateway-create-url-route-arm-ps.md).

<!---HONumber=AcomDC_0914_2016-->