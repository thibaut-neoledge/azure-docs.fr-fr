---
title: FAQ sur la migration de plateforme Security Center | Microsoft Docs
description: Ce FAQ concerne la migration de plateforme Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: 540180e7d6cd02dfa1f3cac8ccd343e965ded91b
ms.openlocfilehash: 2ffbaca614d667db565197f3c13b1658fffc2a7c
ms.contentlocale: fr-fr
ms.lasthandoff: 08/16/2017

---
# <a name="security-center-platform-migration-faq"></a>FAQ sur la migration de plateforme Security Center
Au début du mois de juin 2017, Azure Security Center a commencé à utiliser Microsoft Monitoring Agent pour collecter et stocker des données. Pour en savoir plus, consultez [Migration de plateforme Azure Security Center](security-center-platform-migration.md). Ce FAQ concerne la migration de plateforme.

## <a name="data-collection-agents-and-workspaces"></a>Collecte de données, agents et espaces de travail

### <a name="how-is-data-collected"></a>Comment les données sont-elles collectées ?
Security Center utilise Microsoft Monitoring Agent pour collecter des données de sécurité sur vos machines virtuelles. Les données de sécurité incluent des informations sur les configurations de sécurité, qui servent à identifier les vulnérabilités, et les événements de sécurité, qui sont utilisés pour détecter les menaces. Les données collectées par l’agent sont stockées soit dans un espace de travail Log Analytics existant connecté à la machine virtuelle, soit dans un espace de travail créé par Security Center. Lorsque Security Center crée un nouvel espace de travail, la géolocalisation de la machine virtuelle est prise en compte.

> [!NOTE]
> Microsoft Monitoring Agent est l’agent utilisé par Operations Management Suite (OMS), le service Log Analytics et System Center Operations Management (SCOM).
>
>

Lorsque la collecte de données est activée pour la première fois ou lorsque vous migrez vos abonnements, Security Center vérifie si Microsoft Monitoring Agent est déjà installé comme une extension Azure sur chacune de vos machines virtuelles. Si l’agent Microsoft Monitoring Agent n’est pas installé, Security Center :

- installera l’agent Microsoft Monitoring sur la machine virtuelle
   - et si un espace de travail créé par Security Center existe déjà dans la même zone géographique que la machine virtuelle, l’agent est connecté à cet espace de travail
   - tandis que s’il n’existe pas d’espace de travail, Security Center crée un nouveau groupe de ressources et un espace de travail par défaut dans cette zone géographique et connecte l’agent à ce dernier. La convention d’affectation de noms pour l’espace de travail et le groupe de ressources est la suivante :

       Espace de travail : DefaultWorkspace-[ID d’abonnement]-[zone géographique]

       Groupe de ressources : DefaultResouceGroup-[zone géographique]
- installer une solution Security Center sur l’espace de travail

L’emplacement de l’espace de travail est basé sur l’emplacement de la machine virtuelle. Pour plus d’informations, consultez [Sécurité des données](security-center-data-security.md).

> [!NOTE]
> Avant la migration de plateforme, Security Center collecte des données de sécurité sur vos machines virtuelles à l’aide d’Azure Monitoring Agent et stocke des données dans le compte de stockage. Après la migration de la plateforme, Security Center utilise Microsoft Monitoring Agent et l’espace de travail pour collecter et stocker les mêmes données. Le compte de stockage peut être supprimé après la migration.
>
>

### <a name="am-i-billed-for-log-analytics-or-oms-on-the-workspaces-created-by-security-center"></a>Est-ce que je suis facturé pour Log Analytics ou OMS lorsqu’ils sont installés sur des espaces de travail créés par Security Center ?
Non. Les espaces de travail créés par Security Center, bien qu’ils soient configurés pour une facturation OMS par nœud, n’entraînent pas de frais OMS. La facturation Security Center est toujours basée sur la stratégie de sécurité Security Center et les solutions installées sur l’espace de travail :

- **Niveau Gratuit** : Security Center installe la solution « SecurityCenterFree » sur l’espace de travail par défaut. Vous n’êtes pas facturé pour le niveau gratuit.
- **Niveau Standard** : Security Center installe les solutions « SecurityCenterFree » et « Security » sur l’espace de travail par défaut.

Pour plus d’informations sur la tarification, consultez la page de [tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center/). La page de tarification traite les modifications apportées à la facturation au prorata et au stockage des données de sécurité à partir de juin 2017.

> [!NOTE]
> Le niveau tarifaire OMS des espaces de travail créés par Security Center n’affecte pas la facturation Security Center.
>
>

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Puis-je supprimer les espaces de travail par défaut créés par Security Center ?
**Il n’est pas recommandé de supprimer l’espace de travail par défaut.** Security Center utilise les espaces de travail par défaut pour stocker les données de sécurité de vos machines virtuelles.  Si vous supprimez un espace de travail, Security Center ne peut pas collecter ces données, ainsi que certaines recommandations de sécurité et les alertes ne sont pas disponibles.

Pour effectuer une récupération, supprimez Microsoft Monitoring Agent sur les machines virtuelles connectées à l’espace de travail supprimé. Security Center réinstalle l’agent et crée des nouveaux espaces de travail par défaut.

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>Que se passe-t-il si l’agent Microsoft Monitoring Agent est déjà installé en tant qu’extension sur la machine virtuelle ?
Security Center n’écrase pas les connexions existantes des espaces de travail utilisateur. Security Center stocke les données de sécurité de la machine virtuelle dans l’espace de travail déjà connecté.

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>Que se passe-t-il si Microsoft Monitoring Agent est installé sur la machine, mais pas en tant qu’extension ?
Si l’agent Microsoft Monitoring Agent est installé directement sur la machine virtuelle (pas en tant qu’extension Azure), Security Center n’installe pas l’agent Microsoft Monitoring Agent et la surveillance de la sécurité est limitée.

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Quel est l’impact de la suppression de ces extensions ?
Si vous supprimez l’extension Microsoft Monitoring, Security Center n’est pas en mesure de collecter des données de sécurité sur la machine virtuelle et certaines recommandations de sécurité et les alertes ne sont pas disponibles. Sous 24 heures, Security Center détermine que l’extension est absente de la machine virtuelle et la réinstalle.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Comment empêcher l’installation automatique de l’agent et la création de l’espace de travail ?
Vous pouvez désactiver la collecte de données pour vos abonnements dans la stratégie de sécurité, mais ce n’est pas recommandé. La désactivation de la collecte de données a pour effet de limiter les recommandations Security Center et les alertes. La collecte de données est requise pour les abonnements au niveau tarifaire Standard. Pour désactiver la collecte de données :

1. Si votre abonnement est configuré pour le niveau Standard, ouvrez la stratégie de sécurité de cet abonnement, puis sélectionnez le niveau **Gratuit**.

   ![Niveau tarifaire ][1]

2. Ensuite, désactivez la collecte de données en sélectionnant **Non** sur le panneau **Stratégie de sécurité : collecte de données**.

   ![Collecte des données][2]

### <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Comment supprimer des extensions OMS installées par Security Center ?
Vous pouvez supprimer manuellement l’agent Microsoft Monitoring Agent. Ce n’est pas recommandé car cela limite les recommandations de Security Center et les alertes.

> [!NOTE]
> Si la collecte de données est activée, Security Center réinstalle l’agent après l’avoir supprimé.  Vous devez désactiver la collecte de données avant de supprimer manuellement l’agent. Consultez [Comment empêcher l’installation automatique de l’agent et la création de l’espace de travail ?](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation?) pour obtenir des instructions sur la désactivation de la collecte de données.
>
>

Pour supprimer manuellement l’agent :

1.  Dans le portail, ouvrez **Log Analytics**.
2.  Dans le panneau Log Analytics, sélectionnez un espace de travail :
3.  Sélectionnez chaque machine virtuelle que vous ne souhaitez pas surveiller et sélectionnez **Déconnecter**.

   ![Supprimer l’agent][3]

> [!NOTE]
> Si une machine virtuelle Linux possède déjà un agent OM qui n’est pas un agent d’extension, la suppression de l’extension a pour effet de supprimer également l’agent. Le client doit alors le réinstaller.
>
>

## <a name="existing-oms-customers"></a>Clients OMS existants

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Est-ce que Security Center peut écraser les connexions existantes entre les machines virtuelles et les espaces de travail ?
Si l’agent Microsoft Monitoring Agent est déjà installé sur une machine virtuelle comme une extension Azure, Security Center n’écrase pas la connexion à l’espace de travail existante. En revanche, Security Center utilise l’espace de travail existant.

Une solution Security Center est installée sur l’espace de travail si elle ne l’était pas déjà. Celle-ci est appliquée uniquement aux machines virtuelles appropriées. Lorsque vous ajoutez une solution, elle est déployée automatiquement par défaut sur tous les agents Windows et Linux connectés à votre espace de travail Log Analytics. Le [ciblage de solution](../operations-management-suite/operations-management-suite-solution-targeting.md) est une fonctionnalité OMS qui permet d’appliquer une étendue à vos solutions.

Si l’agent Microsoft Monitoring Agent est installé directement sur la machine virtuelle (pas en tant qu’extension Azure), Security Center n’installera pas l’agent Microsoft Monitoring Agent et la surveillance de la sécurité est limitée.

### <a name="what-should-i-do-if-i-suspect-that-the-data-platform-migration-broke-the-connection-between-one-of-my-vms-and-my-workspace"></a>Que dois-je faire si je soupçonne la migration de la plateforme de données d’interférer avec la connexion entre une de mes machines virtuelles et mon espace de travail ?
Cela ne devrait pas se produire. Mais le cas échéant, vous devez [Créer une demande de support Azure](../azure-supportability/how-to-create-azure-support-request.md) et fournir les informations suivantes :

- L’ID de ressource Azure de la machine virtuelle affectée
- L’ID de ressource Azure de l’espace de travail configuré sur l’extension avant que la connexion ne soit interrompue
- L’agent et la version installés précédemment

### <a name="does-security-center-install-solutions-on-my-existing-oms-workspaces-what-are-the-billing-implications"></a>Est-ce que Security Center installe des solutions sur mes espaces de travail OMS existants ? Quelles sont les conséquences sur la facturation ?
Lorsque Security Center détecte qu’une machine virtuelle est déjà connectée à un espace de travail que vous avez créé, il active des solutions sur cet espace de travail en fonction de votre niveau tarifaire. Les solutions sont appliquées uniquement aux machines virtuelles Azure pertinentes, via le [ciblage de solution](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting), de sorte que la facturation reste la même.

- **Niveau Gratuit** : Security Center installe la solution « SecurityCenterFree » sur l’espace de travail. Vous n’êtes pas facturé pour le niveau gratuit.
- **Niveau Standard** : Security Center installe les solutions « SecurityCenterFree » et « Security » sur l’espace de travail.

   ![Solutions sur l’espace de travail par défaut][4]

> [!NOTE]
> La solution « Sécurité » dans Log Analytics correspond à la solution Security & Audit d’OMS.
>
>

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Je dispose déjà d’espaces de travail dans mon environnement. Puis-je les utiliser pour collecter des données de sécurité ?
Si l’agent Microsoft Monitoring Agent est déjà installé comme une extension Azure sur une machine virtuelle, Security Center utilise l’espace de travail connecté existant. Une solution de Security Center est installée sur l’espace de travail si elle ne l’était pas déjà. La solution est appliquée uniquement aux machines virtuelles appropriées via le [ciblage de solution](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting).

Lorsque Security Center installe l’agent Microsoft Monitoring Agent sur des machines virtuelles, il utilise les espaces de travail par défaut créés par Security Center. Les clients seront bientôt en mesure de configurer les espaces de travail utilisés.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Je dispose déjà de la solution de sécurité sur mes espaces de travail. Quelles sont les conséquences sur la facturation ?
La solution Security & Audit permet d’activer les fonctionnalités Security Center de niveau Standard pour les machines virtuelles Azure. Si la solution Security & Audit est déjà installée sur un espace de travail, Security Center utilise la solution existante. Il n’y aura aucune modification de la facturation.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la migration de plateforme Security Center, consultez

- [Migration de plateforme Azure Security Center](security-center-platform-migration.md)
- [Guide de résolution des problèmes d’Azure Security Center](security-center-troubleshooting-guide.md)

<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png

