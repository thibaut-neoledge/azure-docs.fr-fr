---
title: "Mise en réseau Azure Governmenmt | Microsoft Docs"
description: "Cela fournit une comparaison des fonctionnalités et des conseils pour la connectivité privée à Azure Government"
services: azure-government
cloud: gov
documentationcenter: 
author: jawalte
manager: zakramer
ms.assetid: 3da70579-ecda-421a-8ebf-d52906334e9b
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/24/2017
ms.author: jawalte
translationtype: Human Translation
ms.sourcegitcommit: 8288ca2e85ec0cd1a1b53862b2d60fcb88e7be4b
ms.openlocfilehash: c6b8c27299c1bfceb045ee7d82981654f3a446d8
ms.lasthandoff: 01/30/2017


---
# <a name="azure-government-networking"></a>Mise en réseau d’Azure Government
## <a name="expressroute-private-connectivity"></a>ExpressRoute (connectivité privée)
ExpressRoute est mis à la disposition générale dans Azure Government. Pour plus d’informations (y compris sur les partenaires et les emplacements d’homologation), consultez la [documentation publique ExpressRoute ](../expressroute/index.md).

### <a name="variations"></a>Variantes
ExpressRoute est mis à la disposition générale dans Azure Government. 

* Les clients Government se connectent à une capacité physiquement isolée via une connexion ExpressRoute (ER) Azure Government (Gov) dédiée
* Azure Gov fournit une plus grande disponibilité et une durabilité accrue en tirant parti de plusieurs paires de régions distantes d’au moins 800 kilomètres (500 miles) 
* Par défaut, toute la connectivité Azure Gov ER est configurée sous forme redondante actif-actif avec prise en charge de la rupture, et offre des capacités de circuit jusqu’à 10 Go (50 Mo pour la plus petite)
* Les emplacements Azure Gov ER fournissent des itinéraires optimisés (sauts les plus courts, faible latence, hautes performances, etc.) pour les clients et les régions géo-redondantes Azure Gov
* La connexion privée Azure Gov ER n’utilise pas, ne parcourt pas et ne dépend pas d’Internet
* L’infrastructure physique et logique d’Azure Gov est physiquement dédiée et séparée, et son accès est limité aux résidents des États-Unis
* Microsoft possède et opère toute l’infrastructure fibre entre les régions Azure Gov et les emplacements Azure Gov ER Meet-Me
* Azure Gov ER assure la connectivité aux services cloud Microsoft Azure, O365 et CRM

### <a name="considerations"></a>Considérations
Il existe deux services de base qui fournissent une connectivité de réseau privé à Azure Government : VPN (site à site pour une organisation type) et ExpressRoute.

Azure ExpressRoute est utilisé pour créer des connexions privées entre les centres de données Azure Government et votre infrastructure locale, ou au sein d’un environnement de colocalisation. Les connexions ExpressRoute ne sont pas établies via l’Internet public et offrent une plus grande fiabilité, des débits plus importants et des latences moindres par rapport aux connexions Internet classiques. Dans certains cas, l’utilisation de connexions ExpressRoute pour le transfert de données entre des systèmes locaux et Azure offre des avantages significatifs en matière de coûts.   

Grâce à ExpressRoute, vous pouvez établir des connexions à Azure au niveau d’un emplacement ExpressRoute (comme l’établissement d’un fournisseur Exchange) ou vous connecter directement à Azure à partir de votre réseau WAN existant, tel qu’un VPN MPLS (Multi-Protocol Label Switching) fourni par votre fournisseur de services réseau.

![texte de remplacement](./media/azure-government-capability-private-connectivity-options.PNG)  ![texte de remplacement](./media/government-capability-expressroute.PNG)  

Pour que les services de réseau prennent en charge les solutions et applications des clients Azure Government, il est fortement recommandé d’utiliser ExpressRoute (connectivité privée) pour vous connecter à Azure Government. Si vous utilisez des connexions VPN, les éléments suivants doivent être pris en compte :

* Les clients doivent contacter l’agence/le fonctionnaire responsable de leur autorisation pour déterminer si une connectivité privée ou un autre mécanisme de connexion sécurisée est nécessaire, et pour identifier toute restriction supplémentaire à prendre en compte.
* Les clients doivent décider s’il faut imposer que la connexion VPN de site à site soit acheminée via une zone de connectivité privée.
* Les clients doivent obtenir soit un circuit MPLS, soit une connexion VPN avec un fournisseur d’accès de connectivité privée sous licence.

Tous les clients qui utilisent une architecture de connectivité privée doivent valider qu’une implémentation appropriée est établie et gérée pour la connexion client au point de démarcation de routeur périphérique entre le réseau de passerelle et Internet (GN/I) pour Azure Government. De même, votre organisation doit établir une connectivité réseau entre votre environnement local et le point de démarcation de routeur périphérique entre le réseau de passerelle et le client (GN/C) pour Azure Government.

## <a name="support-for-bgp-communities"></a>Prise en charge des communautés BGP
Cette section fournit une vue d’ensemble de l’utilisation des communautés BGP dans AzureGov. Microsoft publiera des routages sur les chemins d’homologation publiques et Microsoft avec des routages marqués à l’aide des valeurs de communauté appropriées. La logique de cette procédure et les détails concernant les valeurs de la communauté sont décrits ci-dessous. Cependant, Microsoft ignorera toutes les valeurs de communauté marquées pour des itinéraires qui lui sont proposés.

Si vous vous connectez à Microsoft via ExpressRoute dans n’importe quel emplacement d’homologation de la région AzureGov, vous aurez accès à tous les services cloud Microsoft de toutes les régions situées dans les limites du gouvernement. 

Par exemple, si vous vous êtes connecté à Microsoft à Washington D.C. à l’aide d’ExpressRoute, vous aurez accès à tous les services cloud Microsoft hébergés dans AzureGov.

Reportez-vous à l’onglet « Vue d’ensemble » de la [documentation publique ExpressRoute](../expressroute/index.md) pour en savoir plus sur les emplacements et les partenaires, et pour consulter une liste détaillée des emplacements d’homologation ExpressRoute pour AzureGov.

Vous pouvez acheter plusieurs circuits ExpressRoute. Le fait de disposer de plusieurs connexions vous offre des avantages significatifs en termes de haute disponibilité en raison de la redondance géographique. Si vous avez plusieurs circuits ExpressRoute, vous recevrez le même jeu de préfixes publiés par Microsoft sur les chemins d'homologation publiques et Microsoft. Cela signifie que vous disposez de plusieurs chemins de votre réseau vers Microsoft. Vous risquez ainsi de prendre des décisions de routage non optimales au sein de votre réseau. Et par conséquent, vous risquez de rencontrer des problèmes de connectivité non optimale avec différents services. 

Microsoft marquera les préfixes publiés via l'homologation publique et l’homologation Microsoft avec les valeurs de communauté BGP appropriées indiquant la région dans laquelle les préfixes sont hébergés. Vous pouvez compter sur les valeurs de communauté pour prendre des décisions de routage avisées et offrir aux clients un routage optimal.  Pour plus d’informations, reportez-vous à l’onglet « Prise en main » de la [documentation publique ExpressRoute](../expressroute/index.md), puis cliquez sur l’option d’optimisation du routage.

| **Région Azure pour les clouds nationaux**| **Valeur de communauté BGP** |
| --- | --- |
| **Gouvernement américain** |  |
| Gouvernement américain - Iowa | 12076:51109 |
| Gouvernement américain - Virginie | 12076:51105 |

Tous les routages publiés par Microsoft seront marqués avec la valeur de communauté appropriée. 

Par ailleurs, Microsoft marquera également des préfixes basés sur le service auquel ils appartiennent. Cela s'applique uniquement à l'homologation Microsoft. Le tableau ci-dessous fournit un mappage d’un service à la valeur de communauté BGP.

| **Service dans les clouds nationaux** | **Valeur de communauté BGP** |
| --- | --- |
| **Gouvernement américain** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| Skype Entreprise Online |12076:5130 |
| CRM Online |12076:5140 |
| Autres services Office 365 en ligne |12076:5200 |

> [!NOTE]
> Microsoft ignore les valeurs de communauté BGP définies sur les itinéraires proposés à Microsoft.

## <a name="support-for-load-balancer"></a>Prise en charge de l’équilibrage de charge
Généralement, l’équilibrage de charge est disponible dans Azure Government. Pour plus d’informations, consultez [la documentation publique Équilibrage de charge](../load-balancer/load-balancer-overview.md). 

## <a name="support-for-traffic-manger"></a>Prise en charge de Traffic Manager
Généralement, Traffic Manager est disponible dans Azure Government. Pour plus d’informations, consultez [la documentation publique Traffic Manager](../traffic-manager/traffic-manager-overview.md). 

## <a name="support-for-vnet-peering"></a>Prise en charge de VNet Peering 
Généralement, VNet Peering est disponible dans Azure Government. Pour plus d’informations, consultez [Documentation publique VNet Peering](../virtual-network/virtual-network-peering-overview.md). 

## <a name="support-for-vpn-gateway"></a>Prise en charge de la passerelle VPN 
Généralement, la passerelle VPN est disponible dans Azure Government. Pour plus d’informations, consultez [la documentation publique Passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). 

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations supplémentaires et des mises à jour, veuillez vous inscrire au <a href="https://blogs.msdn.microsoft.com/azuregov/">blog Microsoft Azure Government</a>.


