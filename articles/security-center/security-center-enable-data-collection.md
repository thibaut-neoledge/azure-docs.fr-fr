---
title: Activer la collecte des données dans Azure Security Center | Microsoft Docs
description: " Découvrez comment activer la collecte des données dans Azure Security Center. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2016
ms.author: terrylan

---
# Activer la collecte des données dans Azure Security Center
Pour aider les clients à prévenir les menaces, à les détecter et à y répondre, Azure Security Center collecte et traite des données concernant vos machines virtuelles Azure, notamment des informations de configuration, des métadonnées, des journaux des événements et bien plus. Lorsque vous accédez à Security Center pour la première fois, la collecte des données est activée sur toutes les machines virtuelles de votre abonnement. La collecte des données est recommandée, mais vous pouvez refuser cette fonctionnalité en la désactivant dans la stratégie de Security Center (consultez [Désactivation de la collecte des données](#disabling-data-collection)). Si vous désactivez la collecte des données, Security Center vous recommandera d’activer la collecte des données dans la stratégie de sécurité de cet abonnement.

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.
> 
> 

## Implémenter la recommandation
1. Sélectionnez la vignette **Recommandations** dans le panneau **Security Center**. Le panneau **Recommandations** s’ouvre. ![Panneau Centre de sécurité][1]
2. Sur le panneau **Recommandations**, sélectionnez **Activer la collecte des données pour les abonnements**. Cette opération ouvre le panneau **Activer la collecte des données**. ![Panneau Recommandations][2]
3. Sur le panneau **Activer la collecte des données**, sélectionnez votre abonnement. Le panneau **Stratégie de sécurité** de cet abonnement s’ouvre.
4. Dans le panneau **Stratégie de sécurité**, sélectionnez **Activée** sous **Collecte des données** pour collecter automatiquement les journaux. L’activation de l’option Collecte des données activera également l’extension de la surveillance sur toutes les machines virtuelles prises en charge, actuelles et nouvelles, dans l’abonnement. ![Panneau Stratégie de sécurité][3]
5. Sélectionnez **Enregistrer**.
6. Sélectionnez **Choisir un compte de stockage par région**. Pour chaque région où s’exécutent des machines virtuelles, vous devez choisir le compte de stockage où doivent être stockées les données collectées à partir de ces machines virtuelles. Si vous ne choisissez pas un compte de stockage pour chaque région, il sera automatiquement créé pour vous. Dans cet exemple, nous allons choisir **newstoracct**. Vous pouvez modifier le compte de stockage ultérieurement en revenant à la stratégie de sécurité de votre abonnement et en choisissant un autre compte de stockage. ![Choisir un compte de stockage][4]
7. Sélectionnez **OK**.

> [!NOTE]
> Nous vous recommandons d’activer la collecte de données et de choisir un compte de stockage au niveau de l’abonnement en premier. Bien que vous puissiez définir les stratégies de sécurité au niveau du groupe de ressources et au niveau de l’abonnement Azure, la configuration de la collecte des données et du compte de stockage intervient uniquement au niveau de l’abonnement.
> 
> 

## Après l’activation de la collecte des données
La collecte des données peut être activée via l’agent de surveillance Azure et via l’extension Surveillance de la sécurité Azure. L’extension Surveillance de la sécurité Azure analyse différentes configurations de sécurité et les envoie sous forme de traces de [Suivi d’événements pour Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). En outre, le système d’exploitation crée des entrées de journal des événements. L’agent de surveillance Azure lit les entrées du journal des événements et les traces ETW, puis les copie dans votre compte de stockage pour les analyser. L’agent de surveillance copie également les fichiers de vidage sur incident sur votre compte de stockage. Le compte de stockage en question est celui que vous avez configuré dans la stratégie de sécurité.

## Désactivation de la collecte des données
Vous pouvez désactiver la collecte des données à tout moment, ce qui entraîne la suppression des agents de surveillance précédemment installés par Security Center. Vous devez sélectionner un abonnement pour désactiver la collecte des données.

> [!NOTE]
> Vous pouvez définir les stratégies de sécurité au niveau du groupe de ressources et de l’abonnement Azure, mais vous devez sélectionner un abonnement pour désactiver la collecte des données.
> 
> 

1. Revenez au panneau **Security Center** et sélectionnez la vignette **Stratégie**. Le panneau **Stratégie de sécurité – Définir une stratégie par abonnement ou groupe de ressources** s’ouvre. ![Sélectionner la vignette de la stratégie][5]
2. Dans le panneau **Stratégie de sécurité – Définir une stratégie par abonnement ou groupe de ressources**, sélectionnez l’abonnement pour lequel vous souhaitez désactiver la collecte des données. ![Sélectionner l’abonnement pour lequel désactiver la collecte des données][6]
3. Le panneau **Stratégie de sécurité** de cet abonnement s’ouvre. Sélectionnez **Désactivée** sous Collecte des données.
4. Sélectionnez **Enregistrer** dans le ruban supérieur.
5. Sélectionnez **Supprimer des agents** dans le ruban supérieur pour supprimer les agents des machines virtuelles existantes.

## Voir aussi
Cet article vous a montré comment implémenter la recommandation de Security Center « Activer la collecte des données ». Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaires.
* [FAQ d’Azure Security Center](security-center-faq.md) : consultez le forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/security-center-blade.png
[2]: ./media/security-center-enable-data-collection/recommendations.png
[3]: ./media/security-center-enable-data-collection/data-collection.png
[4]: ./media/security-center-enable-data-collection/storage-account.png
[5]: ./media/security-center-enable-data-collection/policy.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png

<!---HONumber=AcomDC_0803_2016-->