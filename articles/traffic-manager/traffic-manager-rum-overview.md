---
title: "Mesures des utilisateurs réels dans Azure Traffic Manager | Microsoft Docs"
description: "Présentation de la fonctionnalité Mesures des utilisateurs réels dans Traffic Manager"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: a7e8ae605b6234341d9ab8b790f4c54d8627f29f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Vue d’ensemble de la fonctionnalité Mesures des utilisateurs réels dans Traffic Manager

>[!NOTE]
>La fonctionnalité Mesures des utilisateurs réels dans Traffic Manager est en préversion publique. Il se peut qu’elle n’offre pas les mêmes niveaux de disponibilité et de fiabilité que les fonctionnalités de la version mise à la disposition générale. Cette fonctionnalité n’est pas prise en charge, est susceptible de disposer de possibilités limitées et peut ne pas être disponible à certains emplacements Azure. Pour les notifications les plus récentes sur la disponibilité et l’état de cette fonctionnalité, consultez la page relative aux [mises à jour d’Azure Traffic Manager](https://azure.microsoft.com/updates/?product=traffic-manager).

Quand vous configurez un profil Traffic Manager pour utiliser la méthode de routage des performances, le service recherche la provenance des demandes de requête DNS et prend des décisions de routage pour diriger ces demandeurs vers la région Azure qui leur offre la latence la plus faible. Pour ce faire, il utilise l’intelligence de la latence réseau que Traffic Manager gère pour différents réseaux d’utilisateurs finaux.

La fonctionnalité Mesures des utilisateurs réels vous permet d’évaluer les mesures de la latence réseau vers les régions Azure, à partir des applications clientes auxquelles recourent vos utilisateurs finaux, et d’intégrer ces informations aux prises de décision de routage par Traffic Manager. En choisissant d’utiliser la fonctionnalité Mesures des utilisateurs réels, vous pouvez augmenter la précision du routage pour les demandes provenant des réseaux où résident vos utilisateurs finaux. 

## <a name="how-real-user-measurements-work"></a>Fonctionnement de la fonctionnalité Mesures des utilisateurs réels

Vos applications clientes mesurent la latence vers les régions Azure du point de vue des réseaux d’utilisateurs finaux où elles sont utilisées. Par exemple, si vous avez une page web accessible aux utilisateurs en différents endroits (par exemple, dans les régions d’Amérique du Nord), vous pouvez tirer parti de la puissance de la fonctionnalité Mesures des utilisateurs réels quand vous utilisez la méthode de routage des performances pour les aiguiller vers la meilleure région Azure hébergeant votre application serveur.

Dans un premier temps, un code JavaScript fourni par Azure (et contenant une clé unique) est incorporé dans vos pages web. Ensuite, chaque fois qu’un utilisateur visite cette page web, le code JavaScript interroge Traffic Manager pour obtenir des informations sur les régions Azure qu’il doit mesurer. Le service retourne un ensemble de points de terminaison au script, qui mesure ensuite ces régions consécutivement en téléchargeant une image d’un pixel hébergée dans ces régions Azure et en notant la latence qui s’est écoulée entre l’envoi de la demande et la réception du premier octet. Ces mesures sont ensuite renvoyées au service Traffic Manager.

Au fil du temps, l’exécution répétée de cette opération sur plusieurs réseaux permet à Traffic Manager d’obtenir des informations plus précises sur les caractéristiques de la latence des réseaux dans lesquels se trouvent vos utilisateurs finaux. Ces informations sont progressivement incluses dans les décisions de routage prises par Traffic Manager. Il en résulte une précision accrue des décisions basées sur les mesures des utilisateurs réels envoyées.

Quand vous utilisez la fonctionnalité Mesures des utilisateurs réels, vous êtes facturé en fonction du nombre de mesures envoyées à Traffic Manager. Pour plus d’informations sur les prix, visitez la page [Tarifs Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment utiliser la [fonctionnalité Mesures des utilisateurs réels avec des pages web](traffic-manager-create-rum-web-pages.md).
- En savoir plus sur le [fonctionnement de Traffic Manager](traffic-manager-overview.md)
- En savoir plus sur [Mobile Center](https://docs.microsoft.com/mobile-center/)
- En savoir plus sur les [méthodes de routage du trafic](traffic-manager-routing-methods.md) prises en charge par Traffic Manager
- En savoir plus sur la [création d’un profil Traffic Manager](traffic-manager-create-profile.md)

