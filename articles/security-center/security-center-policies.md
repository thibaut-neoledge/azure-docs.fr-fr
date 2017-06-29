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
ms.date: 06/16/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 2593e6846c897644017083b49ad4ba8219696c6c
ms.contentlocale: fr-fr
ms.lasthandoff: 06/17/2017


---
# <a name="set-security-policies-in-azure-security-center"></a>Définir des stratégies de sécurité dans Azure Security Center
Ce document est conçu pour vous aider à configurer des stratégies de sécurité dans le Centre de sécurité en vous guidant tout au long des étapes nécessaires à l’exécution de cette tâche.

>[!NOTE] 
>À compter de début juin 2017, Security Center utilisera Microsoft Monitoring Agent pour collecter et stocker des données. Consultez [Azure Security Center Platform Migration](security-center-platform-migration.md) (Migration de la plateforme Azure Security Center) pour en savoir plus. Les informations contenues dans cet article représentent les fonctionnalités de Security Center après la transition vers Microsoft Monitoring Agent.
>

## <a name="what-are-security-policies"></a>Que sont les stratégies de sécurité ?
Une stratégie de sécurité définit l’ensemble des contrôles recommandés pour les ressources d’un abonnement spécifique. Dans Security Center, vous devez définir des stratégies pour vos abonnements Azure en fonction des exigences de sécurité de votre société et du type d’applications ou du niveau de confidentialité des données de chaque abonnement.

Par exemple, les ressources utilisées pour le développement ou le test peuvent avoir des exigences de sécurité différentes de celles utilisées pour les applications de production. De même, les applications qui utilisent des données réglementées, telles que des informations d’identification personnelle, peuvent nécessiter un niveau de sécurité plus élevé. Les stratégies de sécurité activées dans Azure Security Center déterminent les recommandations de sécurité et la surveillance qui vous aident à identifier les vulnérabilités potentielles et à éliminer les menaces. Pour plus d’informations sur le choix de l’option adaptée à votre situation, consultez le [Guide des opérations et de planification d’Azure Security Center](security-center-planning-and-operations-guide.md) .

## <a name="set-security-policies"></a>Définir des stratégies de sécurité
Vous pouvez configurer des stratégies de sécurité pour chaque abonnement. Pour modifier une stratégie de sécurité, vous devez avoir le rôle de propriétaire ou de collaborateur pour l’abonnement concerné. Connectez-vous au portail Azure et suivez les étapes ci-dessous pour configurer des stratégies de sécurité dans Security Center :

1. Cliquez sur la mosaïque **Stratégie** dans le tableau de bord du Centre de sécurité.
2. Sur le panneau Stratégie de sécurité qui s’ouvre, sélectionnez l’abonnement pour lequel vous souhaitez activer la stratégie de sécurité.

    ![Définition de stratégie](./media/security-center-policies/security-center-policies-fig1-ga.png)
3. Le panneau **Stratégie de sécurité** de l’abonnement sélectionné s’ouvre avec un ensemble d’options. Les options disponibles dans ce panneau sont :

   * **Stratégie de prévention**: utilisez cette option pour configurer des stratégies par abonnement ou par groupe de ressources.  
   * **Notification par e-mail**: utilisez cette option pour configurer une notification par e-mail qui est envoyée à la première occurrence quotidienne d’une alerte et pour les alertes de gravité élevée. Les préférences de courrier électronique peuvent être configurées uniquement pour les stratégies d’abonnement. Pour plus d’informations sur la configuration d’une notification par e-mail, consultez [Fournir les détails du contact de sécurité dans Azure Security Center](security-center-provide-security-contact-details.md) .
   * **Niveau tarifaire**: utilisez cette option pour mettre à niveau l’option de niveau tarifaire. Pour plus d’informations sur les options de tarification, consultez [Tarification de Security Center](security-center-pricing.md) .
4. Vérifiez que l’option **Collecter des données à partir des machines virtuelles** est définie sur **Activé**. Cette option active la collecte de journal automatique pour les ressources nouvelles et existantes à l’aide de Microsoft Monitoring Agent. Il s’agit du même agent que celui utilisé par Operations Management Suite et le service Log Analytics. Les données collectées à partir de cet agent seront stockées dans un espace de travail Log Analytics existant associé à votre abonnement Azure ou dans un nouvel espace de travail, en tenant compte de la zone géographique de la machine virtuelle.

5. Dans le panneau **Stratégie de sécurité**, cliquez sur **Stratégie de prévention** pour afficher les options disponibles. Cliquez sur **Activé** pour activer les recommandations de sécurité qui sont pertinentes pour cet abonnement.

    ![Sélection des stratégies de sécurité](./media/security-center-policies/security-center-policies-fig4-newUI.png)

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
| Chiffrement du stockage |Cette fonctionnalité est actuellement disponible pour les objets blob et fichiers Azure. Notez qu’après l’activation du chiffrement de service de stockage, seules les nouvelles données seront chiffrées et tous les fichiers existants dans ce compte de stockage resteront non chiffrés. |

Après avoir configuré toutes les options, cliquez sur **OK** dans le panneau **Stratégie de sécurité** contenant les recommandations, puis cliquez sur **Enregistrer** dans le panneau **Stratégie de sécurité** contenant les paramètres initiaux.

> [!NOTE]
> Le niveau tarifaire est toujours applicable pour le niveau de groupe de ressources. Pour plus d’informations, consultez notre [page de tarification](https://azure.microsoft.com/pricing/details/security-center/).
>
>

## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez appris à configurer des stratégies de sécurité dans le Centre de sécurité Azure. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

* [Azure Security Center planning and operations guide](security-center-planning-and-operations-guide.md). découvrez comment planifier l’adoption d’Azure Security Center et prenez connaissance des considérations relatives à la conception.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md). découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md). découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md). découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
* [FAQ du Centre de sécurité Azure](security-center-faq.md). forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/). accédez à des billets de blog sur la sécurité et la conformité Azure.

