<properties 
	pageTitle="Présentation de l'architecture réseau des environnements App Service" 
	description="Présentation de l'architecture de la topologie de réseau des environnements App Service." 
	services="app-service\web" 
	documentationCenter="" 
	authors="stefsch" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/06/2015" 
	ms.author="stefsch"/>

# Présentation de l'architecture réseau des environnements App Service

## Introduction ##
Les environnements App Service sont toujours créés dans un sous-réseau d'un [réseau virtuel][virtualnetwork]. Les applications exécutées dans un environnement App Service peuvent communiquer avec des points de terminaison privés au sein de la même topologie de réseau virtuel. Dans la mesure où les clients peuvent verrouiller des parties de leur infrastructure de réseau virtuel, il est important de comprendre les types de flux de communication réseau qui se produisent avec un environnement App Service.

## Flux réseau général ##
 
Un environnement App Service a toujours une adresse IP virtuelle publique (VIP). Tout le trafic entrant arrive sur cette adresse VIP publique, y compris le trafic HTTP et HTTPS pour les applications, ainsi que le reste du trafic pour les opérations de gestion Azure, les fonctionnalités de débogage à distance et FTP. Pour obtenir une liste complète des ports spécifiques (obligatoires et facultatifs) disponibles sur l'adresse VIP publique, consultez l'article sur le [contrôle du trafic entrant][controllinginboundtraffic] dans un environnement App Service.

Le diagramme suivant montre une vue d'ensemble des différents flux réseau entrants et sortants :

![Flux de réseau généraux][GeneralNetworkFlows]

Un environnement App Service peut communiquer avec plusieurs points de terminaison client privés. Par exemple, les applications exécutées dans l'environnement App Service peuvent se connecter au(x) serveur(s) de base de données en cours d'exécution sur des machines virtuelles IaaS dans la même topologie de réseau virtuel.

Les environnements App Service communiquent également avec les ressources BD SQL et stockage Azure nécessaires pour la gestion et l'exploitation d'un environnement App Service. Certaines des ressources de stockage et Sql avec lesquelles un environnement App Service communique se trouvent dans la même région que l'environnement App Service, tandis que d'autres sont situées dans des régions Azure distantes. Par conséquent, une connectivité sortante à Internet est toujours requise pour qu'un environnement App Service fonctionne correctement.

Étant donné qu'un environnement App Service est déployé dans un sous-réseau, vous pouvez utiliser des groupes de sécurité réseau pour contrôler le trafic entrant vers le sous-réseau. Pour plus d'informations sur la façon de contrôler le trafic entrant vers un environnement App Service, consultez l'[article][controllinginboundtraffic] suivant.

Pour plus d'informations sur la façon d'autoriser la connectivité Internet sortante à partir d'un environnement App Service, consultez l'article suivant sur l'utilisation d'[ExpressRoute][ExpressRoute]. La même approche que celle décrite dans cet article s'applique avec une connectivité de site à site et l'utilisation du tunneling forcé.

## Adresses réseau sortantes ##
Lorsqu'un environnement App Service effectue des appels sortants, une adresse IP est toujours associée aux appels sortants. L'adresse IP spécifique utilisée varie suivant si le point de terminaison appelé se trouve dans la topologie de réseau virtuel ou en dehors de la topologie de réseau virtuel.

Si le point de terminaison appelé est **en dehors** de la topologie de réseau virtuel, alors l'adresse sortante (également appelé adresse NAT sortante) utilisée est l'adresse VIP publique de l'environnement App Service. Cette adresse se trouve dans l'interface utilisateur du portail de l'environnement App Service (remarque : UX en attente).

Il est également possible de déterminer cette adresse en créant une application dans l'environnement App Service, puis en effectuant une opération *nslookup* sur l'adresse de l'application. L'adresse IP obtenue est à la fois l'adresse VIP publique et l'adresse NAT sortante de l'environnement App Service.

Si le point de terminaison appelé est **dans** la topologie de réseau virtuel, l'adresse sortante de l'application appelante sera l'adresse IP interne de la ressource de calcul individuelle exécutant l'application. Toutefois, il n'existe pas de mappage persistant des adresses IP internes du réseau virtuel aux applications. Les applications peuvent se déplacer entre différentes ressources de calcul et le pool de ressources de calcul disponibles dans un environnement App Service peut changer en raison d'opérations de mise à l'échelle.

Toutefois, dans la mesure où un environnement App Service est toujours situé dans un sous-réseau, vous pouvez être sûr que l'adresse IP interne d'une ressource de calcul exécutant une application sera toujours comprise dans la plage CIDR du sous-réseau. Par conséquent, si des listes de contrôle d'accès précises ou des groupes de sécurité réseau sont utilisés pour sécuriser l'accès à d'autres points de terminaison dans le réseau virtuel, l'accès doit être accordé à la plage du sous-réseau contenant l'environnement App Service.

Le diagramme suivant illustre ces concepts plus en détail :

![Adresses réseau sortantes][OutboundNetworkAddresses]

Dans le diagramme ci-dessus :

- Étant donné que l'adresse VIP publique de l'environnement App Service est 192.23.1.2, cela correspond à l'adresse IP sortante utilisée lors des appels aux points de terminaison « Internet ».
- La plage CIDR du sous-réseau conteneur pour l'environnement App Service est 10.0.1.0/26. Les autres points de terminaison dans la même infrastructure de réseau virtuel verront les appels provenant d'applications comme émanant de quelque part dans cette plage d'adresses.

## Informations et liens supplémentaires ##
Vous trouverez plus d'informations sur les ports entrants utilisés par les environnements App Service et l'utilisation de groupes de sécurité réseau pour contrôler le trafic entrant [ici][controllinginboundtraffic].

Cet [article][ExpressRoute] contient des informations sur l'utilisation d'itinéraires définis par l'utilisateur pour accorder un accès Internet sortant à des environnements App Service.


<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png

<!---HONumber=July15_HO4-->