---
title: "Solution de sécurité et d’audit d’Operations Management Suite - Base de référence web | Microsoft Docs"
description: "Ce document explique comment utiliser la solution de sécurité et d’audit d’Operations Management Suite (OMS) pour effectuer une évaluation de la base de référence web pour l’ensemble des serveurs web surveillés, afin de déterminer leur niveau de conformité et de sécurité."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ROBOTS: NOINDEX
redirect_url: https://www.microsoft.com/cloud-platform/security-and-compliance
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 0d4707dc0c0ffbf40d0d10a6d12b9709a9655258
ms.contentlocale: fr-fr
ms.lasthandoff: 05/03/2017


---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Évaluation de la base de référence web dans la solution de sécurité et d’audit d’Operations Management Suite
Ce document vous aide à utiliser les capacités d’évaluation de la base de référence web de [la solution de sécurité et d’audit d’Operations Management Suite](operations-management-suite-overview.md) pour accéder à l’état de sécurité des ressources analysées.

## <a name="what-is-web-baseline-assessment"></a>Qu’est-ce qu’une évaluation de la base de référence web ?
La solution de sécurité d’OMS permet actuellement d’évaluer la base de référence de sécurité pour les systèmes d’exploitation. Elle analyse les paramètres de système d’exploitation de vos serveurs toutes les 24 heures et affiche les paramètres potentiellement vulnérables. Pour plus d’informations à ce sujet, consultez [Évaluation de la base de référence dans la solution de sécurité et d’audit d’Operations Management Suite](oms-security-baseline.md).

L’objectif d’une évaluation de la base de référence web consiste à identifier les paramètres de serveur web potentiellement vulnérables. On distingue trois principales sources pour les configurations de base de référence web : les configurations .NET, ASP.NET et IIS.  Tout comme l’évaluation de la base de référence du système d’exploitation, la solution de sécurité d’OMS va analyser vos serveurs web toutes les 24 heures et fournir un aperçu de leur état de sécurité.  Dans Internet Information Services (IIS), les configurations sont hautement personnalisables, ce qui permet de remplacer différents niveaux de site et d’application. L’analyseur vérifie les paramètres au niveau de chaque site/application en complément du niveau racine par défaut. Vous pouvez ainsi identifier l’emplacement des paramètres potentiellement vulnérables et y remédier rapidement.


## <a name="web-security-baseline-assessment"></a>Évaluation de la base de référence de la sécurité web
Pour cette version préliminaire, cette fonctionnalité sera accessible à l’aide de l’option de recherche d’OMS. Suivez les étapes ci-dessous pour exécuter la requête appropriée :

1. Dans le tableau de bord principal de **Microsoft Operations Management Suite**, cliquez sur la vignette **Sécurité et audit**.
2. Dans le tableau de bord **Sécurité et audit**, cliquez sur le bouton **Recherche dans les journaux**.
3. La première requête que vous pouvez utiliser est le **résumé de l’évaluation de la base de référence Web**. Dans le champ **Begin search here** (Lancer la recherche ici), tapez la requête suivante : Type*=SecurityBaselineSummary BaselineType=web*. L’écran suivant présente un exemple de sortie :

![Résumé de l’évaluation de la base de référence web](./media/oms-security-web-baseline/oms-security-web-baseline-fig1-new.png)

> [!NOTE]
> Dans cette requête, chaque enregistrement indique un récapitulatif de l’évaluation sur un serveur donné.

Dans la zone **Recherche dans les journaux**, vous pouvez taper différentes requêtes pour obtenir davantage d’informations sur l’évaluation de la base de référence web. En plus de la requête précédente, vous pouvez également utiliser les requêtes suivantes dans cette version préliminaire.

**Évaluation de la règle de base de référence web** : chaque enregistrement représente une seule évaluation de la règle de base de référence web sur un seul serveur. Il inclut toutes les données de la règle, l’emplacement, le résultat attendu et le résultat réel.

**Requête** : Type*=SecurityBaseline BaselineType=web*

![Évaluation de la règle de base de référence web](./media/oms-security-web-baseline/oms-security-web-baseline-fig2.png)

**Afficher tous les résultats d’un serveur spécifique** : cette requête montre comment afficher les résultats d’un serveur spécifique.

**Requête** : *Type=SecurityBaseline BaselineType=web Computer=BaselineTestVM1*

![Tous les résultats](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

Vous pouvez également utiliser ces enregistrements/requêtes pour créer vos propres tableaux de bord, rapports ou alertes. L’écran ci-dessous présente un exemple de contrôle d’interface utilisateur que vous pouvez ajouter à votre tableau de bord. Pour savoir comment visualiser vos données à l’aide du Concepteur de vue d’OMS, cliquez [ici](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/). L’écran ci-dessous montre à quoi ressemble la mosaïque une fois que vous avez effectué cette personnalisation.

![Exemple d’interface utilisateur](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

> [!NOTE]
> Si vous souhaitez connaître les paramètres qui sont vérifiés pour l’évaluation de la base de référence, vous pouvez télécharger [cette feuille de calcul Excel](https://gallery.technet.microsoft.com/OMS-Web-Baseline-1e811690) qui contient ces paramètres.

## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez découvert la fonction d’évaluation de la base de référence web de la solution de sécurité et d’audit d’OMS. Pour plus d’informations sur la sécurité OMS, consultez les articles suivants :

* [Présentation - Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Surveiller et répondre aux alertes de sécurité dans la solution de sécurité et d’audit d’Operations Management Suite](oms-security-responding-alerts.md)
* [Surveillance des ressources dans la solution de sécurité et d’audit d’Operations Management Suite](oms-security-monitoring-resources.md)


