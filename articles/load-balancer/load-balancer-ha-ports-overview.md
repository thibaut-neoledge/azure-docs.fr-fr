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
ms.openlocfilehash: 62d78e067e50183f25af84e547db2e11c0014f5d
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2017
---
# <a name="high-availability-ports-overview-preview"></a>Présentation des ports de haute disponibilité (préversion)

Azure Load Balancer Standard permet désormais d’équilibrer la charge des flux TCP et UDP sur tous les ports simultanément lors de l’utilisation d’un équilibrage de charge interne. 

>[!NOTE]
> La fonctionnalité des ports haute disponibilité est proposée avec Load Balancer Standard, actuellement en préversion. Le niveau de disponibilité et la fiabilité des fonctionnalités de la préversion peuvent différer de ceux de la version publique. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Il est nécessaire de s’inscrire à la préversion standard de Load Balancer pour pouvoir utiliser les ports haute disponibilité avec des ressources Load Balancer Standard. Suivez les instructions relatives à l’inscription en plus de la [préversion standard](https://aka.ms/lbpreview#preview-sign-up) de Load Balancer.

Une règle de Ports à haute disponibilité est une variante d’une règle d’équilibrage de charge configurée sur un équilibrage de charge Load Balancer Standard interne.  Les scénarios sont simplifiés en fournissant une seule règle d’équilibrage de charge pour équilibrer la charge de tous les flux TCP et UDP arrivant sur tous les ports d’un serveur frontal Load Balancer Standard interne. La décision d’équilibrage de charge est effectuée par flux basé sur le tuple de cinq : adresse IP source, port source, adresse IP de destination, port de destination et protocole.

Les ports à haute disponibilité permettent d’obtenir des scénarios critiques comme la haute disponibilité et la mise à l’échelle des appliances virtuelles réseau dans des réseaux virtuels, ainsi que d’autres scénarios où l’équilibrage de charge doit être effectué sur un grand nombre de ports. 

Les ports à haute disponibilité sont configurés en définissant les ports du serveur frontal et principal sur **0** et le protocole sur **All** (Tous).  La ressource d’équilibrage de charge interne équilibre désormais tous les flux TCP et UDP, quel que soit le numéro de port.

## <a name="why-use-ha-ports"></a>Pourquoi utiliser des ports à haute disponibilité ?

### <a name="network-virtual-appliances"></a>Appliances virtuelles réseau

Vous pouvez utiliser des appliances virtuelles réseau pour sécuriser votre charge de travail Azure contre plusieurs types de menaces de sécurité. Lorsque des appliances virtuelles réseau sont utilisées dans ces scénarios, elles doivent être fiables, hautement disponibles et évolutives pour la demande.

Vous pouvez atteindre ces objectifs dans votre scénario en ajoutant simplement des instances d’appliances virtuelles réseau au pool principal de l’équilibrage de charge interne Azure et en configurant une règle d’équilibrage de charge sur les ports à haute disponibilité.

Les ports à haute disponibilité présentent plusieurs avantages pour les scénarios d’appliances virtuelles réseau à haute disponibilité :
- basculement rapide vers des instances intègres avec des sondes d’intégrité par instance
- meilleures performances avec la montée en puissance vers n instances actives
- scénarios n-actif et actif-passif
- élimination du besoin d’utiliser des solutions complexes comme les nœuds Zookeeper pour le monitoring des appliances

L’exemple suivant présente un déploiement de type « Hub and Spoke » sur un réseau virtuel, dans lequel les spokes forcent le tunneling de leur trafic vers le réseau virtuel du hub et via l’appliance virtuelle réseau, avant de quitter l’espace de confiance. Les appliances virtuelles réseau se situent derrière un Load Balancer Standard interne avec une configuration de ports à haute disponibilité.  Tout le trafic peut être traité et transféré en conséquence. 

![exemple de ports de haute disponibilité](./media/load-balancer-ha-ports-overview/nvaha.png)

Figure 1 : Réseau virtuel « Hub and Spoke » avec appliances virtuelles réseau déployées en mode haute disponibilité

Si vous utilisez des appliances virtuelles réseau, renseignez-vous auprès du fournisseur respectif sur l’utilisation de ports à haute disponibilité et les scénarios pris en charge.

### <a name="load-balancing-large-numbers-of-ports"></a>Équilibrage de charge d’un grand nombre de ports

Vous pouvez également utiliser des ports à haute disponibilité pour des scénarios d’application qui nécessitent l’équilibrage de charge d’un grand nombre de ports. Ces scénarios peuvent être simplifiés à l’aide d’un [Load Balancer Standard](https://aka.ms/lbpreview) interne avec des ports à haute disponibilité où une règle d’équilibrage de charge unique remplace plusieurs règles d’équilibrage de charge individuelles, une pour chaque port.

## <a name="region-availability"></a>Disponibilité des régions

Les ports à haute disponibilité sont disponibles dans les [mêmes régions que Load Balancer Standard](https://aka.ms/lbpreview#region-availability).  

## <a name="preview-sign-up"></a>S’inscrire à la préversion

Pour découvrir la préversion des ports à haute disponibilité associés à Load Balancer Standard, inscrivez votre abonnement et bénéficiez d’un accès via PowerShell ou Azure CLI 2.0.  Effectuez les trois étapes suivantes :

>[!NOTE]
>Pour utiliser cette fonctionnalité, vous devez également vous inscrire à la [préversion standard](https://aka.ms/lbpreview#preview-sign-up) de Load Balancer, en plus des ports haute disponibilité. L’inscription aux ports haute disponibilité et aux préversions standard de Load Balancer peut prendre jusqu’à une heure.

### <a name="sign-up-using-azure-cli-20"></a>S’inscrire à l’aide d’Azure CLI 2.0

1. Enregistrer la fonctionnalité auprès du fournisseur
    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
2. L’opération précédente peut prendre jusqu’à 10 minutes.  Vous pouvez vérifier l’état de l’opération à l’aide de la commande suivante :

    ```cli
    az feature show --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
    Passez à l’étape 3 lorsque l’état d’enregistrement de la fonction retourne « Enregistré », comme indiqué ci-dessous :
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowILBAllPortsRule",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
3. Complétez l’inscription à la préversion en réinscrivant votre abonnement avec le fournisseur de ressources :

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    
### <a name="sign-up-using-powershell"></a>S’inscrire à l’aide de PowerShell

1. Enregistrer la fonctionnalité auprès du fournisseur
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    
2. L’opération précédente peut prendre jusqu’à 10 minutes.  Vous pouvez vérifier l’état de l’opération à l’aide de la commande suivante :

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    Passez à l’étape 3 lorsque l’état d’enregistrement de la fonction retourne « Enregistré », comme indiqué ci-dessous :
   
    ```
    FeatureName          ProviderName      RegistrationState
    -----------          ------------      -----------------
    AllowILBAllPortsRule Microsoft.Network Registered
    ```
    
3. Complétez l’inscription à la préversion en réinscrivant votre abonnement avec le fournisseur de ressources :

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```


## <a name="limitations"></a>Limites

Voici les configurations et exceptions prises en charge pour les ports de haute disponibilité :

- Une configuration d’adresse IP frontale peut consister en une seule règle d’équilibrage de charge DSR avec ports à haute disponibilité ou en une seule règle d’équilibrage de charge non DSR avec ports à haute disponibilité. Elle ne peut pas comprendre les deux à la fois.
- Une même configuration IP d’interface réseau ne peut avoir qu’une seule règle d’équilibrage de charge non DSR avec des ports à haute disponibilité. Aucune autre règle ne peut être configurée pour cette configuration IP.
- Une même configuration IP d’interface réseau peut comporter une ou plusieurs règles d’équilibrage de charge DSR avec ports à haute disponibilité, du moment que toutes leurs configurations IP frontend sont uniques.
- Il est possible de faire coexister plusieurs règles d’équilibrage de charge pointant vers un même pool backend si l’ensemble des règles d’équilibrage de charge est configuré avec des ports à haute disponibilité (DSR uniquement), ou, si l’ensemble des règles est configuré sans ports à haute disponibilité (DSR et non DSR). Ces deux règles d’équilibrage de charge ne peuvent pas coexister en présence d’une combinaison de règles avec et sans ports à haute disponibilité.
- Les ports à haute disponibilité ne sont pas disponibles avec IPv6.


## <a name="next-steps"></a>Étapes suivantes

- [Configurer des ports à haute disponibilité sur un Load Balancer Standard interne](load-balancer-configure-ha-ports.md)
- [En savoir plus sur la préversion de Load Balancer Standard](https://aka.ms/lbpreview)

