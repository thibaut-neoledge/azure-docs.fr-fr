---
title: Contrôle du trafic entrant dans un environnement App Service
description: Découvrez comment configurer des règles de sécurité réseau pour contrôler le trafic entrant vers un environnement App Service.
services: app-service
documentationcenter: ''
author: ccompy
manager: wpickett
editor: ''

ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2016
ms.author: stefsch

---
# Contrôle du trafic entrant vers un environnement App Service
## Vue d'ensemble
Un environnement App Service peut être créé **soit** dans un réseau virtuel Azure Resource Manager, **ou** dans un [réseau virtuel][virtualnetwork] de modèle de déploiement classique. Il est possible de définir un nouveau réseau virtuel et un nouveau sous-réseau au moment de la création d'un environnement App Service. Vous pouvez également créer un environnement App Service dans un réseau virtuel préexistant et un sous-réseau préexistant. Grâce à une modification récente effectuée en juin 2016, les ASE peuvent désormais être déployés dans les réseaux virtuels qui utilisent soit des plages d’adresses publiques soit des espaces d’adressage RFC1918 (par exemple, des adresses privées). Pour plus de détails sur la création d'un environnement App Service, consultez [Création d’un environnement App Service][HowToCreateAnAppServiceEnvironment].

Un environnement App Service doit toujours être créé dans un sous-réseau, car un sous-réseau fournit une limite réseau qui peut être utilisée pour verrouiller le trafic entrant derrière des appareils et des services en amont, de sorte que le trafic HTTP et HTTPS soit accepté uniquement à partir d'adresses IP en amont.

Le trafic réseau entrant et sortant sur un sous-réseau est contrôlé à l'aide d'un [groupe de sécurité réseau][NetworkSecurityGroups]. Le contrôle du trafic entrant requiert la création de règles de sécurité réseau dans un groupe de sécurité réseau, ainsi que l'affectation du sous-réseau contenant l'environnement App Service à ce groupe de sécurité réseau.

Lorsqu'un groupe de sécurité réseau est affecté à un sous-réseau, le trafic entrant vers des applications de l'environnement App Service est autorisé/bloqués en fonction des règles d'autorisation et de refus définies dans le groupe de sécurité réseau.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Ports réseau dans un environnement App Service
Avant de verrouiller le trafic réseau entrant à l'aide d'un groupe de sécurité réseau, il est important de connaître l'ensemble de ports réseau obligatoires et facultatifs utilisés par un environnement App Service. La fermeture accidentelle du trafic vers certains ports peut entraîner une perte de fonctionnalités dans un environnement App Service.

La liste suivante présente les ports utilisés par un environnement App Service :

* 454 : **Port obligatoire** utilisé par l'infrastructure Azure pour la gestion et la maintenance des environnements App Service par le biais de SSL. Ne bloquez pas le trafic vers ce port. Ce port est toujours lié à l’adresse IP virtuelle publique d’un ASE.
* 455 : **Port obligatoire** utilisé par l'infrastructure Azure pour la gestion et la maintenance des environnements App Service par le biais de SSL. Ne bloquez pas le trafic vers ce port. Ce port est toujours lié à l’adresse IP virtuelle publique d’un ASE.
* 80 : Port par défaut pour le trafic HTTP entrant vers des applications s'exécutant dans plans App Service d'un environnement App Service. Sur un ASE avec équilibrage de charge interne, ce port est lié à l’adresse d’équilibrage de charge interne de l’ASE.
* 443 : port par défaut pour le trafic SSL entrant vers des applications s'exécutant dans plans App Service d'un environnement App Service. Sur un ASE avec équilibrage de charge interne, ce port est lié à l’adresse d’équilibrage de charge interne de l’ASE.
* 21 : Canal de contrôle pour FTP. Ce port peut être bloqué en toute sécurité si FTP n'est pas utilisé. Sur un ASE avec équilibrage de charge interne, ce port peut être lié à l’adresse d’équilibrage de charge interne d’un ASE.
* 990 : Canal de contrôle pour FTPS. Ce port peut être bloqué en toute sécurité si FTPS n’est pas utilisé. Sur un ASE avec équilibrage de charge interne, ce port peut être lié à l’adresse d’équilibrage de charge interne d’un ASE.
* 10001-10020 : Canaux de données pour FTP. Comme avec le canal de contrôle, ces ports peuvent être bloqués en toute sécurité si FTP n’est pas utilisé. Sur un ASE avec équilibrage de charge interne, ce port peut être lié à l’adresse d’équilibrage de charge interne de l’ASE.
* 4016 : Utilisé pour le débogage à distance avec Visual Studio 2012. Ce port peut être bloqué en toute sécurité si la fonctionnalité n'est pas utilisée. Sur un ASE avec équilibrage de charge interne, ce port est lié à l’adresse d’équilibrage de charge interne de l’ASE.
* 4018 : Utilisé pour le débogage à distance avec Visual Studio 2013. Ce port peut être bloqué en toute sécurité si la fonctionnalité n'est pas utilisée. Sur un ASE avec équilibrage de charge interne, ce port est lié à l’adresse d’équilibrage de charge interne de l’ASE.
* 4020 : Utilisé pour le débogage à distance avec Visual Studio 2015. Ce port peut être bloqué en toute sécurité si la fonctionnalité n'est pas utilisée. Sur un ASE avec équilibrage de charge interne, ce port est lié à l’adresse d’équilibrage de charge interne de l’ASE.

## Connectivité sortante et configuration DNS requise
Pour qu’un environnement App Service fonctionne correctement, il requiert un accès sortant à différents points de terminaison. Une liste complète des points de terminaison externes utilisés par un ASE est disponible dans la section « Connectivité réseau requise » de l’article [Configuration réseau pour ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity).

Les environnements App Service nécessitent une infrastructure DNS valide configurée pour le réseau virtuel. Si, pour une raison quelconque, la configuration DNS est modifiée après la création d'un environnement App Service, les développeurs peuvent forcer un environnement App Service à récupérer la nouvelle configuration DNS. Le déclenchement d’un redémarrage d’un environnement propagé à l’aide de l’icône « Redémarrer » située en haut du panneau de gestion de l’environnement App Service du [portail Azure][NewPortal] force l’environnement à récupérer la nouvelle configuration DNS.

Il est également recommandé de configurer les serveurs DNS personnalisés sur le réseau virtuel à l'avance, avant de créer un environnement App Service. Si la configuration DNS d'un réseau virtuel est modifiée pendant la création d'un environnement App Service, alors le processus de création de l'environnement App Service échouera. De même, s’il existe un serveur DNS personnalisé à l’autre extrémité d’une passerelle VPN et que le serveur DNS n’est pas accessible ou disponible, le processus de création d’un environnement App Service échoue également.

## Création d'un groupe de sécurité réseau
Pour plus d'informations sur le fonctionnement des groupes de sécurité réseau, consultez les [informations][NetworkSecurityGroups] suivantes. Les informations détaillées ci-dessous abordent les points principaux des groupes de sécurité réseau, en se concentrant sur la configuration et l'application d'un groupe de sécurité réseau à un sous-réseau contenant un environnement App Service.

**Remarque :** les groupes de sécurité réseau peuvent être configurés sous forme graphique à l’aide du [portail Azure](https://portal.azure.com) ou via Azure PowerShell.

Les groupes de sécurité réseau sont tout d’abord créés en tant qu’entités autonomes associées à un abonnement. Dans la mesure où les groupes de sécurité réseau sont créés dans une région Azure, assurez-vous que le groupe de sécurité réseau est créé dans la même région que l'environnement App Service.

Voici un exemple de création d'un groupe de sécurité réseau :

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Une fois un groupe de sécurité réseau créé, une ou plusieurs règles de sécurité réseau lui sont ajoutées. Étant donné que l'ensemble de règles peut changer au fil du temps, il est recommandé d'espacer le modèle de numérotation utilisé pour les priorités des règles pour faciliter l'insertion de règles supplémentaires dans le temps.

L'exemple ci-dessous montre une règle qui accorde explicitement l'accès aux ports de gestion requis par l'infrastructure Azure pour gérer et maintenir un environnement App Service. Notez que tout le trafic de gestion transite sur SSL et est sécurisé par des certificats clients. Par conséquent, même si les ports sont ouverts, ils sont inaccessibles à toute entité autre que l'infrastructure de gestion Azure.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Lors du verrouillage de l'accès aux ports 80 et 443 pour « masquer » un environnement App Service derrière des appareils ou services en amont, vous devez connaître l'adresse IP en amont. Par exemple, si vous utilisez un pare-feu d'applications web (WAF), ce pare-feu aura sa propre adresse IP (ou ses propres adresses IP) qu'il utilise lors de l'acheminement du trafic proxy vers un environnement App Service d'application en aval. Vous devez utiliser cette adresse IP dans le paramètre *SourceAddressPrefix* d'une règle de sécurité réseau.

Dans l'exemple ci-dessous, le trafic entrant à partir d'une adresse IP en amont spécifique est explicitement autorisé. L'adresse *1.2.3.4* est utilisée comme un espace réservé pour l'adresse IP d'un pare-feu d'applications web (WAF) en amont. Modifiez la valeur pour la faire correspondre à l'adresse utilisée par votre service ou appareil en amont.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Si la prise en charge FTP est souhaitée, les règles suivantes peuvent être utilisées comme modèle pour accorder l'accès au port de contrôle FTP et aux ports du canal de contrôle. Étant donné que FTP est un protocole avec état, il se peut que vous ne puissiez pas acheminer le trafic FTP via un pare-feu HTTP/HTTPS ou un appareil proxy traditionnel. Dans ce cas, vous devez définir le paramètre *SourceAddressPrefix* sur une autre valeur (par exemple, la plage d'adresses IP de machines de développement ou de déploiement sur lesquelles s'exécutent les clients FTP.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Remarque :** la plage de ports du canal de données peut changer pendant la durée de la version préliminaire.)

Si le débogage à distance avec Visual Studio est utilisé, les règles suivantes montrent comment accorder l'accès. Il existe une règle distincte pour chaque version prise en charge de Visual Studio, car chaque version utilise un port différent pour le débogage à distance. Comme avec l'accès FTP, il est possible que le trafic du débogage à distance ne transite pas correctement via un pare-feu d'applications web (WAF) ou un appareil proxy traditionnel. À la place, le paramètre *SourceAddressPrefix* peut être défini sur la plage d'adresses IP des machines de développement exécutant Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## Affectation d'un groupe de sécurité réseau à un sous-réseau
Un groupe de sécurité réseau comporte une règle de sécurité par défaut qui refuse l'accès à tout le trafic externe. En résultat de la combinaison des règles de sécurité réseau décrites ci-dessus et de la règle de sécurité par défaut bloquant le trafic entrant, seul le trafic provenant de plages d'adresses sources associées à une action *Autoriser* pourra être envoyé vers des applications qui s'exécutent dans un environnement App Service.

Une fois un groupe de sécurité réseau rempli avec des règles de sécurité, il doit être affecté au sous-réseau contenant l'environnement App Service. La commande d'affectation fait référence à la fois au nom du réseau virtuel dans lequel réside l'environnement App Service et au nom du sous-réseau dans lequel l'environnement App Service a été créé.

L'exemple ci-dessous présente un groupe de sécurité réseau attribué à un sous-réseau et un réseau virtuel :

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Une fois l'affectation du groupe de sécurité réseau réussie (l'affectation est une opération longue qui peut prendre quelques minutes), seul le trafic entrant respectant les règles *Autoriser* peut atteindre des applications de l'environnement App Service.

Par souci d'exhaustivité, l'exemple suivant montre comment supprimer, et donc dissocier, le groupe de sécurité réseau du sous-réseau :

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## Considérations spécifiques concernant les adresses IP SSL explicites
Si une application est configurée avec une adresse IP explicite (applicable aux ASE qui n’ont qu’une adresse IP virtuelle publique), au lieu de l’adresse IP par défaut de l’environnement App Service, le trafic HTTP et HTTPS transite dans le sous-réseau via un ensemble de ports autres que les ports 80 et 443.

Vous trouverez la paire de ports utilisés par chaque adresse IP SSL individuelle dans l’interface utilisateur du portail, dans le panneau UX des détails de l’environnement App Service. Sélectionnez « Tous les paramètres » --> « Adresses IP ». Le panneau « Adresses IP » affiche un tableau de toutes les adresses IP SSL configurées explicitement pour l’environnement App Service, ainsi que la paire de ports spéciale utilisée pour acheminer le trafic HTTP et HTTPS associé à chaque adresse IP SSL. Il s’agit de la paire de ports à utiliser pour les paramètres DestinationPortRange lors de la configuration de règles dans un groupe de sécurité réseau.

Lorsqu’une application sur un environnement App Service est configurée pour utiliser IP-SSL, les clients externes ne voient pas et n’ont pas à se préoccuper du mappage spécial de la paire de ports. Le trafic vers les applications se dirigera normalement vers l’adresse IP-SSL configurée. La traduction automatique de la paire de port spéciale se produit en interne pendant la dernière phase du routage du trafic dans un sous-réseau contenant l’ASE.

## Prise en main
Pour prendre en main les environnements App Service, consultez [Présentation de l'environnement App Service][IntroToAppServiceEnvironment].

Tous les articles et procédures concernant les environnements App Service sont disponibles dans le [fichier Lisez-moi des environnements App Service](../app-service/app-service-app-service-environments-readme.md).

Pour plus d'informations sur les applications se connectant de manière sécurisée à des ressources de backend à partir d'un environnement App Service, consultez [Connexion sécurisée à des ressources de backend à partir d'un environnement App Service][SecurelyConnecttoBackend].

Pour plus d’informations sur la plateforme Azure App Service, consultez la rubrique [Azure App Service][AzureAppService].

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[SecurelyConnecttoBackend]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NewPortal]: https://portal.azure.com

<!-- IMAGES -->


<!---HONumber=AcomDC_0928_2016-->