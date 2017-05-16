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
ms.date: 04/12/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: b942f934ecb25083d779096dc628e342870edc26
ms.contentlocale: fr-fr
ms.lasthandoff: 04/13/2017


---
# <a name="enable-data-collection-in-azure-security-center"></a>Activer la collecte des données dans Azure Security Center
Pour aider les clients à prévenir les menaces, à les détecter et à y répondre, Azure Security Center collecte et traite des données concernant vos machines virtuelles Azure, notamment des informations de configuration, des métadonnées et des journaux d’événements. Lorsque vous accédez au Centre de sécurité pour la première fois, la collecte de données est activée sur toutes les machines virtuelles de votre abonnement. La collecte de données est recommandée, mais vous pouvez la refuser en désactivant cette fonctionnalité dans la stratégie de Security Center (consultez la page [Désactiver la collecte de données](#disabling-data-collection)). Si vous désactivez la collecte de données, Security Center vous recommande d’activer la collecte de données dans la stratégie de sécurité de cet abonnement.

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.
>
>

## <a name="implement-the-recommendation"></a>Implémenter la recommandation
1. Sélectionnez la mosaïque **Recommandations** dans le panneau **Security Center**.  Le panneau **Recommandations** s’ouvre.
   ![Panneau Security Center][1]
2. Dans le panneau **Recommandations**, sélectionnez **Activer la collecte des données pour des abonnements**.  Cette opération ouvre le panneau **Activer la collecte de données** .
   ![Panneau Recommandations][2]
3. Sur le panneau **Activer la collecte des données** , sélectionnez votre abonnement. Le panneau **Stratégie de sécurité** de cet abonnement s’ouvre.
4. Dans le panneau **Stratégie de sécurité**, sélectionnez **Activée** sous **Collecte des données** pour collecter automatiquement les journaux. L’activation de la collecte de données configure l’extension de surveillance sur toutes les machines virtuelles prises en charge, actuelles et nouvelles, de l’abonnement.

   ![Panneau Stratégie de sécurité][3]

5. Sélectionnez **Enregistrer**.
6. Sélectionnez **Choisir un compte de stockage par région**. Pour chaque région où s’exécutent des machines virtuelles, vous devez choisir le compte de stockage où doivent être stockées les données collectées à partir de ces machines virtuelles. Si vous ne choisissez pas un compte de stockage pour chaque région, un compte de stockage est créé pour vous et placé dans le groupe de ressources securitydata. Dans cet exemple, nous choisissons **newstoracct**. Vous pouvez modifier le compte de stockage ultérieurement en revenant à la stratégie de sécurité de votre abonnement et en choisissant un autre compte de stockage.
   ![Choisir un compte de stockage][4]
7. Sélectionnez **OK**.

> [!NOTE]
> Nous vous recommandons d’activer la collecte de données et de choisir un compte de stockage au niveau de l’abonnement en premier. Bien que vous puissiez définir les stratégies de sécurité au niveau du groupe de ressources et au niveau de l’abonnement Azure, la configuration de la collecte des données et du compte de stockage intervient uniquement au niveau de l’abonnement.
>
>

## <a name="after-data-collection-is-enabled"></a>Après l’activation de la collecte des données
La collecte des données peut être activée via l’agent de surveillance Azure et via l’extension Surveillance de la sécurité Azure. L’extension Surveillance de la sécurité Azure analyse différentes configurations de sécurité et les envoie sous forme de traces de [Suivi d’événements pour Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). En outre, le système d’exploitation crée des entrées de journal des événements. L’agent de surveillance Azure lit les entrées du journal des événements et les traces ETW, puis les copie dans votre compte de stockage pour les analyser. L’agent de surveillance copie également les fichiers de vidage sur incident sur votre compte de stockage. Le compte de stockage en question est celui que vous avez configuré dans la stratégie de sécurité.

## <a name="disabling-data-collection"></a>Désactivation de la collecte des données
Vous pouvez désactiver la collecte de données à tout moment, ce qui entraîne la suppression automatique des agents de surveillance précédemment installés par Security Center. Vous devez sélectionner un abonnement pour désactiver la collecte des données.

> [!NOTE]
> Vous pouvez définir les stratégies de sécurité au niveau du groupe de ressources et de l’abonnement Azure, mais vous devez sélectionner un abonnement pour désactiver la collecte des données.
>
>

1. Revenez au panneau **Security Center** et sélectionnez la mosaïque **Stratégie**. Le panneau **Stratégie de sécurité – Définir une stratégie par abonnement ou groupe de ressources** s’ouvre.
   ![Sélectionner la vignette de la stratégie][5]
2. Dans le panneau **Stratégie de sécurité – Définir une stratégie par abonnement ou groupe de ressources** , sélectionnez l’abonnement pour lequel vous souhaitez désactiver la collecte des données.
   ![Sélectionner l’abonnement pour lequel désactiver la collecte des données][6]
3. Le panneau **Stratégie de sécurité** de cet abonnement s’ouvre.  Sélectionnez **Désactivée** sous Collecte des données.
4. Sélectionnez **Enregistrer** dans le ruban supérieur.


## <a name="next-steps"></a>Étapes suivantes
Cet article vous a montré comment implémenter la recommandation de Security Center « Activer la collecte des données ». Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md): découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md): découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaires.
* [FAQ du Centre de sécurité Azure](security-center-faq.md): forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/): découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/security-center-blade.png
[2]: ./media/security-center-enable-data-collection/recommendations.png
[3]: ./media/security-center-enable-data-collection/data-collection.png
[4]: ./media/security-center-enable-data-collection/storage-account.png
[5]: ./media/security-center-enable-data-collection/policy.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png

