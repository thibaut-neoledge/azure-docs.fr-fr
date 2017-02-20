---
title: Activation de Transparent Data Encryption dans Azure Security Center | Microsoft Docs
description: "Ce document vous explique comment implémenter la recommandation de l’Azure Security Center **Activer le chiffrement transparent des données**."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: e4be8a0e-2118-4ee9-a266-69e52d9f7f8e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: f532f1aea9b8c4be6707862be4001099aa3c1d32
ms.openlocfilehash: 2a2963affdbff3710ad08f86c6ed4e6304335559


---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Activation de Transparent Data Encryption dans Azure Security Center
Azure Security Center vous recommande d’activer TDE (Transparent Data Encryption) sur les bases de données SQL si ce n’est déjà fait. TDE protège vos données et vous aide à répondre aux exigences de conformité en chiffrant votre base de données, les sauvegardes associées et les journaux des transactions au repos, sans modifier votre application. Pour en savoir plus, consultez [Transparent Data Encryption avec Azure SQL Database](https://msdn.microsoft.com/library/dn948096).

Cette recommandation s’applique uniquement au service SQL Azure, elle ne concerne pas SQL en cours d’exécution sur vos machines virtuelles.

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement.  Il ne s’agit pas d’un guide pas à pas.
>
>

## <a name="implement-the-recommendation"></a>Implémenter la recommandation
1. Dans le panneau **Recommandations**, sélectionnez **Activer Transparent Data Encryption**.
   ![Activer Transparent Data Encryption][1]
2. Le panneau **Activer Transparent Data Encryption sur les bases de données SQL** apparaît. Sélectionnez une base de données SQL sur laquelle activer Transparent Data Encryption.
   ![Sélectionner la base de données SQL sur laquelle activer Transparent Data Encryption][2]
3. Dans le panneau **Transparent Data Encryption**, sélectionnez **ON** sous Chiffrement des données et sélectionnez **Enregistrer** dans le ruban supérieur du panneau.
   ![Activer Transparent Data Encryption][3]

   Une fois TDE activé dans la base de données SQL sélectionnée, **l’état de chiffrement** affiche **Chiffré**.    

   ![État du chiffrement][4]

## <a name="see-also"></a>Voir aussi
Cet article vous a montré comment implémenter la recommandation du Centre de sécurité « Activer Transparent Data Encryption ». Pour plus d’informations sur SQL TDE, consultez les rubriques suivantes :

* [Transparent Data Encryption avec Azure SQL Database](https://msdn.microsoft.com/library/dn948096)
* [Prise en main du chiffrement transparent des données (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez comment les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions partenaires.
* [FAQ sur Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png



<!--HONumber=Feb17_HO1-->


