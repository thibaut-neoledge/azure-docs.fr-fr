---
title: Documentation Azure Government | Microsoft Docs
description: Cela fournit une comparaison des fonctionnalités et des conseils pour la connectivité privée à Azure Government
services: Azure-Government
cloud: gov
documentationcenter: ''
author: ryansoc
manager: zakramer
editor: ''

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 08/25/2016
ms.author: ryansoc

---
# Mise en réseau d’Azure Government
## ExpressRoute (connectivité privée)
Il existe deux services de base qui fournissent une connectivité de réseau privé à Azure Government : VPN (site à site pour une organisation type) et ExpressRoute.

Azure ExpressRoute est utilisé pour créer des connexions privées entre les centres de données Azure Government et votre infrastructure locale, ou au sein d’un environnement de colocalisation. Les connexions ExpressRoute ne sont pas établies via l’Internet public et offrent une plus grande fiabilité, des débits plus importants et des latences moindres par rapport aux connexions Internet classiques. Dans certains cas, l’utilisation de connexions ExpressRoute pour le transfert de données entre des systèmes locaux et Azure offre des avantages significatifs en matière de coûts.

Grâce à ExpressRoute, vous pouvez établir des connexions à Azure au niveau d’un emplacement ExpressRoute (comme l’établissement d’un fournisseur Exchange) ou vous connecter directement à Azure à partir de votre réseau WAN existant, tel qu’un VPN MPLS (Multi-Protocol Label Switching) fourni par votre fournisseur de services réseau.

![texte de remplacement](./media/azure-government-capability-private-connectivity-options.PNG) ![texte de remplacement](./media/government-capability-expressroute.PNG)

Pour que les services de réseau prennent en charge les solutions et applications des clients Azure Government, il est fortement recommandé d’utiliser ExpressRoute (connectivité privée) pour vous connecter à Azure Government. Si vous utilisez des connexions VPN, les éléments suivants doivent être pris en compte :

• Les clients doivent contacter l’agence/le fonctionnaire responsable de leur autorisation pour déterminer si une connectivité privée ou un autre mécanisme de connexion sécurisée est nécessaire, et pour identifier toute restriction supplémentaire à prendre en compte.

• Les clients doivent décider s’il faut imposer que la connexion VPN de site à site soit acheminée via une zone de connectivité privée.

• Les clients doivent obtenir soit un circuit MPLS, soit une connexion VPN avec un fournisseur d’accès de connectivité privée sous licence.

Tous les clients qui utilisent une architecture de connectivité privée doivent valider qu’une implémentation appropriée est établie et gérée pour la connexion client au point de démarcation de routeur périphérique entre le réseau de passerelle et Internet (GN/I) pour Azure Government. De même, votre organisation doit établir une connectivité réseau entre votre environnement local et le point de démarcation de routeur périphérique entre le réseau de passerelle et le client (GN/C) pour Azure Government.

ExpressRoute est mis à la disposition générale dans Azure Government. Pour plus d’informations (y compris sur les partenaires et les emplacements d’homologation), veuillez consulter la <a href="https://azure.microsoft.com/documentation/services/expressroute/"> documentation publique ExpressRoute </a>.

Pour obtenir des informations supplémentaires et des mises à jour, veuillez vous inscrire au <a href="https://blogs.msdn.microsoft.com/azuregov/">blog Microsoft Azure Government</a>.

<!---HONumber=AcomDC_0831_2016-->