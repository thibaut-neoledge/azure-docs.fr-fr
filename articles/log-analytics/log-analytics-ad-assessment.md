---
title: Optimisation de votre environnement Active Directory avec Azure Log Analytics | Microsoft Docs
description: "La solution d’évaluation Active Directory permet d’évaluer les risques et l’intégrité de vos environnements de serveurs à intervalles réguliers."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 81eb41b8-eb62-4eb2-9f7b-fde5c89c9b47
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 540180e7d6cd02dfa1f3cac8ccd343e965ded91b
ms.openlocfilehash: 97368f0b9e89ffd0cd982b6e8670d5a1f62ad42c
ms.contentlocale: fr-fr
ms.lasthandoff: 08/16/2017

---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-assessment-solution-in-log-analytics"></a>Optimisez votre environnement Active Directory avec la solution d’évaluation Active Directory dans Log Analytics

![Symbole d’AD Assessment](./media/log-analytics-ad-assessment/ad-assessment-symbol.png)

La solution d’évaluation Active Directory permet d’évaluer les risques et l’intégrité de vos environnements de serveurs à intervalles réguliers. Cet article vous aide à installer et utiliser la solution pour vous permettre de prendre les mesures correctives en vue de régler des problèmes potentiels.

Cette solution fournit une liste hiérarchisée de recommandations propres à votre infrastructure de serveurs déployée. Les recommandations sont classées en quatre domaines pour vous aider à rapidement mesurer les risques et prendre les mesures appropriées.

Les recommandations sont basées sur les connaissances et l'expérience que les ingénieurs de Microsoft ont acquises au contact de milliers de visiteurs. Chaque recommandation explique pourquoi un problème peut vous concerner et comment implémenter les modifications suggérées.

Vous pouvez choisir les domaines les plus importants pour votre organisation et suivre votre progression vers un environnement sans risque et intègre.

Une fois la solution ajoutée et l’évaluation terminée, le résumé des informations de domaines s'affiche sur le tableau de bord **Évaluation AD** de l'infrastructure de votre environnement. Les sections suivantes expliquent comment utiliser les informations du tableau de borde **Évaluation AD** , dans lequel vous pouvez afficher et exécuter les actions recommandées pour votre infrastructure de serveur Active Directory.

![image de la vignette d’évaluation de SQL](./media/log-analytics-ad-assessment/ad-tile.png)

![image du tableau de bord d’évaluation de SQL](./media/log-analytics-ad-assessment/ad-assessment.png)

## <a name="installing-and-configuring-the-solution"></a>Installation et configuration de la solution
Utilisez les informations suivantes pour installer et configurer les solutions.

* Des agents doivent être installés sur les contrôleurs de domaine qui sont membres du domaine à évaluer.
* La solution d'évaluation Active Directory nécessite une version prise en charge de .NET Framework 4 (4.5.2 ou version ultérieure) sur chaque ordinateur qui dispose d'un agent OMS.
* Ajoutez la solution d’évaluation Active Directory à votre espace de travail OMS à partir de [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ADAssessmentOMS?tab=Overview) ou en utilisant le processus décrit dans l’article [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) (Ajouter des solutions Log Analytics à partir de la galerie de solutions).  Aucune configuration supplémentaire n’est requise.

  > [!NOTE]
  > Une fois que vous avez ajouté la solution, le fichier AdvisorAssessment.exe est ajouté aux serveurs comportant des agents. Les données de configuration sont lues puis envoyées au service OMS dans le cloud pour traitement. La logique est appliquée aux données reçues et le service cloud enregistre les données.
  >
  >

## <a name="active-directory-assessment-data-collection-details"></a>Détails de la collecte de données d’évaluation Active Directory

Active Directory Assessment collecte les données provenant des sources suivantes à l’aide des agents que vous avez activés :

- Collecteurs du Registre
- Collecteurs LDAP
- .NET Framework
- Collecteurs de journaux des événements
- Interfaces ADSI (Active Directory Service Interface)
- Windows PowerShell
- Collecteurs de données de fichier
- Windows Management Instrumentation (WMI)
- API de l’outil DCDIAG
- API de service de réplication de fichiers (NTFRS)
- Code C# personnalisé


Le tableau suivant présente les méthodes de collecte de données pour les agents, indique si Operations Manager (SCOM) est requis, et précise la fréquence à laquelle les données sont collectées par un agent.

| plateforme | Agent direct | Agent SCOM | Stockage Azure | SCOM requis ? | Données de l’agent SCOM envoyées via un groupe d’administration | fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |7 jours |

## <a name="understanding-how-recommendations-are-prioritized"></a>Hiérarchisation des recommandations
Une valeur de pondération déterminant l'importance relative de la recommandation est attribuée à chaque recommandation. Seules les 10 recommandations les plus importantes sont affichées.

### <a name="how-weights-are-calculated"></a>Calcul des pondérations
Les pondérations sont des agrégations de valeurs basées sur trois facteurs clés :

* La *probabilité* qu’une anomalie identifiée cause des problèmes. Une plus grande probabilité attribue un score global supérieur à la recommandation.
* L’*impact* de l’anomalie sur votre organisation si elle devait causer des problèmes. Un plus grand impact attribue un score global supérieur à la recommandation.
* L’*effort* requis pour implémenter la recommandation. Un plus grand effort attribue un score global inférieur à la recommandation.

La pondération de chaque recommandation est exprimée en pourcentage du score total disponible pour chaque domaine. Par exemple, si une recommandation dans le domaine de la sécurité et de la conformité a un score de 5 %, l’implémentation de cette recommandation augmente votre score global de sécurité et conformité de 5 %.

### <a name="focus-areas"></a>Domaines
**Sécurité et conformité** : ce domaine présente les recommandations relatives aux menaces de sécurité potentielles et violations de stratégies d’entreprise, ainsi qu’aux exigences techniques, juridiques et réglementaires.

**Disponibilité et continuité d’activité** : ce domaine présente les recommandations relatives à la disponibilité du service, la résilience de votre infrastructure et la protection de votre activité.

**Performances et évolutivité** : ce domaine présente les recommandations pour aider votre organisation à développer son infrastructure informatique, s’assurer que votre environnement informatique répond aux besoins de performances actuels et est en mesure de répondre à l’évolution des besoins d’infrastructure.

**Mise à niveau, migration et déploiement** : ce domaine présente des recommandations pour vous aider à mettre à niveau, migrer et déployer Active Directory dans votre infrastructure existante.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Faut-il viser un score de 100 % dans chaque domaine ?
Pas nécessairement. Les recommandations sont basées sur les connaissances et l'expérience que les ingénieurs de Microsoft ont acquises au contact de milliers de visiteurs. Toutefois, chaque infrastructure de serveur étant différente, certaines recommandations peuvent être plus ou moins adaptées à votre système. Par exemple, il se peut que certaines recommandations de sécurité soient moins appropriées si vos ordinateurs virtuels ne sont pas connectés à Internet. Certaines recommandations de disponibilité peuvent être moins pertinentes pour les services qui fournissent des rapports et des données ad hoc de faible priorité. Les problèmes importants pour une entreprise bien établie peuvent l'être moins pour une start-up. Nous vous conseillons donc d'identifier tout d'abord vos domaines prioritaires, puis d'observer l'évolution de vos résultats au fil du temps.

Chaque recommandation inclut une justification de son importance. Servez-vous de cette explication pour évaluer si la mise en œuvre de la recommandation est importante pour vous, en fonction de la nature de vos services informatiques et des besoins de votre organisation.

## <a name="use-assessment-focus-area-recommendations"></a>Utilisation des recommandations des domaines d'évaluation
Avant de pouvoir utiliser une solution d’évaluation dans OMS, vous devez avoir installé cette solution. Pour plus d’informations sur l’installation des solutions, consultez la rubrique [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md). Une fois le pack installé, vous pouvez afficher un résumé des recommandations à l'aide de la vignette Évaluation de la page Vue d'ensemble d'OMS.

Consultez le résumé des évaluations de conformité pour votre infrastructure, puis explorez les recommandations.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Pour afficher les recommandations relatives à un domaine et prendre des mesures correctives
1. Dans la page **Vue d’ensemble**, cliquez sur la vignette **Évaluation** de votre infrastructure de serveur.
2. Sur la page **Évaluation** , passez en revue les informations de résumé dans l’un des panneaux relatifs à un domaine, puis cliquez sur l’un d’entre eux pour afficher les recommandations y afférentes.
3. Les pages relatives au domaine répertorient les recommandations prioritaires pour votre environnement. Cliquez sur une recommandation sous **Objets affectés** pour en afficher les détails et comprendre pourquoi elle apparaît.  
    ![Image des recommandations d’évaluation](./media/log-analytics-ad-assessment/ad-focus.png)
4. Vous pouvez effectuer les actions correctives suggérées dans **Actions suggérées**. Une fois l’élément traité, les évaluations ultérieures indiquent que des mesures ont été prises et votre score de conformité augmente. Les éléments corrigés apparaissent comme **objets passés**.

## <a name="ignore-recommendations"></a>Ignorer les recommandations
Si vous souhaitez ignorer, des recommandations, vous pouvez créer un fichier texte qu’OMS utilisera pour empêcher les recommandations d'apparaître dans les résultats de votre évaluation.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Pour identifier les recommandations que vous ignorerez
1. Connectez-vous à votre espace de travail et ouvrez Recherche de journal. Utilisez la requête suivante pour répertorier les recommandations qui ont échoué pour les ordinateurs de votre environnement.

   ```
   Type=ADAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```
>[!NOTE]
> Si vous avez mis à niveau votre espace de travail vers le [nouveau langage de requête dans Log Analytics](log-analytics-log-search-upgrade.md), remplacez la requête ci-dessus par la requête ci-dessous.
>
> `ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

   Voici une capture d’écran montrant la requête de recherche de journal : ![Échecs de recommandations](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)
2. Choisissez les recommandations que vous souhaitez ignorer. Vous utiliserez les valeurs RecommendationId dans la procédure suivante.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Pour créer et utiliser un fichier texte IgnoreRecommendations.txt
1. Créez un fichier nommé IgnoreRecommendations.txt.
2. Collez ou saisissez chaque valeur RecommendationId de chaque recommandation que Log Analytics doit ignorer sur une ligne distincte, puis enregistrez et fermez le fichier.
3. Placez le fichier dans le dossier suivant sur chaque ordinateur sur lequel OMS doit ignorer les recommandations.
   * Sur les ordinateurs avec Microsoft Monitoring Agent (connectés directement ou avec Operations Manager) : *lecteur_système*:\Program Files\Microsoft Monitoring Agent\Agent
   * Sur le serveur d’administration Operations Manager : *lecteur_système*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Pour vérifier que les recommandations sont ignorées
Une fois les prochaines évaluations planifiées exécutées, par défaut tous les sept jours, les recommandations spécifiées sont marquées *Ignorées* et n’apparaissent pas dans le tableau de bord d’évaluation.

1. Vous pouvez utiliser les requêtes Recherche de journal suivantes pour répertorier toutes les recommandations ignorées.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
>[!NOTE]
> Si vous avez mis à niveau votre espace de travail vers le [nouveau langage de requête dans Log Analytics](log-analytics-log-search-upgrade.md), remplacez la requête ci-dessus par la requête ci-dessous.
>
> `ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

2. Si vous décidez ultérieurement d’afficher les recommandations ignorées, supprimez tous les fichiers IgnoreRecommendations.txt, ou supprimez les valeurs RecommendationID de ces fichiers.

## <a name="ad-assessment-solutions-faq"></a>FAQ sur les solutions d'évaluation AD
*Quelle est la fréquence d'exécution des évaluations ?*

* L'évaluation s'exécute tous les 7 jours.

*Est-il possible de configurer la fréquence d'exécution de l'évaluation ?*

* Pas pour l'instant.

*Si un autre serveur est découvert après l'ajout d'une solution d'évaluation, ce serveur sera-t-il évalué ?*

* Oui, une fois découverts, les nouveaux serveurs sont évalués tous les 7 jours.

*Si un serveur est désactivé, est-il retiré du processus d'évaluation ?*

* Si un serveur n'envoie pas de données pendant 3 semaines, il est supprimé.

*Quel est le nom du processus qui effectue la collecte de données ?*

* AdvisorAssessment.exe

*Combien de temps la collecte de données prend-elle ?*

* La collecte de données sur le serveur prend environ 1 heure. Cela peut prendre plus de temps sur les serveurs dotés d'un grand nombre de serveurs Active Directory.

*Est-il possible de configurer les périodes de collecte de données ?*

* Pas pour l'instant.

*Pourquoi afficher uniquement les 10 premières recommandations ?*

* Au lieu d'exploiter une liste de tâches trop importante, nous vous recommandons de vous concentrer sur les recommandations prioritaires. Une fois que vous les aurez suivies, de nouvelles recommandations apparaîtront. Si vous préférez afficher la liste détaillée de toutes les recommandations, utilisez Recherche de journal.

*Est-il possible d'ignorer une recommandation ?*

* Oui, consultez la section [Ignorer les recommandations](#ignore-recommendations) ci-dessus.

## <a name="next-steps"></a>Étapes suivantes
* Utilisez [Recherches de journal dans Log Analytics](log-analytics-log-searches.md) pour afficher les données d’évaluation AD détaillées ainsi que les recommandations.

