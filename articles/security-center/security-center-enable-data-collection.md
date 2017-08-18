---
title: "Activer la collecte des données dans Azure Security Center | Microsoft Docs"
description: " Découvrez comment activer la collecte des données dans Azure Security Center. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 7e9ad8cd8c77c57c37dc208b86b3727a4e1dc7b5
ms.contentlocale: fr-fr
ms.lasthandoff: 06/17/2017


---
# <a name="enable-data-collection-in-azure-security-center"></a>Activer la collecte des données dans Azure Security Center

> [!NOTE]
> Depuis début juin 2017, Security Center utilise Microsoft Monitoring Agent pour collecter et stocker des données. Pour en savoir plus, consultez [Migration de plateforme Azure Security Center](security-center-platform-migration.md). Les informations contenues dans cet article représentent les fonctionnalités de Security Center après la transition vers Microsoft Monitoring Agent.
>
>

Azure Security Center collecte les données de vos machines virtuelles afin d’évaluer l’état de leur sécurité, de fournir des recommandations en matière de sécurité et de vous avertir des menaces. Lorsque vous accédez à Azure Security Center pour la première fois, vous pouvez activer la collecte de données sur toutes les machines virtuelles de votre abonnement. Si la collecte de données n’est pas activée, Security Center vous recommande de l’activer dans la stratégie de sécurité de cet abonnement.

Lorsque la collecte de données est activée, Security Center approvisionne Microsoft Monitoring Agent sur toutes les machines virtuelles Azure prises en charge existantes et sur toutes celles nouvellement créées. Microsoft Monitoring Agent analyse les différentes configurations relatives à la sécurité. En outre, le système d’exploitation déclenche des événements de journal des événements. Il peut s’agir des données suivantes : type et version de système d’exploitation, journaux de système d’exploitation (journaux d’événements Windows), processus en cours d’exécution, nom de machine, adresses IP, utilisateur connecté et ID de locataire. Microsoft Monitoring Agent lit les entrées du journal des événements et les configurations, puis copie les données dans votre espace de travail à des fins d’analyse. Microsoft Monitoring Agent copie également les fichiers de vidage sur incident dans votre espace de travail.

Si vous utilisez le niveau Gratuit de Security Center, vous pouvez désactiver la collecte de données sur les machines virtuelles dans la stratégie de sécurité. Le fait de désactiver la collecte de données limite les évaluations de sécurité pour vos machines virtuelles. Pour plus d’informations, consultez [Désactivation de la collecte de données](#disabling-data-collection). La collecte des artefacts et les captures instantanées des disques de machine virtuelle sont activées, même si la collecte de données est désactivée. La collecte de données est obligatoire pour les abonnements Security Center du niveau Standard.

> [!NOTE]
> En savoir plus sur les [niveaux tarifaires](security-center-pricing.md) Gratuit et Standard de Security Center.
>
>

## <a name="implement-the-recommendation"></a>Implémenter la recommandation

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement. Ce document n’est pas un guide pas à pas.
>
>

1. Dans le panneau **Recommandations**, sélectionnez **Activer la collecte des données pour des abonnements**.  Cette opération ouvre le panneau **Activer la collecte de données** .
   ![Panneau Recommandations][2]
2. Sur le panneau **Activer la collecte des données** , sélectionnez votre abonnement. Le panneau **Stratégie de sécurité** de cet abonnement s’ouvre.
3. Dans le panneau **Stratégie de sécurité**, sélectionnez **Activée** sous **Collecte des données** pour collecter automatiquement les journaux. L’activation de la collecte de données configure l’extension de surveillance sur toutes les machines virtuelles prises en charge, actuelles et nouvelles, de l’abonnement.
4. Sélectionnez **Enregistrer**.
5. Sélectionnez **OK**.

## <a name="disabling-data-collection"></a>Désactivation de la collecte des données
Si vous utilisez le niveau Gratuit de Security Center, vous pouvez désactiver la collecte de données sur les machines virtuelles dans la stratégie de sécurité à tout moment. La collecte de données est obligatoire pour les abonnements Security Center du niveau Standard.

1. Revenez au panneau **Security Center** et sélectionnez la mosaïque **Stratégie**. Le panneau **Stratégie de sécurité - Définir la stratégie par abonnement** s’ouvre.
   ![Sélectionner la vignette de la stratégie][5]
2. Dans le panneau **Stratégie de sécurité - Définir la stratégie par abonnement**, sélectionnez l’abonnement pour lequel vous souhaitez désactiver la collecte de données.
3. Le panneau **Stratégie de sécurité** de cet abonnement s’ouvre.  Sélectionnez **Désactivée** sous Collecte des données.
4. Sélectionnez **Enregistrer** dans le ruban supérieur.

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a montré comment implémenter la recommandation de Security Center « Activer la collecte des données ». Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md): découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md): découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions partenaires.
- [Sécurité des données Azure Security Center](security-center-data-security.md) : découvrez comment les données sont gérées et protégées dans Security Center.
* [FAQ du Centre de sécurité Azure](security-center-faq.md): forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/): découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!--Image references-->
[2]: ./media/security-center-enable-data-collection/recommendations.png
[3]: ./media/security-center-enable-data-collection/data-collection.png
[4]: ./media/security-center-enable-data-collection/storage-account.png
[5]: ./media/security-center-enable-data-collection/policy.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png

