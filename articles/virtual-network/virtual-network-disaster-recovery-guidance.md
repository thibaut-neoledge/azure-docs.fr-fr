---
title: "Que faire si une interruption du service Azure affecte des réseaux virtuels Azure ? | Microsoft Docs"
description: "Découvrez la procédure à suivre si une interruption du service Azure affecte vos instances réseaux virtuels Azure."
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan;aglick
ms.openlocfilehash: 4e125406d2e798138c45e3fbbf61a610afab69fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="virtual-network--business-continuity"></a>Réseau virtuel – Continuité des activités
## <a name="overview"></a>Vue d'ensemble
Un réseau virtuel (VNet) est une représentation de votre réseau dans le cloud. Il vous permet de définir votre propre espace d’adressage IP privé et de segmenter le réseau en sous-réseaux. Les réseaux virtuels servent de limite d’approbation pour héberger vos ressources de calcul telles que les machines virtuelles et les services cloud Azure (rôles web/de travail). Un réseau virtuel permet la communication IP privée directe entre les ressources qu’il contient. Un réseau virtuel peut également être lié à un réseau local par le biais de l’une des options hybrides telles que la passerelle VPN ou ExpressRoute.

Un réseau virtuel est créé dans l’étendue d’une région. Vous pouvez créer des réseaux virtuels avec le même espace d’adressage dans deux régions différentes (par exemple, l’Est des États-Uniset l’Ouest des États-Unis), mais il est impossible de les connecter les uns aux autres directement. 

## <a name="business-continuity"></a>Continuité des activités
Peut-être que votre application peut être interrompue de différentes manières. Une région donnée peut être coupée complètement des autres en raison d’une catastrophe naturelle ou d’une catastrophe partielle en raison d’une panne de plusieurs appareils ou services. L’impact sur le service de réseau virtuel est différent dans chacune de ces situations.

**Q : Que faire en cas de panne dans une région entière ? Par exemple, si une région est complètement coupée des autres en raison d’une catastrophe naturelle ? Que se passe-t-il pour les réseaux virtuels hébergés dans la région ?**

R : le réseau virtuel et les ressources dans la région affectée restent inaccessibles pendant la durée de l’interruption de service.

![Diagramme de réseau virtuel simple](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**Q: que faire pour recréer le même réseau virtuel dans une autre région ?**

R : un réseau virtuel est une ressource relativement légère. Vous pouvez appeler des API Azure pour créer un réseau virtuel avec le même espace d’adressage dans une autre région. Pour recréer le même environnement que celui qui était présent dans la région affectée, vous devez effectuer des appels d’API pour redéployer vos services cloud (rôles web/de travail) et les machines virtuelles que vous aviez. Vous devrez également créer une passerelle VPN et vous connecter à votre réseau local si vous aviez une connectivité locale (comme dans un déploiement hybride).

Vous trouverez [ici](virtual-networks-create-vnet-arm-pportal.md)des instructions pour la création d’un réseau virtuel. 

**Q: un réplica d’un réseau virtuel dans une région donnée peut-il être recréé dans une autre région à l’avance ?**

R : oui, vous pouvez créer deux réseaux virtuels utilisant les mêmes espaces d’adressage IP privé et ressources dans deux régions différentes à l’avance. Si un client hébergeait des services Internet dans le réseau virtuel, il aurait pu configurer Traffic Manager de manière à router géographiquement le trafic vers la région active. Toutefois, un client ne peut pas connecter deux réseaux virtuels avec le même espace d’adressage à son réseau local sous peine de causer des problèmes de routage. En cas d’incident et de perte d’un réseau virtuel dans une région, un client peut connecter à son réseau local l’autre réseau virtuel dans la région disponible avec l’espace d’adressage correspondant.

Vous trouverez [ici](virtual-networks-create-vnet-arm-pportal.md)des instructions pour la création d’un réseau virtuel.

