---
title: "Présentation des ports de haute disponibilité dans Azure | Microsoft Docs"
description: "Découvrez comment équilibrer la charge des ports de haute disponibilité sur un équilibreur de charge interne."
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.openlocfilehash: 3e54cb45cf002a183a5b0bd9b3082a235cd825f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="high-availability-ports-overview-preview"></a>Présentation des ports de haute disponibilité (préversion)

La référence SKU Standard d’Azure Load Balancer comprend de nouveaux ports de haute disponibilité qui permettent de distribuer le trafic provenant de tous les ports, pour tous les protocoles pris en charge. Lorsque vous configurez un équilibreur de charge interne, les utilisateurs peuvent configurer une règle de ports de haute disponibilité qui définit les ports frontend et backend sur **0** et le protocole sur **Tous**, permettant ainsi au trafic de circuler au sein de l’équilibreur de charge interne.

>[!NOTE]
> La fonctionnalité des ports haute disponibilité est proposée avec Load Balancer Standard, actuellement en préversion. Le niveau de disponibilité et la fiabilité des fonctionnalités de la préversion peuvent différer de ceux de la version publique. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Il est nécessaire de s’inscrire à la préversion standard de Load Balancer pour pouvoir utiliser les ports haute disponibilité avec des ressources Load Balancer Standard. Suivez les instructions relatives à l’inscription en plus de la [préversion standard](https://aka.ms/lbpreview#preview-sign-up) de Load Balancer.

L’algorithme d’équilibrage de charge reste le même, et la destination est sélectionnée en fonction des cinq tuples <adresse IP source, port source, adresse IP de destination, port de destination, protocole>. Toutefois, cette configuration permet à une seule règle d’équilibrage de charge de traiter tout le trafic disponible, ce qui simplifie la configuration et permet de ne pas être limité par le nombre maximal de règles d’équilibrage de charge pouvant être ajoutées.

L’un des scénarios critiques rendus possibles par les ports de haute disponibilité est le déploiement haute disponibilité d’appliances virtuelles sur des réseaux virtuels Azure. Ces ports de haute disponibilité permettent également un équilibrage de charge pour une plage de ports. 

## <a name="why-use-high-ha-ports"></a>Pourquoi utiliser des ports de haute disponibilité

Les clients Azure s’appuient essentiellement sur les appliances virtuelles réseau pour protéger leurs charges de travail des différents types de menaces de sécurité. En outre, les appliances virtuelles réseau doivent être hautement disponibles et fiables.  

Les ports de haute disponibilité simplifient le scénario de haute disponibilité des appliances virtuelles réseau en éliminant le besoin de solutions complexes telles que Zookeeper. En outre, grâce à une plus grande rapidité du basculement et de la montée en puissance, ils permettent une plus grande fiabilité. Pour obtenir la haute disponibilité des appliances virtuelles réseau, vous devez ajouter ces dernières au pool backend de l’équilibreur de charge interne d’Azure, puis configurer la règle de port de haute disponibilité de l’équilibreur de charge.

L’exemple suivant présente un déploiement de type « Hub and Spoke » sur un réseau virtuel, dans lequel les spokes forcent le tunneling de leur trafic vers le réseau virtuel du hub et via l’appliance virtuelle réseau, avant de quitter l’espace de confiance. Les appliances virtuelles réseau sont situées derrière un équilibreur de charge interne avec une configuration de haute disponibilité des ports. Elles peuvent donc traiter tout le trafic et le transférer en conséquence. 

![exemple de ports de haute disponibilité](./media/load-balancer-ha-ports-overview/nvaha.png)

Figure 1 : Réseau virtuel « Hub and Spoke » avec appliances virtuelles réseau déployées en mode haute disponibilité


## <a name="region-availability"></a>Disponibilité des régions

Les ports de haute disponibilité sont actuellement disponibles dans les régions suivantes :
- Est des États-Unis 2
- Centre des États-Unis
- Europe du Nord
- Centre-Ouest des États-Unis
- Europe de l'Ouest
- Asie du Sud-Est 

## <a name="preview-sign-up"></a>S’inscrire à la préversion

Pour découvrir la préversion des ports de haute disponibilité associés aux références SKU de l’équilibreur de charge standard, inscrivez votre abonnement et bénéficiez d’un accès via PowerShell ou Azure CLI 2.0.

- S’inscrire à l’aide de PowerShell

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```

- S’inscrire à l’aide d’Azure CLI 2.0

    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network 
    ```  


>[!NOTE]
>Pour utiliser cette fonctionnalité, vous devez également vous inscrire à la [préversion standard](https://aka.ms/lbpreview#preview-sign-up) de Load Balancer, en plus des ports haute disponibilité. L’inscription aux ports haute disponibilité et aux préversions standard de Load Balancer peut prendre jusqu’à une heure.

## <a name="limitations"></a>Limites

Voici les configurations et exceptions prises en charge pour les ports de haute disponibilité :

- Une configuration d’adresse IP frontale peut consister en une seule règle Load Balancer DSR avec ports haute disponibilité (tous les ports) ou en une seule règle Load Balancer non DSR avec ports haute disponibilité (tous les ports). Elle ne peut pas comprendre les deux à la fois.
- Une même configuration IP d’interface réseau ne peut avoir qu’une seule règle d’équilibreur de charge non DSR avec ports de haute disponibilité. Aucune autre règle ne peut être configurée pour cette configuration IP.
- Une même configuration IP d’interface réseau peut comporter une ou plusieurs règles d’équilibreur de charge DSR avec ports de haute disponibilité, du moment que toutes leurs configurations IP frontend sont uniques.
- Il est possible de faire coexister plusieurs règles d’équilibreur de charge pointant vers un même pool backend si l’ensemble des règles d’équilibrage de charge est configuré avec des ports de haute disponibilité (DSR uniquement), ou, si l’ensemble des règles est configuré sans ports de haute disponibilité (DSR et non DSR). Ces deux règles d’équilibrage de charge ne peuvent pas coexister en présence d’une combinaison de règles avec et sans ports de haute disponibilité.
- Les ports de haute disponibilité ne sont pas disponibles pour les locataires IPv6.


## <a name="next-steps"></a>Étapes suivantes

[Configurer des ports de haute disponibilité sur un équilibreur de charge interne](load-balancer-configure-ha-ports.md)

