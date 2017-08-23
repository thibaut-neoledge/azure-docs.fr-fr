---
title: "Utiliser les recommandations Azure Security Center pour améliorer la sécurité | Microsoft Docs"
description: " Découvrez comment utiliser les stratégies de sécurité et les recommandations d’Azure Security Center pour prévenir une attaque de sécurité. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: cb4a7db5666242576bf83abbf10682cc6f53ba69
ms.contentlocale: fr-fr
ms.lasthandoff: 07/11/2017


---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Utiliser les recommandations Azure Security Center pour améliorer la sécurité
Vous pouvez réduire les risques d’un incident de sécurité en configurant une stratégie de sécurité, puis en implémentant les recommandations fournies par Azure Security Center. Cet article explique comment utiliser les stratégies de sécurité et les recommandations d'Azure Security Center pour prévenir une attaque de sécurité.

> [!NOTE]
> Cet article s’appuie sur les rôles et les concepts présentés dans le [Guide de planification et de fonctionnement](security-center-planning-and-operations-guide.md) de Security Center. Il est judicieux de consulter le guide de planification avant de continuer.
>
>

## <a name="managing-security-recommendations"></a>Gestion des Recommandations de sécurité
Une stratégie de sécurité définit l’ensemble des contrôles recommandés pour les ressources d’un abonnement ou groupe de ressources spécifique. Dans Security Center, vous définissez des stratégies en fonction des exigences de sécurité de votre entreprise. Pour en savoir plus, consultez [Définir des stratégies de sécurité dans Security Center](security-center-policies.md).

Les stratégies de sécurité pour ce groupe de ressources sont héritées du niveau d’abonnement.

![Héritage d'une stratégie de sécurité][1]

Si vous avez besoin de stratégies personnalisées dans certains groupes de ressources, vous pouvez désactiver l’héritage dans le groupe de ressources. Pour le désactiver, définissez la valeur Héritage sur Unique dans le panneau de la stratégie de sécurité puis personnalisez les contrôles pour lesquels Security Center affiche des recommandations.

Par exemple, si certaines de vos charges de travail ne nécessitent pas la stratégie SQL Database Transparent Data Encryption (TDE), désactivez cette dernière au niveau de l’abonnement et ne l’activez que dans les groupes de ressources qui la requièrent.

> [!NOTE]
> Si un conflit se produit entre la stratégie de niveau abonnement et la stratégie de niveau groupe de ressources, la stratégie de niveau groupe de ressources est prioritaire.
>
>

Le Centre de sécurité analyse l’état de sécurité de vos ressources Azure. Lorsque Security Center identifie des failles de sécurité potentielles, il crée des recommandations en fonction des contrôles définis dans la stratégie de sécurité. Ces recommandations vous guident tout au long du processus de configuration des contrôles de sécurité nécessaires.

Les recommandations de stratégie actuelles de Security Center se concentrent sur les mises à jour du système, la configuration du système d’exploitation, les groupes de sécurité réseau sur les sous-réseaux et les machines virtuelles, l’audit de base de données SQL, SQL Database TDE, et les pare-feu d’applications web. Pour obtenir les recommandations Security Center les plus récentes, consultez [Gestion des recommandations de sécurité dans Security Center](security-center-recommendations.md).

## <a name="scenario"></a>Scénario
Ce scénario vous montre comment utiliser Security Center pour réduire les risques d’un incident de sécurité important en surveillant les recommandations Security Center et en prenant les mesures nécessaires. Le scénario utilise la société fictive Contoso et les rôles présentés dans le [Guide de planification et de fonctionnement](security-center-planning-and-operations-guide.md#security-roles-and-access-controls) de Security Center. Les rôles représentent des personnes et des équipes qui peuvent utiliser Security Center pour exécuter différentes tâches liées à la sécurité. Ces rôles sont les suivants :

![Rôles du scénario][2]

Contoso a récemment migré certaines de ses ressources locales vers Azure. Contoso souhaite implémenter et gérer des protections qui limitent sa vulnérabilité face à une attaque de sécurité sur ses ressources dans le cloud.

## <a name="recommended-solution"></a>Solution recommandée
Une solution consiste à utiliser Security Center pour empêcher et détecter les failles de sécurité. Contoso peut accéder à Security Center via son abonnement Azure. Le [niveau Gratuit](security-center-pricing.md) de Security Center est automatiquement activé sur tous les abonnements Azure, et la collecte de données est activée sur toutes les machines virtuelles de son abonnement.

David, membre de l'équipe de sécurité informatique de Contoso, configure une **stratégie de sécurité** à l’aide de Security Center. Security Center analyse l’état de sécurité des ressources Azure de Contoso. Lorsque Security Center identifie des failles de sécurité potentielles, il crée des **recommandations** en fonction des contrôles définis dans la stratégie de sécurité.

Jeff, qui possède une charge de travail dans le cloud, est responsable de la maintenance des protections conformément à la stratégie de sécurité de Contoso. Jeff peut surveiller les recommandations créées par Security Center pour appliquer des protections. Ces recommandations guident Jeff tout au long du processus de configuration des contrôles de sécurité nécessaires.

Pour que Jeff puisse implémenter, maintenir les protections et éliminer les failles de sécurité, il doit :

- Surveiller les recommandations de sécurité fournies par Security Center
- Évaluer les recommandations de sécurité et décider s’il doit les appliquer ou les ignorer
- Appliquer les recommandations de sécurité

Examinons comment Jeff utilise les recommandations de Security Center tout au long du processus de configuration des contrôles afin d'éliminer les failles de sécurité.

## <a name="how-to-implement-this-solution"></a>Mise en œuvre de cette solution
Jeff se connecte au [portail Azure](https://azure.microsoft.com/features/azure-portal/) et ouvre la console Security Center. Dans le cadre de ses activités de surveillance quotidiennes, il vérifie si des recommandations de sécurité sont disponibles en procédant comme suit :

1. Jeff sélectionne la mosaïque **Recommandations** pour ouvrir le panneau **Recommandations**.
   ![Sélectionner la mosaïque Recommandations][3]
2. Jeff passe en revue la liste des recommandations. Il constate que Security Center a fourni la liste des recommandations par ordre de priorité, de la priorité la plus élevée à la priorité la plus basse. Il décide de traiter une recommandation de priorité élevée dans la liste. Il sélectionne **Installer Endpoint Protection** dans le panneau **Recommandations**.
3. Le panneau **Installer Endpoint Protection** s’ouvre en affichant une liste de machines virtuelles sans logiciel anti-programme malveillant activé. Jeff passe en revue la liste des machines virtuelles, sélectionne toutes les machines virtuelles puis choisit **Installer sur 3 machines virtuelles**.
   ![Installer Endpoint Protection][4]
4. Le panneau **Sélectionner Endpoint Protection** s’ouvre et offre à Jeff deux solutions anti-programmes malveillants. Jeff sélectionne la solution **Microsoft Antimalware**.
5. Des informations supplémentaires sur la solution anti-programme malveillant s’affichent. Jeff sélectionne **Créer**.
   ![Microsoft Antimalware][5]
6. Jeff entre les paramètres de configuration nécessaires dans le panneau **Installer** puis sélectionne **OK**.

[Microsoft Antimalware](../security/azure-security-antimalware.md) est maintenant activé sur les machines virtuelles sélectionnées.

Jeff continue de parcourir les recommandations de priorité élevée et moyenne, en choisissant de les appliquer ou pas. Jeff consulte l'article [Gestion des recommandations de sécurité](security-center-recommendations.md) pour comprendre les recommandations et leur impact en cas de mise en œuvre.

Jeff apprend que [Microsoft Security Response Center (MSRC)](../security/azure-security-response-center.md) effectue certaines analyses de sécurité sur l'infrastructure et le réseau Azure et reçoit des informations sur les menaces et des plaintes pour mauvaise utilisation provenant de tiers. Si Jeff fournit les coordonnées du contact de sécurité de l’abonnement Azure de Contoso, Microsoft contacte Contoso si MSRC découvre que les données client de Contoso ont été consultées par un pirate ou une personne non autorisée. Jeff applique maintenant la recommandation **Fournir des informations de contact de sécurité** (une recommandation de priorité moyenne dans la liste des recommandations ci-dessus).

1. Jeff sélectionne **Fournir des informations de contact de sécurité** dans le panneau **Recommandations**, ce qui ouvre le panneau **Fournir des informations de contact de sécurité**.
2. Jeff sélectionne l’abonnement Azure pour lequel fournir les informations du contact. Un second panneau **Fournissez les détails du contact de sécurité** s’ouvre.
   ![Informations de contact de sécurité][6]
3. Sur le second panneau **Fournir des informations de contact de sécurité**, Jeff saisit les informations suivantes :

  - Les adresses de messagerie du contact de sécurité, séparées par des virgules (il n’y a pas de limite au nombre d’adresses de messagerie)
  - Un numéro de téléphone du contact de sécurité

4. Jeff active également l’option **Send me emails about alerts** (M’envoyer des messages électroniques concernant les alertes) pour recevoir des messages électroniques concernant les alertes de gravité élevée.
5. Jeff sélectionne **OK** pour appliquer les informations du contact de sécurité à l'abonnement de Contoso.

Pour finir, Jeff passe en revue la recommandation de faible priorité **Corriger les vulnérabilités du système d’exploitation** et décide que cette recommandation n’est pas applicable. Il souhaite ignorer la recommandation. Jeff sélectionne les trois points qui apparaissent à droite, puis sélectionne **Dismiss** (Ignorer).
   ![Ignorer une recommandation][7]

## <a name="conclusion"></a>Conclusion
Surveiller les recommandations dans Security Center peut vous aider à éliminer les failles de sécurité avant qu'une attaque ne se produise. Vous pouvez empêcher un incident de sécurité en implémentant et en gérant des protections à l'aide des stratégies de sécurité de Security Center.

<!--Image references-->
[1]: ./media/security-center-using-recommendations/security-center-policy-inheritance.png
[2]: ./media/security-center-using-recommendations/scenario-roles.png
[3]: ./media/security-center-using-recommendations/select-recommendations-tile.png
[4]: ./media/security-center-using-recommendations/install-endpoint-protection.png
[5]:./media/security-center-using-recommendations/microsoft-antimalware.png
[6]: ./media/security-center-using-recommendations/provide-security-contact-details.png
[7]: ./media/security-center-using-recommendations/dismiss-recommendation.png

