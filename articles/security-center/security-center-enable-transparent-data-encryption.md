---
title: Activation du chiffrement transparent des données dans Azure Security Center | Microsoft Docs
description: Ce document vous montre comment implémenter la recommandation de l’Azure Security Center **Activer le chiffrement transparent des données**.
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
# Activation du chiffrement transparent des données dans Azure Security Center
Azure Security Center vous recommandera d’activer le chiffrement transparent des données (TDE) sur les bases de données SQL si ce n’est déjà fait. TDE protège vos données et vous aide à répondre aux exigences de conformité en chiffrant votre base de données, les sauvegardes associées et les journaux des transactions au repos, sans modifier votre application. Pour en savoir plus, consultez [Chiffrement transparent des données avec la base de données SQL Azure](https://msdn.microsoft.com/library/dn948096).

Cette recommandation s’applique uniquement au service SQL Azure, elle ne concerne pas SQL en cours d’exécution sur vos machines virtuelles.

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.
> 
> 

## Implémenter la recommandation
1. Dans le panneau **Recommandations** , sélectionnez **Activer le chiffrement transparent des données**. ![Activer le chiffrement transparent des données][1]
2. Le panneau **Activer le chiffrement transparent des données sur les bases de données SQL** apparaît. Sélectionnez une base de données SQL sur laquelle activer le chiffrement transparent des données. ![Sélectionner la base de données SQL sur laquelle activer le chiffrement transparent des données][2]
3. Dans le panneau **Chiffrement transparent des données**, sélectionnez **ON** sous Chiffrement des données et sélectionnez **Enregistrer** dans le ruban supérieur du panneau. ![Activer le chiffrement transparent des données][3]
   
   Une fois TDE activé sur la base de données SQL sélectionnée, l’**l’état de chiffrement** affiche **Encrypted** (Chiffré).
   
   ![État du chiffrement][4]

## Voir aussi
Cet article vous a montré comment implémenter la recommandation du Centre de sécurité « Activer le chiffrement transparent des données ». Pour plus d’informations sur SQL TDE, consultez les rubriques suivantes :

* [Chiffrement transparent des données avec Azure SQL Database](https://msdn.microsoft.com/library/dn948096)
* [Prise en main du chiffrement transparent des données (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaires.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]: ./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png

<!---HONumber=AcomDC_0803_2016-->