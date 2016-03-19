<properties 
	pageTitle="Connexion sécurisée à des ressources de backend à partir d’un environnement App Service" 
	description="Découvrez comment connecter de façon sécurisée des ressources de backend à partir d'un environnement App Service." 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2016" 
	ms.author="stefsch"/>

# Connexion sécurisée à des ressources de backend à partir d'un environnement App Service #

## Vue d’ensemble ##
Étant donné qu’un environnement App Service est toujours créé dans un sous-réseau d’un [réseau virtuel][virtualnetwork] régional « v1 » classique, les connexions sortantes d’un environnement App Service à destination d’autres ressources de back-end peuvent passer exclusivement sur le réseau virtuel.

**Remarque :** un environnement App Service ne peut pas être créé sur un réseau virtuel géré par ARM « v2 ».

Par exemple, un serveur SQL Server peut être en cours d'exécution sur un cluster de machines virtuelles dont le port 1433 est verrouillé. Le point de terminaison peut être placé dans une liste de contrôle d'accès pour autoriser uniquement l'accès à partir d'autres ressources se trouvant sur le même réseau virtuel.

De même, les points de terminaison sensibles peuvent s'exécuter localement et être connectés à Azure via des connexions [de site à site][SiteToSite] ou [Azure ExpressRoute][ExpressRoute]. Par conséquent, seules les ressources des réseaux virtuels connectés aux tunnels site à site ou ExpressRoute peuvent accéder aux points de terminaison locaux.

Pour tous ces scénarios, les applications s'exécutant dans un environnement App Service peuvent se connecter de façon sécurisée aux différents serveurs et aux différentes ressources. Le trafic sortant à partir d'applications qui s'exécutent dans un environnement App Service vers des points de terminaison privés se trouvant sur le même réseau virtuel (ou connectés au même réseau virtuel) circulent uniquement sur le réseau virtuel. Le trafic sortant vers des points de terminaison privés ne circule pas via le réseau Internet public.

L’inconvénient est que le trafic sortant à partir d’un environnement App Service circule vers des points de terminaison se trouvant sur un réseau virtuel . Les environnements App Service ne peuvent pas atteindre les points de terminaison de machines virtuelles situées dans le **même** sous-réseau que l'environnement App Service. Cela ne devrait normalement pas poser de problème tant que les environnements App Service sont déployés dans un sous-réseau réservé à un usage exclusif par l'environnement App Service.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Connectivité sortante et configuration DNS requise ##
Notez que pour qu’un environnement App Service fonctionne correctement, il a besoin d’un accès sortant vers Azure Storage à l’échelon mondial, ainsi que d’une connectivité vers la base de données SQL de la même région Azure. Si l'accès Internet sortant est bloqué sur le réseau virtuel, les environnements App Service ne pourront pas accéder à ces points de terminaison Azure.

Le client peut également avoir des serveurs DNS personnalisés configurés sur le réseau virtuel. Les environnements App Service doivent être en mesure de résoudre les systèmes d’extrémité Azure sous *.database.windows.net, *.file.core.windows.net et *.blob.core.windows.net.

Il est également recommandé de configurer les serveurs DNS personnalisés sur le réseau virtuel à l'avance, avant de créer un environnement App Service. Si la configuration DNS d'un réseau virtuel est modifiée pendant la création d'un environnement App Service, alors le processus de création de l'environnement App Service échouera. S’il existe un serveur DNS personnalisé à l’autre extrémité d’une passerelle VPN et que le serveur DNS n’est pas accessible ou disponible, le processus de création d’un environnement App Service échouera également.

## Connexion à un serveur SQL Server
Une configuration courante de SQL Server comprend un point de terminaison qui écoute sur le port 1433 :

![Point de terminaison de SQL Server][SqlServerEndpoint]

Pour limiter le trafic sur ce point de terminaison, vous avez le choix entre deux approches :


- [Listes de contrôle d'accès réseau][NetworkAccessControlLists] (ACL réseau)

- [Groupes de sécurité réseau][NetworkSecurityGroups]


## Restriction de l'accès à l'aide d'une ACL réseau

Le port 1433 peut être sécurisé à l'aide d'une liste de contrôle d'accès réseau. L'exemple ci-dessous place dans une liste approuvée les adresses de clients provenant d'un réseau virtuel, et bloque l'accès à tous les autres clients.

![Exemple de liste de contrôle d'accès réseau][NetworkAccessControlListExample]

Toutes les applications s'exécutant dans un environnement App Service du même réseau virtuel que le serveur SQL Server peuvent se connecter à l'instance SQL Server à l'aide de l'adresse IP de **réseau virtuel interne** de la machine virtuelle SQL Server.

L'exemple de chaîne de connexion ci-dessous fait référence au serveur SQL Server en utilisant son adresse IP privée.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Même si la machine virtuelle possède également un point de terminaison public, les tentatives de connexion à l'aide de l'adresse IP publique sont rejetées en raison de l'ACL réseau.

## Restriction de l'accès à l'aide d'un groupe de sécurité réseau
Un groupe de sécurité réseau constitue une autre approche de sécurisation de l'accès. Les groupes de sécurité réseau peuvent être appliqués à des machines virtuelles individuelles ou à un sous-réseau comprenant des machines virtuelles.

Un groupe de sécurité réseau doit tout d'abord être créé :

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Il est très simple de restreindre l'accès exclusivement au trafic d'un réseau virtuel interne à l'aide d'un groupe de sécurité réseau. Les règles par défaut d'un groupe de sécurité réseau autorisent l'accès uniquement à partir d'autres clients réseau du même réseau virtuel.

Par conséquent, il est aussi simple de verrouiller l'accès à SQL Server que d'appliquer un groupe de sécurité réseau avec ses règles par défaut aux machines virtuelles exécutant SQL Server ou au sous-réseau comprenant des machines virtuelles.

L'exemple ci-dessous applique un groupe de sécurité réseau au sous-réseau conteneur :

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
    
Le résultat final est un ensemble de règles de sécurité qui bloquent l'accès externe, tout en autorisant l'accès au réseau virtuel interne :

![Règles de sécurité réseau par défaut][DefaultNetworkSecurityRules]


## Prise en main

Pour prendre en main les environnements App Service, consultez [Présentation de l'environnement App Service][IntroToAppServiceEnvironment].

Pour plus d'informations sur le contrôle du trafic entrant vers votre environnement App Service, consultez [Contrôle du trafic entrant vers un environnement App Service][ControlInboundASE].

Pour plus d’informations sur la plateforme Azure App Service, consultez la rubrique [Azure App Service][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ControlInboundASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png

<!---HONumber=AcomDC_0211_2016-->