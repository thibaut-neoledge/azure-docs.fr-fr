---
title: Comment configurer des connexions VPN dans Gestion des API Azure
description: "Apprenez à configurer une connexion VPN dans Gestion des API Azure et à accéder à des services web par son intermédiaire."
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 55a2a1e1-d07e-4111-9ce3-8837ed5040d6
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 30ec6f45da114b6c7bc081f8a2df46f037de61fd
ms.openlocfilehash: 98e88b1c69de8732f1a4ae54c8ecd0b11a547f35


---
# <a name="how-to-setup-vpn-connections-in-azure-api-management"></a>Comment configurer des connexions VPN dans Gestion des API Azure
La prise en charge du VPN par Gestion des API vous permet de connecter votre passerelle Gestion des API à un réseau virtuel Azure (classique). Ceci permet aux clients Gestion des API de se connecter de façon sécurisée à leurs services web principaux, qu’ils soient locaux ou qu’il s’agisse de services inaccessibles via l’internet public.

> [!NOTE]
> La gestion des API Azure fonctionne avec les réseaux virtuels classiques. Pour plus d’informations sur la création d’un réseau virtuel classique, consultez [Créer un réseau virtuel (classique) à l’aide du portail Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Pour obtenir des informations sur la connexion de réseaux virtuels classiques à des réseaux virtuels ARM, consultez [Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).
> 
> 

## <a name="enable-vpn"> </a>Activer les connexions VPN
> La connexion VPN est disponible uniquement dans les niveaux **Premium** et **Développeur**. Pour passer à cette connexion, ouvrez votre service Gestion des API dans le [portail Azure Classic][Azure Classic Portal], puis ouvrez l’onglet **Mettre à l’échelle**. Dans la section **Général** , sélectionnez le niveau Premium et cliquez sur Enregistrer.
> 
> 

Pour activer la connectivité VPN, ouvrez votre service Gestion des API dans le [portail Azure Classic][Azure Classic Portal] et passez à l’onglet **Configurer**. 

Dans la section VPN, définissez la valeur de **Connexion VPN** sur **Activée**.

![Configurer l’onglet de l’instance Gestion des API][api-management-setup-vpn-configure]

Vous voyez maintenant une liste de toutes les régions où votre service Gestion des API est créé.

Sélectionnez un VPN et un sous-réseau pour chaque région. La liste des VPN contient les réseaux virtuels disponibles dans votre abonnement Azure et qui sont installés dans la région que vous configurez.

![Sélectionner le VPN][api-management-setup-vpn-select]

Cliquez sur **Enregistrer** au bas de la page. Vous ne pouvez pas effectuer d’autres opérations sur le service Gestion des API à partir du portail Azure Classic pendant la mise à jour. La passerelle de service reste disponible et les appels d’exécution ne sont pas affectés.

Notez que l’adresse IP virtuelle de passerelle change chaque fois que le VPN est activé ou désactivé.

## <a name="connect-vpn"> </a>Se connecter à un service web derrière un VPN
Une fois que votre service Gestion des API est connecté au VPN, l’accès aux services web dans le réseau virtuel est similaire à l’accès aux services publics. Tapez simplement l’adresse locale ou le nom de l’hôte (si un serveur DNS a été configuré pour ce réseau virtuel Azure) de votre service web dans le champ **URL du service Web** lors de la création ou de la modification d’une API.

![Ajouter des API à partir du VPN][api-management-setup-vpn-add-api]

## <a name="required-ports-for-api-management-vpn-support"></a>Ports requis pour la prise en charge des réseaux privés virtuels de gestion des API
Lorsque l’instance de service Gestion des API est hébergée dans un réseau virtuel, les ports du tableau suivant sont utilisés. Si ces ports sont bloqués, le service risque de ne pas fonctionner correctement. Le blocage d’un ou plusieurs de ces ports constitue le problème de configuration le plus courant lorsque vous utilisez Gestion des API dans un réseau virtuel.

| Port(s) | Direction | Protocole de transfert | Objectif | Source / Destination |
| --- | --- | --- | --- | --- |
| 80, 443 |Trafic entrant |TCP |Communication client avec Gestion des API |INTERNET / VIRTUAL_NETWORK |
| 80,443 |Règle de trafic sortant |TCP |Dépendance Gestion des API sur Azure Storage et Azure Service Bus |VIRTUAL_NETWORK / INTERNET |
| 1433 |Règle de trafic sortant |TCP |Dépendances Gestion des API sur SQL |VIRTUAL_NETWORK / INTERNET |
| 9350, 9351, 9352, 9353, 9354 |Règle de trafic sortant |TCP |Dépendances Gestion des API sur Service Bus |VIRTUAL_NETWORK / INTERNET |
| 5671 |Règle de trafic sortant |AMQP |Dépendance Gestion des API pour la stratégie Journaliser dans Event Hub |VIRTUAL_NETWORK / INTERNET |
| 6381, 6382, 6383 |Entrant/sortant |UDP |Dépendances Gestion des API sur le cache Redis |VIRTUAL_NETWORK / VIRTUAL_NETWORK |
| 445 |Règle de trafic sortant |TCP |Dépendance Gestion des API sur le partage de fichiers Azure pour GIT |VIRTUAL_NETWORK / INTERNET |

## <a name="custom-dns"> </a>Configuration de serveur DNS personnalisée
La gestion des API dépend de plusieurs services Azure. Lorsqu’une instance de service de gestion des API est hébergée dans un réseau virtuel dans lequel un serveur DNS personnalisé est utilisé, il doit être en mesure de résoudre les noms d’hôtes de ces services Azure. Veuillez suivre [ce](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) guide sur la configuration de serveurs DNS personnalisée.  

## <a name="related-content"> </a>Contenu connexe
* [Créer un réseau virtuel avec une connexion VPN site à site à l’aide du Portail Azure Classic][Create a virtual network with a site-to-site VPN connection using the Azure Classic Portal]
* [Utilisation de l’inspecteur d’API pour le suivi des appels dans Gestion des API Azure][How to use the API Inspector to trace calls in Azure API Management]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[Azure Classic Portal]: https://manage.windowsazure.com/

[Create a virtual network with a site-to-site VPN connection using the Azure Classic Portal]: ../vpn-gateway/vpn-gateway-site-to-site-create.md
[How to use the API Inspector to trace calls in Azure API Management]: api-management-howto-api-inspector.md



<!--HONumber=Dec16_HO3-->


