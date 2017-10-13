---
title: "Vue d’ensemble du service Protection DDos Standard Azure | Microsoft Docs"
description: "Découvrez le service Protection DDos Azure."
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: kumud
ms.openlocfilehash: 0130823b6a6f5a4883ad640c9bcefc89b82b2cd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ddos-protection-standard-overview"></a>Vue d’ensemble du service Protection DDos Standard Azure

>[!IMPORTANT]
>Le service Protection DDos Standard Azure est disponible en préversion. Un nombre limité de ressources Azure prennent en charge le service Protection DDos Standard, et ce dans un nombre limité de régions. Vous devez [vous inscrire au service](http://aka.ms/ddosprotection) pendant la préversion limitée afin que le service Protection DDos Standard soit activé pour votre abonnement. L’équipe Azure DDoS vous contacte au moment de l’inscription pour vous guider tout au long du processus d’activation. Le service Protection DDos Standard est disponible dans les régions États-Unis de l’Est, États-Unis de l’Ouest, États-Unis Centre-Ouest et États-Unis du Centre. Pendant la préversion, l’utilisation du service ne vous est pas facturée.

Les attaques par déni de service distribué (DDoS) représentent l’un des problèmes de disponibilité et de sécurité majeurs auxquels sont confrontés les clients qui déplacent leurs applications vers le cloud. Une attaque DDoS tente d’épuiser les ressources d’une application afin de la rendre indisponible aux utilisateurs légitimes. Les attaques DDoS peuvent être ciblées sur n’importe quel point de terminaison qui est publiquement accessible via Internet.

Combiné aux bonnes pratiques de conception d’application, le service Protection DDos Azure assure une excellente protection contre ces attaques. Les deux niveaux de service suivants sont fournis : 

- **Service Protection DDos De base Azure** : automatiquement activé dans le cadre de la plateforme Azure sans frais supplémentaires. La surveillance permanente du trafic et l’atténuation en temps réel des attaques courantes au niveau du réseau fournissent les mêmes défenses que celles utilisées par les services en ligne de Microsoft.  La distribution et l’atténuation du trafic d’attaque peuvent être réalisées entre différentes régions à l’échelle du réseau global d’Azure. 
- **Service Protection DDos Standard Azure** : fournit des fonctionnalités d’atténuation supplémentaires destinées spécifiquement aux ressources de Réseau virtuel. Cette protection est facile à activer et ne nécessite aucune modification de l’application. Les stratégies de protection sont paramétrées par le biais d’algorithmes de surveillance du trafic et de machine learning dédiés et sont appliquées aux adresses IP publiques associées aux ressources de Réseau virtuel telles que les instances Service Fabric, Load Balancer et Application Gateway.  Les données de télémétrie en temps réel sont disponibles par le biais d’affichages Azure Monitor pendant une attaque et à des fins d’historique. Vous pouvez ajouter des protections de la couche Application par le biais du [pare-feu d’applications web Application Gateway](https://azure.microsoft.com/services/application-gateway/). 

![Service Protection DDos Standard Azure](./media/ddos-protection-overview/ddos-protection-overview-fig2.png)

Pour les scénarios de développement et de test, vous pouvez essayer le service Protection DDos Standard et utiliser les ressources ci-après pour fournir des commentaires sur votre expérience :
- [Service Protection DDos Azure sur le Forum Microsoft Azure](https://feedback.azure.com/forums/905032-azure-ddos-protection) 
- [Service Protection DDos Azure sur le Forum MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azureddosprotection)
- [Service Protection DDos Azure sur Stack Overflow](https://stackoverflow.com/tags/azure-ddos/info)

Pour les problèmes de prise en charge, vous pouvez [ouvrir un ticket de support Azure](../azure-supportability/how-to-create-azure-support-request.md).

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Types d’attaques DDoS atténuées par le service Protection DDos Standard

Le service Protection DDos Standard peut atténuer les types d’attaques suivants :

- **Attaques volumétriques** : l’objectif de l’attaque consiste à submerger la couche réseau d’une quantité substantielle de trafic apparemment légitime. Cette attaque inclut les saturations UDP, les saturations par amplification et autres saturations par falsification de paquets. Le service Protection DDos Standard atténue ces attaques potentielles de plusieurs gigaoctets en les absorbant et en les purgeant, en s’appuyant automatiquement sur l’échelle du réseau global d’Azure. 
- **Attaques de protocole** : ces attaques rendent une cible inaccessible en exploitant une faille dans la pile de protocole des couches 3 et 4. Elles incluent les attaques par saturation SYN, les attaques par réflexion et autres attaques de protocole. Le service Protection DDos Standard atténue ces attaques en faisant la distinction entre le trafic légitime et le trafic malveillant, qu’il bloque par l’intermédiaire du client. 
- **Attaques de la couche Application** : ces attaques ciblent les paquets d’application web pour interrompre la transmission des données entre des hôtes. Elles incluent les violations de protocole HTTP, l’injection SQL, les scripts de site à site et autres attaques de la couche 7. Associer le pare-feu d’applications web Application Gateway (Application Gateway WAF) au service Protection DDos Standard offre une défense contre ces attaques. 

Le service Protection DDos Standard protège les ressources d’un réseau virtuel, y compris les adresses IP publiques associées aux machines virtuelles, les équilibreurs de charge internes et les passerelles d’application. Associé à la référence SKU Application Gateway WAF, le service Protection DDos Standard peut fournir une fonctionnalité d’atténuation complète pour les couches 3 à 7.

## <a name="ddos-protection-standard-features"></a>Fonctionnalités du service Protection DDos Standard

![Fonctionnalités de DDoS](./media/ddos-protection-overview/ddos-overview-fig1.png)

Les fonctionnalités du service Protection DDos Standard sont les suivantes : 

- **Intégration à la plateforme native :** le service Protection DDos Standard est intégré en mode natif à Azure, pouvant ainsi être configuré par l’intermédiaire du portail Azure et de PowerShell. Le service Protection DDos Standard comprend vos ressources et leur configuration.
- **Surveillance permanente du trafic :** vos modèles de trafic d’application sont analysés 24h/24 et 7j/7, à la recherche d’indicateurs DDoS. L’atténuation est effectuée en cas de dépassement des stratégies de protection.
- **Protection clés en main :** la configuration simplifiée protège immédiatement toutes les ressources situées sur un réseau virtuel dès que le service Protection DDos Standard est activé. Aucune intervention ou définition par l’utilisateur n’est requise. Le service Protection DDos Standard atténue de façon instantanée et automatique l’attaque une fois que celle-ci est détectée.
- **Réglage adaptatif :** le profilage intelligent du trafic étudie le trafic de votre application au fil du temps pour sélectionner et mettre à jour le profil le plus adapté pour votre service. Le profil s’ajuste en fonction des modifications du trafic au fil du temps.
- **Protection des couches 3 à 7 avec une passerelle d’application :** les fonctionnalités du pare-feu d’applications web Application Gateway offrent une Protection DDos complète de la pile.
- **Échelle d’atténuation étendue :** plus de 60 types d’attaques différents peuvent être atténués avec une protection globale contre les attaques DDoS les plus connues. 
- **Métriques d’attaque :** des métriques récapitulatives de chaque attaque sont accessibles via Azure Monitor.
- **Alerte d’attaque :** vous pouvez configurer des alertes au début et à l’arrêt d’une attaque, ainsi que pendant sa durée, à l’aide de métriques d’attaque intégrées. Les alertes s’intègrent à vos logiciels opérationnels tel qu’OMS, Splunk, Stockage Azure, l’e-mail et le portail Azure.
- **Maîtrise des coûts :** si vous documentez les attaques DDoS, vous bénéficiez en retour de crédits pour les services de transfert de données et de montée en charge des applications.

## <a name="ddos-protection-standard-mitigation"></a>Mitigation avec le service Protection DDos Standard

Le service Protection DDos de Microsoft surveille l’utilisation du trafic réelle et la compare en permanence aux seuils définis dans la stratégie DDoS. Si ce seuil de trafic est dépassé, l’atténuation DDoS est lancée automatiquement. Quand le trafic repasse sous le seuil, l’atténuation est supprimée.

Pendant l’atténuation, le trafic vers la ressource protégée est redirigé par le service Protection DDos et plusieurs vérifications sont effectuées. En règle générale, ces vérifications remplissent les fonctions suivantes :

- Vérifier que les paquets sont conformes aux spécifications de l’Internet et qu’ils ne sont pas mal formés.
- Interagir avec le client pour déterminer s’il s’agit éventuellement d’un paquet falsifié (par exemple, au moyen des techniques SYN Auth ou SYN Cookie ou en supprimant un paquet afin que la source le retransmette).
- Limiter le débit des paquets si aucune autre méthode de mise en œuvre ne peut être effectuée.

Le service Protection DDos bloque le trafic d’attaque et transfère le trafic restant vers la destination prévue. Dans les quelques minutes qui suivent la détection d’une attaque, vous êtes informé grâce aux métriques Azure Monitor. Vous pouvez configurer la journalisation des données de télémétrie du service Protection DDos Standard de manière à écrire les journaux des options disponibles en vue d’une analyse future. Les données des métriques dans Azure Monitor pour le service Protection DDos Standard sont conservées pendant 30 jours.

Nous vous déconseillons de simuler vos propres attaques DDoS. Par contre, vous pouvez utiliser le canal de support pour demander que l’équipe de la gestion réseau Azure exécute une simulation d’attaque DDoS. Un ingénieur vous contactera pour organiser les détails de l’attaque DDoS (ports, protocoles, adresses IP cibles) et le calendrier du test.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez en plus sur la gestion du service Protection DDos Standard à l’aide [d’Azure PowerShell](ddos-protection-manage-ps.md) ou du [portail Azure](ddos-protection-manage-portal.md).