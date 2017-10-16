---
title: Optimisation de votre environnement SQL Server avec Azure Log Analytics | Microsoft Docs
description: "Avec Azure Log Analytics, la solution d’évaluation de SQL permet d’évaluer les risques et l’intégrité de vos environnements de serveurs SQL à intervalles réguliers."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: e297eb57-1718-4cfe-a241-b9e84b2c42ac
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d2aed3315fe60ace46dfb4176dc13aa417257b0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="optimize-your-sql-server-environment-with-the-sql-assessment-solution-in-log-analytics"></a>Optimisez votre environnement SQL Server avec la solution d’évaluation de SQL dans Log Analytics

![Symbole de SQL Assessment](./media/log-analytics-sql-assessment/sql-assessment-symbol.png)

La solution d’évaluation de SQL permet d’évaluer les risques et l’intégrité de vos environnements de serveurs à intervalles réguliers. Cet article vous aidera à installer la solution pour vous permettre de prendre les mesures correctives pour régler des problèmes potentiels.

Cette solution fournit une liste hiérarchisée de recommandations propres à votre infrastructure de serveurs déployée. Les recommandations sont classées en six domaines pour vous aider à rapidement mesurer les risques et prendre les mesures correctives appropriées.

Les recommandations proposées sont basées sur les connaissances et l'expérience que les ingénieurs de Microsoft ont acquises au contact de milliers de visiteurs. Chaque recommandation explique pourquoi un problème peut vous concerner et comment implémenter les modifications suggérées.

Vous pouvez choisir les domaines les plus importants pour votre organisation et suivre votre progression vers un environnement sans risque et intègre.

Une fois la solution ajoutée et l’évaluation terminée, le résumé des informations de domaines s'affiche sur le tableau de bord **Évaluation de SQL** de l'infrastructure de votre environnement. Les sections suivantes expliquent comment utiliser les informations du tableau de bord **Évaluation de SQL** , dans lequel vous pouvez afficher et exécuter les actions recommandées pour votre infrastructure de serveur SQL.

![image de la vignette d’évaluation de SQL](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![image du tableau de bord d’évaluation de SQL](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## <a name="installing-and-configuring-the-solution"></a>Installation et configuration de la solution
L’évaluation de SQL est compatible avec toutes les versions actuellement prises en charge de SQL Server pour les éditions Standard, Developer et Enterprise.

Utilisez les informations suivantes pour installer et configurer la solution.

* Les agents doivent être installés sur des serveurs sur lesquels SQL Server est installé.
* La solution d'évaluation de SQL nécessite l’installation d’une version prise en charge de .NET Framework 4 sur chaque ordinateur qui dispose d'un agent OMS.
* Pour installer la solution, l’utilisateur doit être un administrateur ou un collaborateur sur l’abonnement Azure lors de l’utilisation du portail Azure. En outre, l’utilisateur doit être membre du rôle collaborateur ou administrateur de l’espace de travail OMS dans le portail OMS.
* Lorsque vous utilisez l'agent Operations Manager avec SQL Assessment, vous devez utiliser un compte d’identification Operations Manager. Consultez la rubrique [Comptes d’identification Operations Manager pour OMS](#operations-manager-run-as-accounts-for-oms) ci-dessous pour plus d’informations.

  > [!NOTE]
  > L'agent MMA ne prend pas en charge les comptes d’identification Operations Manager.
  >
  >
* Ajoutez la solution d’évaluation de SQL à votre espace de travail OMS en utilisant le processus décrit dans la rubrique [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md). Aucune configuration supplémentaire n’est requise.

> [!NOTE]
> Une fois que vous avez ajouté la solution, le fichier AdvisorAssessment.exe est ajouté aux serveurs comportant des agents. Les données de configuration sont lues puis envoyées au service OMS dans le cloud pour traitement. La logique est appliquée aux données reçues et le service cloud enregistre les données.

## <a name="sql-assessment-data-collection-details"></a>Détails de l'évaluation SQL de la collection de données
L’évaluation SQL collecte les données WMI, les données du registre, les données de performances et les résultats de la vue de gestion dynamique SQL Server à l’aide des agents que vous avez activés.

Le tableau suivant présente les méthodes de collecte de données pour les agents, indique si Operations Manager (SCOM) est requis, et précise la fréquence à laquelle les données sont collectées par un agent.

| plateforme | Agent direct | Agent SCOM | Stockage Azure | SCOM requis ? | Données de l’agent SCOM envoyées via un groupe d’administration | fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  | &#8226; |7 jours |

## <a name="operations-manager-run-as-accounts-for-oms"></a>Comptes d’identification Operations Manager pour OMS
Log Analytics dans OMS utilise le groupe de gestion et l’agent d’Operations Manager pour collecter et envoyer des données au service OMS. OMS utilise les packs d’administration pour les charges de travail afin de fournir des services à valeur ajoutée. Chaque charge de travail nécessite des privilèges spécifiques à la charge de travail pour exécuter les packs d’administration dans un contexte de sécurité différent, comme un compte de domaine. Vous devez fournir les informations d’identification en configurant un compte d’identification Operations Manager.

Utilisez les informations suivantes pour définir le compte d'identification Operations Manager pour l'évaluation de SQL.

### <a name="set-the-run-as-account-for-sql-assessment"></a>Définir le compte d’identification pour l’évaluation de SQL
 Si vous utilisez déjà le pack d’administration de SQL Server, vous devez utiliser ce compte d’identification.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Configuration du compte d’identification SQL dans la console Operations
> [!NOTE]
> Si vous utilisez l’agent direct OMS, plutôt que l’agent SCOM, le pack d’administration s’exécute toujours dans le contexte de sécurité du compte système local. Ignorez les étapes 1 à 5 ci-dessous et exécutez l’exemple de code T-SQL ou PowerShell, en spécifiant NT AUTHORITY\SYSTEM comme nom d’utilisateur.
>
>

1. Dans Operations Manager, ouvrez la console Operations, puis cliquez sur **Administration**.
2. Sous **Configuration d’identification**, cliquez sur **Profils**, puis ouvrez **Profil d’identification de l’évaluation SQL OMS**.
3. Dans la page **Comptes d’identification**, cliquez sur **Ajouter**.
4. Sélectionnez un compte d'identification Windows qui contient les informations d'identification nécessaires pour SQL Server ou cliquez sur **Nouveau** pour en créer un.

   > [!NOTE]
   > Le type de compte d’identification doit être Windows. Le compte d’identification doit également faire partie du groupe Administrateurs local sur tous les serveurs Windows hébergeant des instances de SQL Server.
   >
   >
5. Cliquez sur **Enregistrer**.
6. Modifiez puis exécutez l’exemple T-SQL suivant sur chaque instance de SQL Server afin d’accorder les autorisations minimales requises pour que le compte d’identification puisse effectuer l’évaluation de SQL. Toutefois, vous n’avez pas besoin de faire cela si un compte d’identification fait déjà partie du rôle de serveur sysadmin sur les instances de SQL Server.

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```
#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Configuration du compte d’identification SQL à l’aide de Windows Powershell
Ouvrez une fenêtre PowerShell et exécutez le script suivant après l’avoir mis à jour avec vos informations :

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Hiérarchisation des recommandations
Une valeur de pondération déterminant l'importance relative de la recommandation est attribuée à chaque recommandation. Seules les dix recommandations les plus importantes sont affichées.

### <a name="how-weights-are-calculated"></a>Calcul des pondérations
Les pondérations sont des agrégations de valeurs basées sur trois facteurs clés :

* La *probabilité* qu'une anomalie identifiée cause des problèmes. Une plus grande probabilité attribue un score global supérieur à la recommandation.
* L’*impact* de l’anomalie sur votre organisation si elle devait causer des problèmes. Un plus grand impact attribue un score global supérieur à la recommandation.
* L’*effort* requis pour implémenter la recommandation. Un plus grand effort attribue un score global inférieur à la recommandation.

La pondération de chaque recommandation est exprimée en pourcentage du score total disponible pour chaque domaine. Par exemple, si une recommandation dans le domaine de la sécurité et de la conformité a un score de 5 %, l'implémentation de cette recommandation augmentera votre score global de sécurité et conformité de 5 %.

### <a name="focus-areas"></a>Domaines
**Sécurité et conformité** : ce domaine présente les recommandations relatives aux menaces de sécurité potentielles et violations de stratégies d’entreprise, ainsi qu’aux exigences techniques, juridiques et réglementaires.

**Disponibilité et continuité d’activité** : ce domaine présente les recommandations relatives à la disponibilité du service, la résilience de votre infrastructure et la protection de votre activité.

**Performances et évolutivité** : ce domaine présente les recommandations pour aider votre organisation à développer son infrastructure informatique, s’assurer que votre environnement informatique répond aux besoins de performances actuels et est en mesure de répondre à l’évolution des besoins d’infrastructure.

**Mise à niveau, migration et déploiement** : ce domaine présente les recommandations pour vous aider à mettre à niveau, migrer et déployer SQL Server dans votre infrastructure existante.

**Opérations et surveillance** : ce domaine présente des recommandations relatives à la rationalisation de vos opérations informatiques, la mise en place une maintenance préventive et l’optimisation des performances.

**Gestion des modifications et configuration** : ce domaine présente des recommandations pour vous aider à protéger vos opérations quotidiennes, vous assurer que les modifications n’affectent pas votre infrastructure, établir des procédures de contrôle des modifications, et effectuer le suivi et l’audit des configurations système.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Faut-il viser un score de 100 % dans chaque domaine ?
Pas nécessairement. Les recommandations sont basées sur les connaissances et l'expérience que les ingénieurs de Microsoft ont acquises au contact de milliers de visiteurs. Toutefois, chaque infrastructure de serveur étant différente, certaines recommandations peuvent être plus ou moins adaptées à votre système. Par exemple, il se peut que certaines recommandations de sécurité soient moins appropriées si vos ordinateurs virtuels ne sont pas connectés à Internet. Certaines recommandations de disponibilité peuvent être moins pertinentes pour les services qui fournissent des rapports et des données ad hoc de faible priorité. Les problèmes importants pour une entreprise bien établie peuvent l'être moins pour une start-up. Nous vous conseillons donc d'identifier tout d'abord vos domaines prioritaires, puis d'observer l'évolution de vos résultats au fil du temps.

Chaque recommandation inclut une justification de son importance. Servez-vous de cette explication pour évaluer si la mise en œuvre de la recommandation est importante pour vous, en fonction de la nature de vos services informatiques et des besoins de votre organisation.

## <a name="use-assessment-focus-area-recommendations"></a>Utilisation des recommandations des domaines d'évaluation
Avant de pouvoir utiliser une solution d’évaluation dans OMS, vous devez avoir installé cette solution. Pour plus d’informations sur l’installation des solutions, consultez la rubrique [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md). Une fois le pack installé, vous pouvez afficher un résumé des recommandations à l’aide de la vignette Évaluation SQL de la page Vue d’ensemble d’OMS.

Consultez le résumé des évaluations de conformité pour votre infrastructure, puis explorez les recommandations.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Pour afficher les recommandations relatives à un domaine et prendre des mesures correctives
1. Dans la page **Vue d’ensemble**, cliquez sur la vignette **Évaluation SQL**.
2. Dans la page **Évaluation SQL**, passez en revue les informations de résumé dans l’un des panneaux relatifs à un domaine, puis cliquez sur l’un d’entre eux pour afficher les recommandations y afférentes.
3. Les pages relatives au domaine répertorient les recommandations prioritaires pour votre environnement. Cliquez sur une recommandation sous **Objets affectés** pour en afficher les détails et comprendre pourquoi elle apparaît.  
    ![Image des recommandations de l’évaluation SQL](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. Vous pouvez effectuer les actions correctives suggérées dans **Actions suggérées**. Une fois l'élément traité, les évaluations ultérieures indiqueront que des mesures ont été prises et votre score de conformité augmentera. Les éléments corrigés apparaissent comme **objets passés**.

## <a name="ignore-recommendations"></a>Ignorer les recommandations
Si vous souhaitez ignorer, des recommandations, vous pouvez créer un fichier texte qu’OMS utilisera pour empêcher les recommandations d'apparaître dans les résultats de votre évaluation.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Pour identifier les recommandations que vous ignorerez
1. Connectez-vous à votre espace de travail et ouvrez Recherche de journal. Utilisez la requête suivante pour répertorier les recommandations qui ont échoué pour les ordinateurs de votre environnement.

   ```
   Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```

   Voici une capture d’écran montrant la requête de recherche de journal : ![Échecs de recommandations](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)
2. Choisissez les recommandations que vous souhaitez ignorer. Vous utiliserez les valeurs RecommendationId dans la procédure suivante.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Pour créer et utiliser un fichier texte IgnoreRecommendations.txt
1. Créez un fichier nommé IgnoreRecommendations.txt.
2. Collez ou saisissez chaque valeur RecommendationId de chaque recommandation qu’OMS doit ignorer sur une ligne distincte, puis enregistrez et fermez le fichier.
3. Placez le fichier dans le dossier suivant sur chaque ordinateur sur lequel OMS doit ignorer les recommandations.
   * Sur les ordinateurs avec Microsoft Monitoring Agent (connectés directement ou avec Operations Manager) : *lecteur_système*:\Program Files\Microsoft Monitoring Agent\Agent
   * Sur le serveur d’administration Operations Manager : *lecteur_système*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Pour vérifier que les recommandations sont ignorées
1. Une fois les prochaines évaluations planifiées exécutées, par défaut tous les 7 jours, les recommandations spécifiées sont marquées Ignored (ignorées) et n'apparaissent pas dans le tableau de bord d'évaluation.
2. Vous pouvez utiliser les requêtes Recherche de journal suivantes pour répertorier toutes les recommandations ignorées.

   ```
   Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```
3. Si vous décidez ultérieurement d’afficher les recommandations ignorées, supprimez tous les fichiers IgnoreRecommendations.txt, ou supprimez les valeurs RecommendationID de ces fichiers.

## <a name="sql-assessment-solution-faq"></a>FAQ sur les solutions d'évaluation de SQL
*Quelle est la fréquence d'exécution des évaluations ?*

* L'évaluation s'exécute tous les 7 jours.

*Est-il possible de configurer la fréquence d'exécution de l'évaluation ?*

* Pas pour l'instant.

*Si un autre serveur est découvert après l'ajout d'une solution d'évaluation de SQL, ce serveur sera-t-il évalué ?*

* Oui, une fois découverts, les nouveaux serveurs sont évalués tous les 7 jours.

*Si un serveur est désactivé, est-il retiré du processus d'évaluation ?*

* Si un serveur n'envoie pas de données pendant 3 semaines, il est supprimé.

*Quel est le nom du processus qui effectue la collecte de données ?*

* AdvisorAssessment.exe

*Combien de temps la collecte de données prend-elle ?*

* La collecte de données sur le serveur prend environ 1 heure. Cela peut prendre plus de temps sur les serveurs dotés d'un grand nombre d'instances SQL ou de bases de données.

*Quels types de données sont collectés ?*

* Les types de données suivants sont collectés :
  * WMI
  * Registre
  * Compteurs de performances
  * Vues de gestion dynamique (DMV) SQL

*Est-il possible de configurer les périodes de collecte de données ?*

* Pas pour l'instant.

*Pourquoi faut-il configurer un compte d'identification ?*

* Pour SQL Server, un petit nombre de requêtes SQL est exécuté. Pour permettre cette exécution, vous devez utiliser un compte d'identification avec les autorisations AFFICHER L'ÉTAT DU SERVEUR.  En outre, pour les requêtes WMI, des informations d'identification d'administrateur local sont requises.

*Pourquoi afficher uniquement les 10 premières recommandations ?*

* Au lieu d'exploiter une liste de tâches trop importante, nous vous recommandons de vous concentrer sur les recommandations prioritaires. Une fois que vous les aurez suivies, de nouvelles recommandations apparaîtront. Si vous préférez afficher la liste détaillée des recommandations, utilisez la recherche de journal d'OMS.

*Est-il possible d'ignorer une recommandation ?*

* Oui, consultez la section [Ignorer les recommandations](#ignore-recommendations) ci-dessus.

## <a name="next-steps"></a>Étapes suivantes
* [Lancez une recherche dans les journaux](log-analytics-log-searches.md) pour afficher les données d’évaluation de SQL détaillées et des recommandations.
