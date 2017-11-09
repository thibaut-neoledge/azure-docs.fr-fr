---
title: "Définir des stratégies de sécurité dans Azure Security Center | Microsoft Docs"
description: "Ce document est conçu pour vous aider à configurer des stratégies de sécurité dans le Centre de sécurité Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 1cebb6edecd13c6ab32c6854bfd6fe908c1f71f4
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="set-security-policies-in-security-center"></a>Définir des stratégies de sécurité dans Security Center
Ce document est conçu pour vous aider à configurer des stratégies de sécurité dans le Centre de sécurité en vous guidant tout au long des étapes nécessaires à l’exécution de cette tâche. 


## <a name="how-security-policies-work"></a>Fonctionnement des stratégies de sécurité
Security Center crée automatiquement une stratégie de sécurité par défaut, pour chacun de vos abonnements Azure. Vous pouvez modifier la stratégie dans Security Center et en surveiller la conformité. 

> [!NOTE]
> Vous pouvez maintenant étendre des stratégies Security Center à l’aide de la stratégie Azure, disponible en préversion limitée. Cliquez sur [ici](http://aka.ms/getpolicy) pour rejoindre la préversion ou consultez la documentation [ici](security-center-azure-policy.md).

Par exemple, les ressources utilisées pour le développement ou le test peuvent avoir des exigences de sécurité différentes de celles utilisées pour les applications de production. De même, les applications qui utilisent des données réglementées, telles que des informations d’identification personnelle, peuvent nécessiter un niveau de sécurité plus élevé. Les stratégies de sécurité activées dans Azure Security Center déterminent les recommandations de sécurité et la surveillance qui vous aident à identifier les vulnérabilités potentielles et à éliminer les menaces. Pour plus d’informations sur le choix de l’option adaptée à votre situation, consultez le [Guide des opérations et de planification d’Azure Security Center](security-center-planning-and-operations-guide.md) .

## <a name="edit-security-policies"></a>Modifier des stratégies de sécurité
Vous pouvez modifier la stratégie de sécurité par défaut pour chacun de vos abonnements Azure dans Security Center. Pour modifier une stratégie de sécurité, vous devez avoir le rôle de propriétaire, de collaborateur ou d’administrateur de la sécurité pour l’abonnement concerné. Connectez-vous au portail Azure et suivez les étapes ci-dessous pour configurer des stratégies de sécurité dans Security Center : 

1.  Dans le tableau de bord **Security Center**,sous **Général**, cliquez sur **Stratégie de sécurité**.
2.  Sélectionnez l’abonnement sur lequel activer la stratégie de sécurité.
3.  Dans la section **COMPOSANTS DE LA STRATÉGIE** , cliquez sur **stratégie de sécurité**.
4.  Il s’agit de la stratégie par défaut affectée par Security Center. Vous pouvez activer ou désactiver les recommandations de sécurité disponibles.
5.  Lorsque vous avez terminé, cliquez sur **Enregistrer**.

## <a name="available-security-policy-options"></a>Options de stratégie de sécurité disponibles

Le tableau ci-dessous explique à quoi sert chaque option :

| Stratégie | Lorsque l’option est activée |
| --- | --- |
| Mises à jour système |Une liste quotidienne des mises à jour de sécurité et critiques disponibles est récupérée à partir de Windows Update ou de Windows Server Update Services. La liste récupérée dépend du service configuré pour cette machine virtuelle et recommande d’appliquer les mises à jour manquantes. Pour les systèmes Linux, la stratégie utilise le système de gestion des packages fournis par un distributeur afin de déterminer pour quels packages des mises à jour sont disponibles. Elle vérifie également la disponibilité des mises à jour de sécurité et critiques à partir des machines virtuelles des [Services cloud Azure](../cloud-services/cloud-services-how-to-configure.md). |
| Vulnérabilités du système d’exploitation |Analyse les configurations de système d’exploitation tous les jours afin d’identifier les problèmes susceptibles de rendre la machine virtuelle vulnérable aux attaques. Cette stratégie recommande également des modifications de configuration pour résoudre ces vulnérabilités. Pour plus d’informations sur les configurations spécifiques surveillées, consultez la [liste des règles de base recommandées](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) . (À ce stade, Windows Server 2016 n'est pas entièrement pris en charge.) |
| Protection du point de terminaison |Recommande l’approvisionnement d’Endpoint Protection pour toutes les machines virtuelles Windows afin d’identifier et de supprimer les virus, les logiciels espions et autres logiciels malveillants. |
| Chiffrement de disque |Recommande d’activer le chiffrement de disque dans toutes les machines virtuelles pour améliorer la protection des données au repos. |
| groupes de sécurité réseau ; |Recommande la configuration de [Groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md) pour contrôler le trafic entrant et sortant vers les machines virtuelles dotées de points de terminaison publics. Les groupes de sécurité réseau configurés pour un sous-réseau sont hérités par toutes les interfaces réseau de machine virtuelle, sauf indication contraire. En plus de vérifier qu’un groupe de sécurité réseau a été configuré, cette stratégie évalue les règles de sécurité de trafic entrant afin d’identifier les règles autorisant le trafic entrant. |
| Pare-feu d’application web |Recommande l’approvisionnement d’un pare-feu d’applications web sur les machines virtuelles lorsque l’une des conditions suivantes est remplie : </br></br>[L’adresse IP publique de niveau d’instance](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP) est utilisée et les règles de sécurité entrantes pour le groupe de sécurité réseau associé sont configurés pour autoriser l’accès au port 80/443.</br></br>Une adresse IP à équilibrage de charge est utilisée et les règles associées d’équilibrage de charge et NAT (Network Access Translation) de trafic entrant sont configurées pour autoriser l’accès au port 80 ou 443. Pour plus d’informations, consultez [Prise en charge d’un équilibrage de charge par Azure Resource Manager](../load-balancer/load-balancer-arm.md). |
| Pare-feu de nouvelle génération |Étend les protections du réseau au-delà des groupes de sécurité réseau intégrés à Azure. Security Center détecte les déploiements pour lesquels un pare-feu de nouvelle génération est recommandé et vous permet d’approvisionner une appliance virtuelle. |
| Audit SQL et détection des menaces |Recommande l’activation de l’audit de l’accès à Azure SQL Database à des fins de conformité, mais également de détection avancée des menaces et d’examen. |
| Chiffrement SQL |Recommande l’activation du chiffrement au repos pour votre base de données Azure SQL Database, ainsi que pour les sauvegardes associées et les fichiers journaux de transaction. Même si vos données font l’objet d’une violation de sécurité, elles ne seront pas lisibles. |
| Évaluation des vulnérabilités |Recommande d’installer une solution d’évaluation des vulnérabilités sur votre machine virtuelle. |
| Chiffrement du stockage |Cette fonctionnalité est actuellement disponible pour les objets blob et fichiers Azure. Après l’activation du chiffrement de service de stockage, seules les nouvelles données seront chiffrées et tous les fichiers existants dans ce compte de stockage resteront non chiffrés. |
| Accès réseau JIT |Lorsque la fonctionnalité Juste à temps est activée, Security Center verrouille le trafic entrant vers vos machines virtuelles Azure en créant une règle de groupe de sécurité réseau. Vous sélectionnez les ports de la machine virtuelle pour lesquels le trafic entrant doit être verrouillé. Pour plus d’informations, consultez [Gérer l’accès Juste à temps à la machine virtuelle](https://docs.microsoft.com/azure/security-center/security-center-just-in-time). |


## <a name="next-step"></a>Étape suivante
Dans ce document, vous avez appris à configurer des stratégies de sécurité dans le Centre de sécurité. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Azure Security Center planning and operations guide](security-center-planning-and-operations-guide.md). découvrez comment planifier l’adoption d’Azure Security Center et prenez connaissance des considérations relatives à la conception.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md). découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md). découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md). découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
* [FAQ du Centre de sécurité Azure](security-center-faq.md). forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/). accédez à des billets de blog sur la sécurité et la conformité Azure.
