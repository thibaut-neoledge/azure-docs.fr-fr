---
title: "Installation d’Endpoint Protection dans Azure Security Center | Microsoft Docs"
description: "Ce document vous montre comment implémenter la recommandation d’Azure Security Center **Installer Endpoint Protection**."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: efb86a0ae362c30a6772c391a499154b7ae2a697
ms.contentlocale: fr-fr
ms.lasthandoff: 07/12/2017

---
# <a name="install-endpoint-protection-in-azure-security-center"></a>Installer Endpoint Protection dans Azure Security Center
Azure Security Center recommande d’installer Endpoint Protection sur vos machines virtuelles Azure si cette dernière n’est pas déjà activée. Cette recommandation s'applique uniquement aux machines virtuelles Windows.

> [!NOTE]
> Cet exemple de déploiement utilise Microsoft Antimalware. Consultez [Intégration des partenaires dans Azure Security Center](security-center-partner-integration.md#partners-that-integrate-with-security-center) pour obtenir la liste des partenaires intégrés dans Security Center.  
>
>

## <a name="implement-the-recommendation"></a>Implémenter la recommandation

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement.  Ce document n’est pas un guide pas à pas.
>
>

1. Dans le panneau **Recommandations**, sélectionnez **Installer Endpoint Protection**.
   ![Sélectionner Installer Endpoint Protection][1]
2. Le panneau **Installer Endpoint Protection** s’ouvre en affichant une liste de machines virtuelles dépourvues de protection des points de terminaison. Dans cette liste, sélectionnez les machines virtuelles sur lesquelles vous souhaitez installer Endpoint Protection et cliquez sur **Installer sur les machines virtuelles**.
   ![Sélectionner les machines virtuelles sur lesquelles installer Endpoint Protection][2]
3. Le panneau **Sélectionner Endpoint Protection** s’ouvre et vous permet de sélectionner la solution Endpoint Protection de votre choix. Dans cet exemple, nous allons sélectionner **Microsoft Antimalware**.
   ![Sélectionner Endpoint Protection][3]
4. Des informations supplémentaires sur la solution Endpoint Protection s’affichent. Sélectionnez **Créer**.
   ![Créer une solution anti-programme malveillant][4]
5. Entrez les paramètres de configuration nécessaires dans le panneau **Ajouter une extension**, puis sélectionnez **OK**. Pour en savoir plus sur les paramètres de configuration, consultez [Configuration d’un logiciel anti-programme malveillant par défaut et personnalisée](../security/azure-security-antimalware.md#default-and-custom-antimalware-configuration).

[Microsoft Antimalware](../security/azure-security-antimalware.md) est maintenant activé sur les machines virtuelles sélectionnées.

## <a name="see-also"></a>Voir aussi
Cet article vous a montré comment implémenter la recommandation de Security Center « Installer Endpoint Protection ». Pour en savoir plus sur l’activation de Microsoft Antimalware dans Azure, consultez le document suivant :

* [Microsoft Antimalware pour Azure Cloud Services et les machines virtuelles](../security/azure-security-antimalware.md) : découvrez comment déployer Microsoft Antimalware.

Pour plus d’informations sur Security Center, consultez les documents suivants :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez comment les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions partenaires.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : recherchez des billets de blog sur la sécurité et la conformité Azure.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/select-install-endpoint-protection.png
[2]:./media/security-center-install-endpoint-protection/install-endpoint-protection-blade.png
[3]:./media/security-center-install-endpoint-protection/select-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/create-antimalware-solution.png

