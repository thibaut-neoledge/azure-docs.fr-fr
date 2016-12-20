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
ms.date: 10/20/2016
ms.author: antonba
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7dfbf0e460dc8de13a4a71c1f925c93f6238df5d


---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Utilisation de la gestion des API Azure avec des réseaux virtuels
Les réseaux virtuels Azure vous permettent de placer vos ressources Azure dans un réseau routable non-Internet dont vous contrôlez l’accès. Ces réseaux peuvent ensuite être connectés à vos réseaux locaux à l’aide d’une variété de technologies VPN. Pour en savoir plus sur les réseaux virtuels Azure, commencez par consulter la page [Présentation du réseau virtuel](../virtual-network/virtual-networks-overview.md).

La fonctionnalité Gestion des API Azure peut être connectée à un réseau virtuel (VNET) afin de pouvoir accéder aux services principaux du réseau et pour que le portail des développeurs et la passerelle API soient accessibles sur le réseau.

## <a name="enable-vpn"> </a>Activer la connexion au réseau virtuel
> La connexion VNET est disponible uniquement dans les niveaux **Premium** et **Développeur**. Pour basculer entre les niveaux, ouvrez votre service Gestion des API dans le portail Azure, puis ouvrez l’onglet **Scale and pricing (Échelle et tarification)**. Dans la section **Niveau de tarification**, sélectionnez le niveau Premium et cliquez sur Enregistrer.
> 
> 

Pour activer la connectivité VNET, ouvrez votre service Gestion des API dans le portail Azure ainsi que la page **Réseau virtuel**.

![Menu Réseau virtuel du service Gestion des API][api-management-using-vnet-menu]

Sélectionnez le type d’accès souhaité :

* **Externe** : la passerelle Gestion des API et le portail des développeurs sont accessibles à partir de l’Internet public via un équilibreur de charge externe. La passerelle peut accéder aux ressources au sein du réseau virtuel.

![Homologation publique][api-management-vnet-public]

* **Interne** : la passerelle Gestion des API et le portail des développeurs sont accessibles uniquement sur le réseau virtuel via un équilibreur de charge interne. La passerelle peut accéder aux ressources au sein du réseau virtuel.

![Homologation privée][api-management-vnet-private]

Vous voyez maintenant une liste de toutes les régions où votre service Gestion des API est créé. Sélectionnez un réseau VNET et un sous-réseau pour chaque région. La liste contient les réseaux virtuels classiques et ARM, disponibles dans vos abonnements Azure, qui sont installés dans la région que vous configurez.

![Sélectionner le VPN][api-management-setup-vpn-select]

Cliquez sur **Enregistrer** dans la partie supérieure de l’écran. 

> Vous ne pouvez pas effectuer d’opérations de gestion sur le service Gestion des API pendant la mise à jour. La passerelle Gestion des API et le portail des développeurs restent disponibles.
> Notez que l’adresse IP virtuelle de l’instance de Gestion des API est susceptible de changer à chaque activation ou désactivation du réseau virtuel.
> 
> 

## <a name="enable-vnet-powershell"> </a>Activer la connexion au réseau virtuel à l’aide des applets de commande PowerShell
Vous pouvez également activer la connectivité VNET à l’aide de l’applet de commande [Set-AzureRmApiManagementVirtualNetworks](https://msdn.microsoft.com/library/mt619277.aspx).

## <a name="connect-vnet"> </a>Se connecter à un service web hébergé sur un réseau virtuel
Une fois que votre service Gestion des API est connecté au réseau virtuel, l’accès aux services principaux de ce réseau est similaire à l’accès aux services publics. Tapez simplement l’adresse IP locale ou le nom d’hôte (si un serveur DNS est configuré pour le réseau virtuel) de votre service web dans le champ **URL du service web** lorsque vous créez ou modifiez une API.

![Ajouter des API à partir du VPN][api-management-setup-vpn-add-api]

## <a name="custom-dns"> </a>Configuration de serveur DNS personnalisée
La gestion des API dépend de plusieurs services Azure. Si une instance de service Gestion des API est hébergée dans un réseau virtuel comportant un serveur DNS personnalisé, il doit être en mesure de résoudre les noms d’hôte de ces services Azure. Veuillez suivre [ce](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) guide sur la configuration de serveurs DNS personnalisée. Reportez-vous au tableau des ports ci-dessous à titre de référence.

## <a name="ports-required"> </a>Ports requis pour la gestion des API
> Si ces ports ne sont pas disponibles, le service Gestion des API risque de ne pas fonctionner correctement et d’être inaccessible. Le blocage d’un ou plusieurs de ces ports constitue le problème de configuration le plus courant lorsque vous utilisez Gestion des API dans un réseau virtuel.
> 
> 

Lorsque l’instance de service Gestion des API est hébergée dans un réseau virtuel, les ports du tableau suivant sont utilisés.

| Port(s) | Direction | Protocole de transfert | Objectif | Source/Destination | Type d’accès |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Trafic entrant |TCP |Communication client avec Gestion des API |INTERNET / VIRTUAL_NETWORK |Externe |
| 3443 |Trafic entrant |TCP |Point de terminaison de gestion |INTERNET / VIRTUAL_NETWORK |Externe et interne |
| 80, 443 |Règle de trafic sortant |TCP |Dépendance sur Stockage Azure et Azure Service Bus |VIRTUAL_NETWORK / INTERNET |Externe et interne |
| 1433 |Règle de trafic sortant |TCP |Dépendance sur SQL Azure |VIRTUAL_NETWORK / INTERNET |Externe et interne |
| 9350, 9351, 9352, 9353, 9354 |Règle de trafic sortant |TCP |Dépendance sur Service Bus |VIRTUAL_NETWORK / INTERNET |Externe et interne |
| 5671 |Règle de trafic sortant |AMQP |Dépendance pour la stratégie Journaliser dans Event Hub |VIRTUAL_NETWORK / INTERNET |Externe et interne |
| 6381, 6382, 6383 |Entrant/sortant |UDP |Dépendance sur Cache Redis |VIRTUAL_NETWORK / VIRTUAL_NETWORK |Externe et interne |
| 445 |Règle de trafic sortant |TCP |Dépendance sur le partage de fichiers Azure pour GIT |VIRTUAL_NETWORK / INTERNET |Externe et interne |

## <a name="limitations"> </a>Limitations
* Un sous-réseau contenant des instances du service Gestion des API ne peut pas contenir d’autres types de ressource Azure.
* Le sous-réseau et le service Gestion des API doivent figurer dans le même abonnement.
* Un sous-réseau contenant des instances du service Gestion des API ne peut pas être déplacé entre des abonnements.
* Lorsque vous utilisez un réseau virtuel interne, seule une adresse IP interne est disponible dans la plage indiquée dans [RFC 1918](https://tools.ietf.org/html/rfc1918) ; une adresse IP publique ne peut pas être fournie.
* Pour les déploiements du service Gestion des API dans plusieurs régions avec des réseaux virtuels internes configurés, les utilisateurs sont chargés de gérer leur propre équilibrage de charge, car ils possèdent le DNS.

## <a name="related-content"> </a>Contenu connexe
* [Création d’un réseau virtuel avec une connexion de site à site à l’aide du portail Azure][Création d’un réseau virtuel avec une connexion de site à site à l’aide du portail Azure]
* [Utilisation de l’inspecteur d’API pour le suivi des appels dans Gestion des API Azure][Utilisation de l’inspecteur d’API pour le suivi des appels dans Gestion des API Azure]

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-type.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-private.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-public.png

[Activer les connexions VPN]: #enable-vpn
[Se connecter à un service web derrière un VPN]: #connect-vpn
[Contenu connexe]: #related-content


[Création d’un réseau virtuel avec une connexion de site à site à l’aide du portail Azure]: ../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[Utilisation de l’inspecteur d’API pour le suivi des appels dans Gestion des API Azure]: api-management-howto-api-inspector.md



<!--HONumber=Nov16_HO3-->


