---
title: "Corriger les vulnérabilités du système d’exploitation dans Azure Security Center | Microsoft Docs"
description: "Ce document vous montre comment implémenter la recommandation de l’Azure Security Center **Corriger les vulnérabilités du système d&quot;exploitation**."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: e6b251d5b97c57b3b6f79d14e53fbed5ca37ecb0
ms.contentlocale: fr-fr
ms.lasthandoff: 06/17/2017


---
# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>Corriger les vulnérabilités du système d’exploitation dans Azure Security Center
Azure Security Center analyse quotidiennement le système d’exploitation de votre machine virtuelle afin d’identifier les configurations susceptibles de rendre la machine virtuelle plus vulnérable aux attaques, et recommande des changements de configuration visant à résoudre ces problèmes. Security Center vous recommande de résoudre les vulnérabilités lorsque la configuration du système d’exploitation de votre machine virtuelle ne correspond pas aux règles de configuration recommandées.

> [!NOTE]
> Consultez la [liste des règles de configuration recommandées](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) pour plus d’informations sur les configurations surveillées.
>
>

## <a name="implement-the-recommendation"></a>Implémenter la recommandation

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement.  Ce document n’est pas un guide pas à pas.
>
>

1. Dans le panneau **Recommandations**, sélectionnez **Corriger les vulnérabilités du système d’exploitation**.
   ![Corriger des vulnérabilités du système d’exploitation][1]

    Le panneau **Corriger des vulnérabilités du système d’exploitation** s’ouvre et répertorie vos machines virtuelles dont les configurations de système d’exploitation ne correspondent pas aux règles de configuration recommandées.  Pour chaque machine virtuelle, le panneau identifie les éléments suivants :

   * **RÈGLES AYANT ÉCHOUÉ** : nombre de règles non respectées par la configuration du système d’exploitation de la machine virtuelle.
   * **HEURE DE LA DERNIÈRE ANALYSE** : date et heure de la dernière analyse de la configuration du système d’exploitation de la machine virtuelle par Security Center.
   * **ÉTAT** : état actuel de la vulnérabilité :

     * Ouverte : la recommandation n’a pas encore été corrigée
     * En cours : la vulnérabilité est en cours de résolution, aucune action de votre part n’est nécessaire
     * Résolue : l’application a déjà été corrigée (une fois le problème résolu, la ligne est grisée)
   * **GRAVITÉ** : toutes les vulnérabilités sont définies à un niveau de gravité faible, ce qui signifie qu’une vulnérabilité doit être traitée, mais qu’elle ne nécessite pas une attention immédiate.

2. Sélectionnez une machine virtuelle. Un panneau de cette machine virtuelle s’ouvre et affiche les règles qui ont échoué.
   ![Règles de configuration ayant échoué][2]

3. Sélectionnez une règle. Dans cet exemple, nous allons sélectionner **Le mot de passe doit respecter des exigences de complexité**. La règle ayant échoué ainsi que son impact s’affichent alors dans un nouveau panneau. Passez en revue les informations du panneau et déterminez comment les configurations de système d’exploitation sont appliquées.
  ![Description de la règle ayant échoué][3]

  Security Center utilise CCE (Common Configuration Enumeration) pour affecter des identificateurs uniques pour les règles de configuration. Ce panneau contient les informations suivantes :

  - NOM : nom de règle
  - GRAVITÉ : valeur de gravité CCE (critique, important ou avertissement)
  - CCIED : identificateur unique CCE pour la règle
  - DESCRIPTION : description de la règle
  - VULNÉRABILITÉ : explication de la vulnérabilité ou du risque si la règle n’est pas appliquée
  - IMPACT : impact sur l’activité lorsque la règle est appliquée
  - VALEUR ATTENDUE : valeur attendue lorsque Security Center analyse la configuration du système d’exploitation de votre machine virtuelle par rapport à la règle
  - OPÉRATION DE LA RÈGLE : opération de règle utilisée par Security Center lors de l’analyse de la configuration du système d’exploitation de votre machine virtuelle par rapport à la règle
  - VALEUR RÉELLE : valeur retournée après analyse de la configuration du système d’exploitation de votre machine virtuelle par rapport à la règle
  - RÉSULTAT DE L’ÉVALUATION : résultat de l’analyse : réussite ou échec

## <a name="see-also"></a>Voir aussi
Cet article vous a montré comment implémenter la recommandation de Security Center « Corriger les vulnérabilités du système d’exploitation ». Vous pouvez consulter l’ensemble des règles de configuration [ici](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Security Center utilise CCE (Common Configuration Enumeration) pour affecter des identificateurs uniques pour les règles de configuration. Visitez le site [CCE](https://nvd.nist.gov/cce/index.cfm) pour plus d’informations.

Pour plus d’informations sur Security Center, consultez les ressources suivantes :

* [Plateformes prises en charge dans Azure Security Center](security-center-os-coverage.md) : répertorie les machines virtuelles Windows et Linux prises en charge.
* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/recommendation.png
[2]:./media/security-center-remediate-os-vulnerabilities/vm-remediate-os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png

