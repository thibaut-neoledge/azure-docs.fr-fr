---
title: "Utilisation de la gestion des API Azure avec des réseaux virtuels"
description: "Découvrez comment configurer une connexion à un réseau virtuel dans Gestion des API Azure et accéder à des services web par son intermédiaire."
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 64b58f7b-ca22-47dc-89c0-f6bb0af27a48
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: f796177baaa5e03b833e5a2b300a98176147cd29
ms.contentlocale: fr-fr
ms.lasthandoff: 07/11/2017


---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Utilisation de la gestion des API Azure avec des réseaux virtuels
Les réseaux virtuels Azure vous permettent de placer vos ressources Azure dans un réseau routable non-Internet dont vous contrôlez l’accès. Ces réseaux peuvent ensuite être connectés à vos réseaux locaux à l’aide de différentes technologies VPN. Pour en savoir plus sur les réseaux virtuels Azure, commencez par consulter la page [Présentation du réseau virtuel](../virtual-network/virtual-networks-overview.md).

La gestion des API Azure peut être déployée à l’intérieur du réseau virtuel (VNET), pour qu’il puisse accéder aux services principaux au sein du réseau. Le portail des développeurs et la passerelle API peuvent être configurés pour être accessibles depuis Internet ou uniquement au sein du réseau virtuel.

> [!NOTE]
> La gestion des API Azure prend en charge les réseaux virtuels classiques et Azure Resource Manager.
>
>

## <a name="enable-vpn"> </a>Activer la connexion au réseau virtuel
> [!NOTE]
> La connectivité VNET est disponible dans les niveaux **Premium** et **Développeur**. Pour basculer entre les niveaux, ouvrez votre service de gestion des API dans le portail Azure, puis ouvrez l’onglet **Scale and pricing (Échelle et tarification)**. Dans la section **Niveau tarifaire**, sélectionnez le niveau Premium et cliquez sur Enregistrer.
>

Pour activer la connectivité VNET, ouvrez votre service de gestion des API dans le portail Azure ainsi que la page **Réseau virtuel**.

![Menu Réseau virtuel du service Gestion des API][api-management-using-vnet-menu]

Sélectionnez le type d’accès souhaité :

* **Externe** : la passerelle Gestion des API et le portail des développeurs sont accessibles à partir de l’Internet public via un équilibreur de charge externe. La passerelle peut accéder aux ressources au sein du réseau virtuel.

![Homologation publique][api-management-vnet-public]

* **Interne** : la passerelle Gestion des API et le portail des développeurs sont accessibles uniquement sur le réseau virtuel via un équilibreur de charge interne. La passerelle peut accéder aux ressources au sein du réseau virtuel.

![Homologation privée][api-management-vnet-private]

Vous voyez maintenant une liste de toutes les régions où votre service Gestion des API est créé. Sélectionnez un réseau VNET et un sous-réseau pour chaque région. La liste contient les réseaux virtuels classiques et Resource Manager, disponibles dans vos abonnements Azure, qui sont installés dans la région que vous configurez.

> [!NOTE]
> Le **point de terminaison de service** du diagramme ci-dessus inclut la passerelle/proxy, le portail des éditeurs, le portail des développeurs, le Git et le point de terminaison de gestion directe.
> Le **point de terminaison de gestion** du diagramme ci-dessus est le point de terminaison hébergé sur le service pour gérer la configuration via le portail Azure et Powershell.
> Veuillez également noter que, bien que le diagramme contient les adresses IP de ses différents points de terminaison, le service Gestion des API répond **uniquement** à ses noms d’hôtes configurés.

> [!IMPORTANT]
> Lorsque vous déployez une instance de la gestion des API Azure sur un réseau virtuel Resource Manager, le service doit se trouver dans un sous-réseau dédié qui ne contient aucune autre ressource à l’exception des instances de la gestion des API Azure. Si vous essayez de déployer une instance de gestion des API Azure sur un sous-réseau virtuel Resource Manager qui contient d’autres ressources, le déploiement échouera.
>
>

![Sélectionner le VPN][api-management-setup-vpn-select]

Cliquez sur **Enregistrer** dans la partie supérieure de l’écran.

> [!NOTE]
> L’adresse IP virtuelle de l’instance de gestion des API change à chaque activation ou désactivation du réseau virtuel.  
> L’adresse IP virtuelle est également modifiée si la gestion des API passe **d’externe** à **interne** ou vice versa
>


> [!IMPORTANT]
> Si vous supprimez le service Gestion des API à partir d’un réseau virtuel (VNET) ou que vous modifiez celui sur lequel il est déployé, le réseau virtuel précédemment utilisé peut rester verrouillé jusqu’à 4 heures. Pendant ce temps, vous ne pourrez pas supprimer le réseau virtuel ou y déployer une nouvelle ressource.

## <a name="enable-vnet-powershell"> </a>Activation de la connexion au réseau virtuel à l’aide d’applets de commande PowerShell
Vous pouvez également activer la connectivité de réseau virtuel à l’aide d’applets de commande PowerShell.

* **Création d’un service de gestion des API au sein d’un réseau virtuel** : utilisez l’applet de commande [New-AzureRmApiManagement](/powershell/module/azurerm.apimanagement/new-azurermapimanagement) pour créer un service de gestion des API Azure au sein d’un réseau virtuel.

* **Déploiement d’un service de gestion des API existant au sein d’un réseau virtuel** : utilisez l’applet de commande [Update-AzureRmApiManagementDeployment](/powershell/module/azurerm.apimanagement/update-azurermapimanagementdeployment) pour déplacer un service de gestion des API Azure existant au sein d’un réseau virtuel.

## <a name="connect-vnet"> </a>Se connecter à un service web hébergé sur un réseau virtuel
Une fois que votre service Gestion des API est connecté au réseau virtuel, l’accès aux services principaux de ce réseau est similaire à l’accès aux services publics. Tapez simplement l’adresse IP locale ou le nom d’hôte (si un serveur DNS est configuré pour le réseau virtuel) de votre service web dans le champ **URL du service web** lorsque vous créez ou modifiez une API.

![Ajouter des API à partir du VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Problèmes courants liés à la configuration du réseau
Voici une liste des problèmes courants de configuration incorrecte qui peuvent se produire lors du déploiement du service de gestion des API dans un réseau virtuel.

* **Configuration du serveur DNS personnalisée** : le service de la gestion des API dépend de plusieurs services Azure. Si la gestion des API est hébergée dans un réseau virtuel comportant un serveur DNS personnalisé, il doit résoudre les noms d’hôte de ces services Azure. Veuillez suivre [ce](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) guide sur la configuration de serveurs DNS personnalisée. Consultez le tableau des ports ci-dessous et les autres exigences en matière de réseau pour référence.

> [!IMPORTANT]
> Si vous utilisez des serveurs DNS personnalisés pour le réseau virtuel, nous vous recommandons de le configurer **avant** d’y déployer un service Gestion des API. Sinon, vous devez mettre à jour le service Gestion des API chaque fois que vous changez les serveurs DNS en exécutant l’[opération Appliquer une configuration réseau](https://docs.microsoft.com/en-us/rest/api/apimanagement/apimanagementservices#ApiManagementServices_ApplyNetworkConfigurationUpdates)

* **Ports requis pour la gestion des API** : le trafic entrant et sortant dans un sous-réseau dans lequel est déployée la gestion des API peut être contrôlé à l’aide du [groupe de sécurité réseau][Network Security Group]. Si ces ports ne sont pas disponibles, la gestion des API risque de ne pas fonctionner correctement et d’être inaccessible. Le blocage d’un ou plusieurs de ces ports constitue un autre problème de configuration courant lorsque vous utilisez la gestion des API dans un réseau virtuel.

Lorsque l’instance de service Gestion des API est hébergée dans un réseau virtuel, les ports du tableau suivant sont utilisés.

| Port(s) source / de destination | Direction | Protocole de transfert | Objectif | Source / Destination | Type d’accès |
| --- | --- | --- | --- | --- | --- |
| * / 80, 443 |Trafic entrant |TCP |Communication client avec Gestion des API |INTERNET / VIRTUAL_NETWORK |Externe |
| * / 3443 |Trafic entrant |TCP |Point de terminaison de gestion pour le portail Azure et Powershell |INTERNET / VIRTUAL_NETWORK |Externe et interne |
| * / 80, 443 |Règle de trafic sortant |TCP |Dépendance sur Stockage Azure et Azure Service Bus |VIRTUAL_NETWORK / INTERNET |Externe et interne |
| * / 1433 |Règle de trafic sortant |TCP |Dépendance sur SQL Azure |VIRTUAL_NETWORK / INTERNET |Externe et interne |
| * / 11000 - 11999 |Règle de trafic sortant |TCP |Dépendance Azure SQL V12 |VIRTUAL_NETWORK / INTERNET |Externe et interne |
| * / 14000 - 14999 |Règle de trafic sortant |TCP |Dépendance Azure SQL V12 |VIRTUAL_NETWORK / INTERNET |Externe et interne |
| * / 5671 |Règle de trafic sortant |AMQP |Dépendance du journal pour la stratégie Event Hub et l’agent de surveillance |VIRTUAL_NETWORK / INTERNET |Externe et interne |
| 6381 - 6383 / 6381 - 6383 |Trafic entrant et sortant |UDP |Dépendance sur Cache Redis |VIRTUAL_NETWORK / VIRTUAL_NETWORK |Externe et interne |-
| * / 445 |Règle de trafic sortant |TCP |Dépendance sur le partage de fichiers Azure pour GIT |VIRTUAL_NETWORK / INTERNET |Externe et interne |
| * / * | Trafic entrant |TCP |Équilibrage de charge de l’infrastructure Azure | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK |Externe et interne |

* **Fonctionnalité SSL** : pour activer la génération et la validation de la chaîne de certification SSL, le service de gestion des API nécessite une connectivité réseau sortante vers ocsp.msocsp.com, mscrl.microsoft.com et crl.microsoft.com.

* **Métriques et surveillance de l’intégrité** : connectivité réseau sortante aux points de terminaison de la surveillance Azure, qui se résolvent sous les domaines suivants : global.metrics.nsatc.net, shoebox2.metrics.nsatc.net, prod3.metrics.nsatc.net.

* **Configuration d’Express Route** : une configuration client courante consiste à définir un itinéraire par défaut (0.0.0.0/0), ce qui force le trafic Internet sortant à circuler sur site. Ce flux de trafic interrompt la connectivité avec la gestion des API Azure, car le trafic sortant peut être bloqué sur site, ou faire l’objet d’une opération NAT sur un jeu d’adresses non reconnaissable qui ne fonctionne plus avec différents points de terminaison Azure. La solution consiste à définir un (ou plusieurs) itinéraires définis par l’utilisateur ([UDR][UDRs]) sur le sous-réseau qui contient la gestion des API Azure. Un itinéraire défini par l'utilisateur définit des itinéraires spécifiques au sous-réseau qui seront respectés au lieu de l'itinéraire par défaut.
  Si possible, il est recommandé d'utiliser la configuration suivante :
 * La configuration ExpressRoute annonce 0.0.0.0/0 et par défaut, tunnelise de force tout le trafic sortant sur site.
 * L’itinéraire défini par l'utilisateur appliqué au sous-réseau contenant la gestion des API Azure définit 0.0.0.0/0 avec un type de tronçon Internet suivant.
 Le résultat est que l’itinéraire défini par l'utilisateur au niveau du sous-réseau a la priorité sur le tunneling forcé ExpressRoute, garantissant ainsi un accès Internet sortant à partir de la gestion des API Azure.

>[!WARNING]  
>La gestion des API Azure n’est pas prise en charge avec les configurations ExpressRoute qui **publient incorrectement de façon croisée des itinéraires à partir du chemin d’accès d’homologation publique vers le chemin d’accès d’homologation privée**. Les configurations ExpressRoute ayant une homologation publique configurée reçoivent les annonces de routage depuis Microsoft pour un grand ensemble de plages d'adresses IP Microsoft Azure. Si ces plages d’adresses sont incorrectement publiées de façon croisée sur le chemin d’accès d’homologation privée, il en résulte que tous les paquets réseau sortants du sous-réseau de l’instance de la gestion des API Azure sont incorrectement acheminés de force vers l’infrastructure réseau sur site d’un client. Ce flux réseau interrompt la gestion des API Azure. La solution à ce problème consiste à arrêter les itinéraires croisés depuis le chemin d'accès d'homologation publique vers le chemin d'accès d'homologation privée.

## <a name="limitations"> </a>Limitations
* Un sous-réseau contenant des instances du service Gestion des API ne peut pas contenir d’autres types de ressource Azure.
* Le sous-réseau et le service Gestion des API doivent figurer dans le même abonnement.
* Un sous-réseau contenant des instances du service Gestion des API ne peut pas être déplacé entre des abonnements.
* Lorsque vous utilisez un réseau virtuel interne, seule une adresse IP interne est disponible dans la plage indiquée dans [RFC 1918](https://tools.ietf.org/html/rfc1918) ; une adresse IP publique ne peut pas être fournie.
* Pour les déploiements du service de gestion des API dans plusieurs régions avec des réseaux virtuels internes configurés, les utilisateurs sont chargés de gérer leur propre équilibrage de charge, car ils possèdent le DNS.


## <a name="related-content"> </a>Contenu connexe
* [Connexion d’un réseau virtuel au serveur principal à l’aide de la passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site-ipsecike-vpn-tunnel)
* [Connexion d’un réseau virtuel utilisant des modèles de déploiement différents](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Utilisation de l’inspecteur d’API pour le suivi des appels dans Gestion des API Azure](api-management-howto-api-inspector.md)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-private.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-public.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/virtual-networks-nsg.md

