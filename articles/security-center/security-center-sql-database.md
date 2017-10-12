---
title: Azure Security Center et service SQL Database | Microsoft Docs
description: "Cet article explique comment Security Center peut vous aider à sécuriser vos bases de données dans Azure SQL Database."
services: sql-database
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: f109adfd-daed-4257-9692-2042a1399480
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 46dd298a5664d914e55d45c5b7599d5983287476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Azure Security Center et service SQL Database
[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) vous aide à vous empêcher, détecter et répondre aux menaces. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements Azure, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

Cet article explique comment Security Center peut vous aider à sécuriser vos bases de données dans Azure SQL Database.

## <a name="why-use-security-center"></a>Pourquoi utiliser Security Center ?
Security Center vous aide à protéger les données dans SQL Database en fournissant une visibilité sur la sécurité de tous vos serveurs et bases de données. Avec Security Center, vous pouvez :

* définir des stratégies pour l’audit et le chiffrement de SQL Database ;
* surveiller la sécurité des ressources SQL Database dans tous vos abonnements ;
* identifier et corriger rapidement les problèmes de sécurité ;
* intégrer les alertes de la [détection de menaces Azure SQL Database](../sql-database/sql-database-threat-detection.md).

En plus de vous aider à protéger vos ressources SQL Database, Security Center fournit également l’analyse de la sécurité et la gestion des machines virtuelles, services cloud, App Services et réseaux virtuels Azure, notamment. Pour en savoir plus sur Security Center, cliquez [ici](security-center-intro.md).

## <a name="prerequisites"></a>Conditions préalables
Pour utiliser le Centre de sécurité, vous devez disposer d’un abonnement à Microsoft Azure. Le niveau Gratuit de Security Center est activé avec votre abonnement. Pour plus d’informations sur les niveaux Gratuit et Standard de Security Center, consultez [Tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center/).

Le Centre de sécurité prend en charge l’accès en fonction du rôle. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle Azure Active Directory](../active-directory/role-based-access-control-configure.md). La FAQ de Security Center fournit des informations sur la [gestion des autorisations dans Security Center](security-center-faq.md#permissions).

## <a name="access-security-center"></a>Accéder au Centre de sécurité
Le Centre de sécurité est accessible à partir du [portail Azure](https://azure.microsoft.com/features/azure-portal/). [Connectez-vous au portail](https://portal.azure.com/) et sélectionnez **l’option Security Center**.

![Vidéos de Security Center][1]

Le panneau **Security Center** s’ouvre.
![Panneau Security Center][2]

## <a name="set-security-policy"></a>Définir une stratégie de sécurité
Une stratégie de sécurité définit l’ensemble des contrôles recommandés pour les ressources d’un abonnement ou groupe de ressources spécifique. Dans Security Center, vous devez définir des stratégies pour vos abonnements ou groupes de ressources en fonction des exigences de sécurité de votre société et du type d’applications ou du niveau de confidentialité des données de chaque abonnement.

Vous pouvez définir une stratégie pour afficher les recommandations d’audit SQL et de chiffrement SQL Transparent Data Encryption (TDE).

* Lorsque vous activez **l’audit et la détection des menaces SQL**, Security Center recommande l’activation de l’audit de l’accès à Azure SQL Database à des fins de conformité, de détection avancée et d’examen.
* Lorsque vous activez **SQL Transparent Data Encryption**, Security Center recommande l’activation du chiffrement au repos pour votre base de données Azure SQL Database, ainsi que pour les sauvegardes associées et les fichiers journaux de transaction.

Pour définir une stratégie de sécurité, sélectionnez la vignette **Stratégie** dans le panneau Security Center. Sur le panneau **Stratégie de sécurité**, sélectionnez l’abonnement pour lequel vous souhaitez activer la stratégie de sécurité. Sélectionnez **Stratégie de prévention** et cliquez sur **Activé** pour les recommandations de sécurité que vous voulez utiliser pour cet abonnement.
![Stratégie de sécurité][3]

Pour plus d’informations, consultez [Définir des stratégies de sécurité](security-center-policies.md).

## <a name="manage-security-recommendation"></a>Gérer les recommandations de sécurité
Le Centre de sécurité analyse l’état de sécurité de vos ressources Azure à intervalles réguliers. Lorsqu’il identifie des failles de sécurité potentielles, il crée des recommandations. Ces recommandations vous guident tout au long du processus de configuration des contrôles nécessaires.

Une fois que vous avez défini une stratégie de sécurité, Security Center analyse l’état de la sécurité de vos ressources pour identifier les vulnérabilités potentielles. Les recommandations s’affichent dans un tableau où chaque ligne correspond à une recommandation. Utilisez le tableau suivant comme référence pour comprendre les recommandations disponibles pour Azure SQL Database, et le rôle de chaque recommandation si vous l’appliquez. En sélectionnant une recommandation, vous accédez à un article qui explique comment l’implémenter dans Security Center.

| Recommandation | Description |
| --- | --- |
| [Activer l’audit et la détection des menaces sur les serveurs SQL](security-center-enable-auditing-on-sql-servers.md) |Recommande l’activation de l’audit et de la détection des menaces pour les serveurs SQL Database. (Service SQL Database uniquement. N’inclut pas Microsoft SQL Server en cours d’exécution sur vos machines virtuelles.) |
| [Activer l’audit et la détection des menaces sur les bases de données SQL](security-center-enable-auditing-on-sql-databases.md) |Recommande l’activation de l’audit et de la détection des menaces pour les bases de données SQL Database. (Service SQL Database uniquement. N’inclut pas Microsoft SQL Server en cours d’exécution sur vos machines virtuelles.) |
| [Activer Transparent Data Encryption](security-center-enable-transparent-data-encryption.md) |Recommande l’activation du chiffrement pour les bases de données SQL. (Service SQL Database uniquement.) |

Pour afficher les recommandations pour vos ressources Azure, sélectionnez la vignette **Recommandations** dans le panneau Security Center. Dans le panneau **Recommandations**, sélectionnez une recommandation pour en afficher les détails. Dans cet exemple, nous allons sélectionner **Activer l’audit et la détection des menaces sur les serveurs SQL**.

![Recommandations][4]

Comme illustré ci-dessous, Security Center vous indique les serveurs SQL pour lesquels l’audit et la détection des menaces ne sont pas activés. Une fois que vous avez activé l’audit, vous pouvez configurer les paramètres de détection des menaces et de messagerie pour recevoir des alertes de sécurité. La détection des menaces vous alerte si des activités de base de données anormales, indiquant la présence potentielle de menaces de sécurité pour la base de données, sont détectées. Les alertes sont affichées dans le tableau de bord de Security Center.
![Audit et détection des menaces][5]

Suivez les étapes de la rubrique [Détection de menaces pour les bases de données SQL dans le portail Azure](../sql-database/sql-database-threat-detection-portal.md) pour activer et configurer la détection des menaces (Threat Detection) et configurer la liste des adresses électroniques qui recevront les alertes de sécurité lors de la détection d’activités anormales.

Pour en savoir plus sur les recommandations, consultez [Gestion des recommandations de sécurité](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Surveiller l’intégrité de la sécurité
Une fois que vous avez activé les [stratégies de sécurité](security-center-policies.md) pour les ressources d’un abonnement, Azure Security Center analyse la sécurité de vos ressources afin d’identifier les vulnérabilités potentielles.  Vous pouvez visualiser l’état de sécurité de vos ressources dans la vignette **Intégrité de la sécurité des ressources**. Lorsque vous cliquez sur l’option **Données** de la vignette **Intégrité de la sécurité des ressources**, le panneau **Ressources de données** s’ouvre et affiche des recommandations SQL relatives aux problèmes, telles que la désactivation de la fonction d’audit ou de Transparent Data Encryption. Il contient également des recommandations pour l’état général de la base de données.
![Intégrité de la sécurité des ressources][6]

Pour plus d’informations, consultez [Contrôle de l’intégrité de la sécurité](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Gérer et répondre aux alertes de sécurité
Security Center collecte, analyse et intègre automatiquement les données de journaux provenant de la [Détection des menaces Azure SQL](../sql-database/sql-database-threat-detection.md), ainsi que d’autres ressources Azure, pour détecter les menaces réelles et réduire le nombre de faux positifs. Une liste hiérarchisée d’alertes de sécurité est affichée dans le Centre de sécurité, ainsi que les informations nécessaires pour trouver rapidement la cause d’une attaque et des recommandations sur la façon d’y remédier.

Pour consulter les alertes, sélectionnez la vignette **Alertes de sécurité** dans le panneau Security Center. Dans le panneau **Alertes de sécurité**, sélectionnez une alerte de sécurité pour en savoir plus sur les événements qui l’ont déclenchée et, le cas échéant, les étapes à suivre pour y remédier. Dans cet exemple, nous allons sélectionner **Injection potentielle de code SQL**.
![Alertes de sécurité][7]

Comme indiqué ci-dessous, Security Center fournit des informations supplémentaires qui permettent d’en savoir plus sur ce qui a déclenché l’alerte, sur la ressource cible, sur l’adresse IP source (le cas échéant) et sur la manière de remédier au problème.
![Injection potentielle de code SQL][8]

Pour en savoir plus, consultez [Gestion et résolution des alertes de sécurité](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Étapes suivantes
* [FAQ Security Center](security-center-faq.md) : découvrez les réponses aux questions les plus souvent posées à propos de l’utilisation de ce service.
* [Guide des opérations et de planification de Security Center](security-center-planning-and-operations-guide.md) : couvre un ensemble d’étapes et de tâches pour optimiser votre utilisation de Security Center en fonction des critères de sécurité et du modèle de gestion cloud de votre entreprise.
* [Sécurité des données de Security Center](security-center-data-security.md) : découvrez comment Security Center collecte et traite des données sur vos ressources Azure, notamment des informations de configuration, des métadonnées, des journaux des événements, des fichiers de vidage sur incident et plus encore.
* [Gestion des incidents de sécurité](security-center-incident.md) : apprenez à utiliser la fonction d’alerte de sécurité dans Security Center pour mieux gérer les incidents de sécurité.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
