---
title: "Évaluation de la base de référence web dans la base de référence de la solution de sécurité et d’audit d’Operations Management Suite | Microsoft Docs"
description: "Ce document explique comment utiliser l’évaluation de la base de référence web dans la solution de sécurité et d’audit d’Operations Management Suite (OMS) pour effectuer une évaluation de la base de référence de l’ensemble des serveurs web surveillés, afin de déterminer leur niveau de conformité et de sécurité."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 40b0c6ca933ea02ac9f5fe3bfaaf87a310542a8d
ms.contentlocale: fr-fr
ms.lasthandoff: 08/19/2017

---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Évaluation de la base de référence web dans la solution de sécurité et d’audit d’Operations Management Suite
Ce document vous aide à utiliser les capacités d’évaluation de la base de référence web de la solution de sécurité et d’audit OMS pour accéder à l’état de sécurité de vos ressources surveillées.

## <a name="what-is-web-baseline-assessment"></a>Qu’est-ce qu’une évaluation de la base de référence web ?
La solution de sécurité d’OMS permet actuellement d’évaluer la base de référence de sécurité pour les systèmes d’exploitation. Elle analyse les paramètres de système d’exploitation de vos serveurs toutes les 24 heures et affiche les paramètres potentiellement vulnérables. Pour plus d’informations à ce sujet, consultez [Évaluation de la base de référence dans la solution de sécurité et d’audit d’Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/oms-security-baseline).

L’objectif d’une évaluation de la base de référence web consiste à identifier les paramètres de serveur web potentiellement vulnérables. On distingue trois principales sources pour les configurations de base de référence web : les configurations .NET, ASP.NET et IIS.  Tout comme l’évaluation de la base de référence du système d’exploitation, la solution de sécurité d’OMS va analyser vos serveurs web toutes les 24 heures et fournir un aperçu de leur état de sécurité.  Dans Internet Information Services (IIS), les configurations sont hautement personnalisables, ce qui permet de remplacer différents niveaux de site et d’application. L’analyseur vérifie les paramètres au niveau de chaque site/application en complément du niveau racine par défaut. Cela vous aide à identifier les paramètres potentiellement vulnérables et à rapidement les corriger, en suivant également nos recommandations pour ces paramètres.

>[!NOTE] 
>Vous pouvez télécharger les identificateurs de configuration communs et les règles de base utilisés par la solution Sécurité d’OMS sur cette [page](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335?redir=0).


## <a name="web-security-baseline-assessment"></a>Évaluation de la base de référence de la sécurité web

Pour cette préversion, la fonctionnalité est accessible via l’option de recherche d’OMS et le tableau de bord de la solution de sécurité et d’audit d’OMS. Suivez les étapes ci-dessous pour exécuter la requête appropriée :

1. Dans le tableau de bord principal de **Microsoft Operations Management Suite**, cliquez sur la mosaïque **Sécurité et audit**.
2. Dans le tableau de bord **Sécurité et audit**, vous pouvez voir la perspective de la base de référence web en regard de la perspective de la base de référence de système d’exploitation.
   
    ![Évaluation de la base de référence de la sécurité web de la solution de sécurité et d’audit d’OMS](./media/oms-security-web-baseline/oms-security-web-baseline-fig5.png)

3. Le volet gauche affiche le nombre de serveurs web par rapport à la base de référence, le pourcentage moyen de règles transmises à tous les serveurs évalués et la liste des serveurs qui ont été évalués.
4. Le volet droit répertorie les règles uniques qui ont échoué par *Gravité* et *Typerègle*. En cliquant sur l’une des règles du volet droit, les détails de cette règle s’affichent. Un exemple est présenté dans l’image ci-dessous. La règle faisant l’objet de l’évaluation est répertoriée sous *Définition de règle*. Le champ *AzId* est un identificateur unique pour chaque règle utilisée par Microsoft pour le suivi des règles de base de référence. En plus de cela, les utilisateurs peuvent voir le *résultat attendu* (valeur recommandée par Microsoft) et d’autres détails concernant l’impact en matière de sécurité de la règle.
    
    ![Interroger](./media/oms-security-web-baseline/oms-security-web-baseline-fig6.png)

5. Vous pouvez créer vos propres requêtes pour passer en revue les résultats. 

La première requête que vous pouvez utiliser est le **résumé de l’évaluation de la base de référence Web**. Dans le champ **Begin search here** (Lancer la recherche ici), tapez la requête suivante : *Type=SecurityBaselineSummary BaselineType=Web*. Voici un exemple de sortie :

![Résultat de la requête](./media/oms-security-web-baseline/oms-security-web-baseline-fig7.png)

>[!NOTE] 
>Dans cette requête, chaque enregistrement indique un récapitulatif de l’évaluation sur un serveur donné.

Dans la zone **Recherche dans les journaux**, vous pouvez taper différentes requêtes pour obtenir davantage d’informations sur l’évaluation de la base de référence web. En plus de la requête précédente, vous pouvez également utiliser les requêtes suivantes dans cette préversion :

**Évaluation de la règle de base de référence web** : chaque enregistrement représente une seule évaluation de la règle de base de référence web sur un seul serveur. Elle inclut toutes les données pour une règle ayant échoué, le *chemin d’accès du site* sur lequel la règle a été évaluée, le *résultat attendu*et le *résultat réel*.

Requête : *Type=SecurityBaseline BaselineType=Web AnalyzeResult=Failed*

![Résultat de la requête 2](./media/oms-security-web-baseline/oms-security-web-baseline-fig8.png)

**Show all results for a specific server** (Afficher tous les résultats d’un serveur spécifique) : cette requête montre comment afficher les résultats d’un serveur spécifique : requête : *Type=SecurityBaseline BaselineType=Web Computer=BaselineTestVM1*

![Résultat de la requête 3](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

Vous pouvez utiliser ces enregistrements/requêtes pour créer vos propres tableaux de bord, rapports ou alertes. Voici un exemple de contrôle d’interface utilisateur que vous pouvez ajouter à votre tableau de bord. Pour savoir comment visualiser vos données à l’aide du Concepteur de vue d’OMS, cliquez [ici](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/). L’écran ci-dessous montre à quoi ressemble la mosaïque une fois que vous avez effectué cette personnalisation.

![dashboard](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez découvert l’évaluation de la base de référence web de la solution de sécurité et d’audit d’OMS. Pour plus d’informations sur la sécurité OMS, consultez les articles suivants :

* [Présentation - Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Surveiller et répondre aux alertes de sécurité dans la solution de sécurité et d’audit d’Operations Management Suite](oms-security-responding-alerts.md)
* [Surveillance des ressources dans la solution de sécurité et d’audit d’Operations Management Suite](oms-security-monitoring-resources.md)


