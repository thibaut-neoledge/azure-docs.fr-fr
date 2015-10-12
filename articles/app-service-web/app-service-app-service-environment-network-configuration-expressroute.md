<properties 
	pageTitle="Détails de la configuration réseau pour travailler avec ExpressRoute" 
	description="Détails de la configuration réseau pour exécuter des environnements App Service sur des réseaux virtuels connectés à un circuit ExpressRoute." 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="nirma" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/11/2015" 
	ms.author="stefsch"/>

# Détails de la configuration réseau pour les environnements App Service avec ExpressRoute 

## Vue d'ensemble ##
Les clients peuvent connecter un circuit [Azure ExpressRoute][ExpressRoute] à leur infrastructure de réseau virtuel pour étendre leur réseau local à Azure. Vous pouvez créer un environnement App Service dans un sous-réseau de cette infrastructure de [réseau virtuel][virtualnetwork]. Les applications exécutées dans l'environnement App Service peuvent alors établir des connexions sécurisées à des ressources principales accessibles uniquement par le biais de la connexion ExpressRoute.

**Remarque :** un environnement App Service ne peut pas être créé sur un réseau virtuel « v2 ». Pour l’heure, les environnements App Service sont pris en charge uniquement sur les réseaux virtuels « v1 » classiques.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Connectivité réseau requise ##
Il existe des exigences de connectivité réseau pour les environnements App Service qui peuvent ne pas être initialement satisfaites dans un réseau virtuel connecté à ExpressRoute.

Les environnements App Service requièrent tous les éléments suivants pour fonctionner correctement :


-  Une connectivité réseau sortante vers Azure Storage à l’échelon mondial et une connectivité vers des ressources de base de données SQL situées dans la même région que l'environnement App Service. Ce chemin d'accès réseau ne peut pas transiter par des proxys d'entreprise internes, car cela est susceptible de changer l'adresse NAT réelle du trafic réseau sortant. La modification de l'adresse NAT du trafic réseau sortant d'un environnement App Service dirigé vers des points de terminaison de stockage Azure et de base de données SQL entraîne des échecs de connectivité.
-  La configuration DNS du réseau virtuel doit être capable de résoudre les systèmes d’extrémité dans les domaines contrôlés par Azure suivants : **.file.core.windows.net*, **.blob.core.windows.net*, **.database.windows.net*.
-  La configuration DNS du réseau virtuel doit rester stable pendant la création des environnements App Service, ainsi que pendant les reconfigurations et les modifications de mise à l'échelle des environnements App Service.   
-  S’il existe un serveur DNS personnalisé à l’autre extrémité d’une passerelle VPN, le serveur DNS doit être accessible et disponible. 
-  L’accès réseau entrant vers les ports requis pour les environnements App Service doit être autorisé, comme décrit dans cet [article][requiredports].

Les exigences DNS peuvent être satisfaites par le biais d'une configuration DNS valide pour le réseau virtuel.

Les exigences d’accès réseau entrant peuvent être satisfaites en configurant un [groupe de sécurité réseau][NetworkSecurityGroups] sur le sous-réseau de l’environnement App Service, afin d’autoriser l’accès requis, comme décrit dans cet [article][requiredports].

## Activation de la connectivité réseau sortante pour un environnement App Service##
Par défaut, un circuit ExpressRoute nouvellement créé publie un itinéraire par défaut qui autorise la connectivité Internet sortante. Avec cette configuration, un environnement App Service sera en mesure de se connecter à d'autres points de terminaison Azure.

Toutefois, une configuration client courante consiste à définir un itinéraire par défaut, ce qui force le trafic Internet sortant à évoluer sur site par le biais de l'infrastructure de pare-feu/proxy d'un client. Ce flux de trafic décompose invariablement les environnements App Service, car le trafic sortant est soit bloqué sur site, soit fait l'objet d'une opération NAT sur un jeu d'adresses non reconnaissable qui ne fonctionne plus avec différents points de terminaison Azure.

La solution consiste à définir un (ou plusieurs) itinéraires définis par l'utilisateur (UDR) sur le sous-réseau qui contient l'environnement App Service. Un itinéraire défini par l'utilisateur définit des itinéraires spécifiques au sous-réseau qui seront respectés au lieu de l'itinéraire par défaut.

Vous trouverez des informations générales sur les itinéraires définis par l’utilisateur dans cette [présentation][UDROverview].

Pour plus d’informations sur la création et la configuration d’itinéraires définis par l’utilisateur, consultez ce [guide][UDRHowTo].

## Exemple de configuration d'itinéraire défini par l'utilisateur pour un environnement App Service ##

**Conditions préalables**

1. Installez la toute dernière version d’Azure Powershell à partir de la [page Téléchargements Azure][AzureDownloads] (datant de juin 2015 ou version ultérieure). Sous « Outils de ligne de commande », un lien « Installer » sous « Windows Powershell » permet d'installer les dernières applets de commande Powershell.

2. Nous vous recommandons de créer un sous-réseau unique pour un usage exclusif par un environnement App Service. Ceci garantit que les itinéraires définis par l'utilisateur appliqués au sous-réseau ouvriront uniquement le trafic sortant pour l'environnement App Service.
3. **Important** : ne déployez pas l’environnement App Service **avant** d’avoir exécuté les étapes de configuration suivantes. Ceci garantit que la connectivité réseau sortante est disponible avant la tentative de déploiement d'un environnement App Service.

**Étape 1 : Créer une table d'itinéraires nommée**

L'extrait de code suivant crée une table d'itinéraires appelée « DirectInternetRouteTable » dans la région Azure Ouest des États-Unis :

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Étape 2 : Créer un ou plusieurs itinéraires dans la table**

Vous devrez ajouter un ou plusieurs itinéraires à la table afin d'activer l'accès Internet sortant. L'exemple suivant ajoute suffisamment d'itinéraires pour couvrir toutes les adresses Azure possibles utilisées dans la région Ouest des États-Unis.

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 1' -AddressPrefix 23.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 2' -AddressPrefix 40.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 3' -AddressPrefix 65.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 4' -AddressPrefix 104.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 5' -AddressPrefix 137.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 6' -AddressPrefix 138.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 7' -AddressPrefix 157.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 8' -AddressPrefix 168.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 9' -AddressPrefix 191.0.0.0/8 -NextHopType Internet


Pour obtenir la liste complète et mise à jour des plages CIDR utilisées par Azure, vous pouvez télécharger un fichier XML contenant toutes les plages à partir du [Centre de téléchargement Microsoft][DownloadCenterAddressRanges]

**Remarque :** à un moment donné, une abréviation CIDR de 0.0.0.0/0 sera disponible pour une utilisation dans le paramètre *AddressPrefix*. Cette abréviation équivaut à « toutes les adresses Internet ». Pour le moment, les développeurs devront utiliser à la place un large éventail de plages CIDR suffisant pour couvrir toutes les plages d’adresses Azure possibles.

**Étape 3 : Associer la table d'itinéraires au sous-réseau contenant l'environnement App Service**

La dernière étape de configuration consiste à associer la table d'itinéraires au sous-réseau où l'environnement App Service sera déployé. La commande suivante associe « DirectInternetRouteTable » à « ASESubnet » qui contiendra un environnement App Service.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Étape 4 : Étapes finales**

Une fois que la table d'itinéraires est liée au sous-réseau, nous vous recommandons de tester et confirmer le résultat souhaité. Par exemple, déployez une machine virtuelle sur le sous-réseau et vérifiez que :


- Le trafic sortant vers les points de terminaison Azure n'est pas transmis vers le circuit ExpressRoute.
- Les recherches DNS des points de terminaison Azure sont résolues correctement. 

Une fois les étapes ci-dessus confirmées, vous pouvez supprimer la machine virtuelle et procéder ensuite à la création d’un environnement App Service.

## Prise en main

Pour prendre en main les environnements App Service, consultez [Présentation de l'environnement App Service][IntroToAppServiceEnvironment].

Pour plus d’informations sur la plateforme Azure App Service, consultez la rubrique [Azure App Service][AzureAppService].

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: http://azure.microsoft.com/downloads/
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
 

<!-- IMAGES -->

<!---HONumber=Oct15_HO1-->