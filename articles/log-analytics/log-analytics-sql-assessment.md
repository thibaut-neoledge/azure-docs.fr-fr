<properties
	pageTitle="Optimiser votre environnement avec la solution d’évaluation de SQL dans Log Analytics | Microsoft Azure"
	description="La solution d’évaluation de SQL permet d’évaluer les risques et l’intégrité de vos environnements de serveurs à intervalles réguliers."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Optimiser votre environnement avec la solution d’évaluation de SQL dans Log Analytics


La solution d’évaluation de SQL permet d’évaluer les risques et l’intégrité de vos environnements de serveurs à intervalles réguliers. Cet article vous aidera à installer la solution pour vous permettre de prendre les mesures correctives pour régler des problèmes potentiels.

Cette solution fournit une liste hiérarchisée de recommandations propres à votre infrastructure de serveurs déployée. Les recommandations sont classées en six domaines pour vous aider à rapidement mesurer les risques et prendre les mesures correctives appropriées.

Les recommandations proposées sont basées sur les connaissances et l'expérience que les ingénieurs de Microsoft ont acquises au contact de milliers de visiteurs. Chaque recommandation explique pourquoi un problème peut vous concerner et comment implémenter les modifications suggérées.

Vous pouvez choisir les domaines les plus importants pour votre organisation et suivre votre progression vers un environnement sans risque et intègre.

Une fois la solution ajoutée et l’évaluation terminée, le résumé des informations de domaines s'affiche sur le tableau de bord **Évaluation de SQL** de l'infrastructure de votre environnement. Les sections suivantes expliquent comment utiliser les informations du tableau de bord **Évaluation de SQL**, dans lequel vous pouvez afficher et exécuter les actions recommandées pour votre infrastructure de serveur SQL.

![image de la vignette d’évaluation de SQL](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![image du tableau de bord d’évaluation de SQL](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## Installation et configuration de la solution
L’évaluation de SQL est compatible avec toutes les versions actuellement prises en charge de SQL Server pour les éditions Standard, Developer et Enterprise.

Utilisez les informations suivantes pour installer et configurer la solution.

- La solution d'évaluation de SQL nécessite l’installation de .NET Framework 4 sur chaque ordinateur qui dispose d'un agent OMS.
- Lorsque vous utilisez l'agent Operations Manager avec SQL Assessment, vous devez utiliser un compte d’identification Operations Manager. Consultez la rubrique [Comptes d’identification Operations Manager pour OMS](#operations-manager-run-as-accounts-for-oms) ci-dessous pour plus d’informations.

    >[AZURE.NOTE] L'agent MMA ne prend pas en charge les comptes d’identification Operations Manager.

- Ajoutez la solution d'évaluation de SQL à votre espace de travail OMS en utilisant le processus décrit dans la rubrique [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md). Aucune configuration supplémentaire n’est requise.

>[AZURE.NOTE] Une fois que vous avez ajouté la solution, le fichier AdvisorAssessment.exe est ajouté aux serveurs comportant des agents. Les données de configuration sont lues puis envoyées au service OMS dans le cloud pour traitement. La logique est appliquée aux données reçues et le service cloud enregistre les données.

## Détails de l'évaluation SQL de la collection de données

Le tableau suivant présente les méthodes de collecte de données pour les agents, indique si Operations Manager (SCOM) est requis, et précise la fréquence à laquelle les données sont collectées par un agent.

| plateforme | Agent direct | Agent SCOM | Azure Storage | SCOM requis ? | Données de l'agent SCOM envoyées via un groupe d'administration | fréquence de collecte |
|---|---|---|---|---|---|---|
|Windows|![Oui](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![Oui](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![Non](./media/log-analytics-sql-assessment/oms-bullet-red.png)|	![Non](./media/log-analytics-sql-assessment/oms-bullet-red.png)|![Oui](./media/log-analytics-sql-assessment/oms-bullet-green.png)|	7 jours|

## Comptes d’identification Operations Manager pour OMS

Log Analytics dans OMS utilise le groupe de gestion et l’agent d’Operations Manager pour collecter et envoyer des données au service OMS. OMS utilise les packs d’administration pour les charges de travail afin de fournir des services à valeur ajoutée. Chaque charge de travail nécessite des privilèges spécifiques à la charge de travail pour exécuter les packs d’administration dans un contexte de sécurité différent, comme un compte de domaine. Vous devez fournir les informations d’identification en configurant un compte d’identification Operations Manager.

Utilisez les informations suivantes pour définir le compte d'identification Operations Manager pour l'évaluation de SQL.

### Définir le compte d’identification pour l’évaluation de SQL

 Si vous utilisez déjà le pack d’administration de SQL Server, vous devez utiliser ce compte d’identification.

#### Configuration du compte d’identification SQL dans la console Operations

1. Dans Operations Manager, ouvrez la console Operations, puis cliquez sur **Administration**.

2. Sous **Configuration d'identification**, cliquez sur **Profils**, puis ouvrez **Profil d'identification de l'évaluation SQL OMS**.

3. Dans la page **Comptes d’identification**, cliquez sur **Ajouter**.

4. Sélectionnez un compte d'identification Windows qui contient les informations d'identification nécessaires pour SQL Server ou cliquez sur **Nouveau** pour en créer un.
	>[AZURE.NOTE] Le type de compte d’identification doit être Windows. Le compte d’identification doit également faire partie du groupe Administrateurs local sur tous les serveurs Windows hébergeant des instances de SQL Server.

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
#### To configure the SQL Run As account using Windows PowerShell

Open a PowerShell window and run the following script after you’ve updated it with your information:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## Understanding how recommendations are prioritized

Every recommendation made is given a weighting value that identifies the relative importance of the recommendation. Only the ten most important recommendations are shown.

### How weights are calculated

Weightings are aggregate values based on three key factors:

- The *probability* that an issue identified will cause problems. A higher probability equates to a larger overall score for the recommendation.

- The *impact* of the issue on your organization if it does cause a problem. A higher impact equates to a larger overall score for the recommendation.

- The *effort* required to implement the recommendation. A higher effort equates to a smaller overall score for the recommendation.

The weighting for each recommendation is expressed as a percentage of the total score available for each focus area. For example, if a recommendation in the Security and Compliance focus area has a score of 5%, implementing that recommendation will increase your overall Security and Compliance score by 5%.

### Focus areas

**Security and Compliance** - This focus area shows recommendations for potential security threats and breaches, corporate policies, and technical, legal and regulatory compliance requirements.

**Availability and Business Continuity** - This focus area shows recommendations for service availability, resiliency of your infrastructure, and business protection.

**Performance and Scalability** - This focus area shows recommendations to help your organization's IT infrastructure grow, ensure that your IT environment meets current performance requirements, and is able to respond to changing infrastructure needs.

**Upgrade, Migration and Deployment** - This focus area shows recommendations to help you upgrade, migrate, and deploy SQL Server to your existing infrastructure.

**Operations and Monitoring** - This focus area shows recommendations to help streamline your IT operations, implement preventative maintenance, and maximize performance.

**Change and Configuration Management** - This focus area shows recommendations to help protect day-to-day operations, ensure that changes don't negatively affect your infrastructure, establish change control procedures, and to track and audit system configurations.

### Should you aim to score 100% in every focus area?

Not necessarily. The recommendations are based on the knowledge and experiences gained by Microsoft engineers across thousands of customer visits. However, no two server infrastructures are the same, and specific recommendations may be more or less relevant to you. For example, some security recommendations might be less relevant if your virtual machines are not exposed to the Internet. Some availability recommendations may be less relevant for services that provide low priority ad hoc data collection and reporting. Issues that are important to a mature business may be less important to a start-up. You may want to identify which focus areas are your priorities and then look at how your scores change over time.

Every recommendation includes guidance about why it is important. You should use this guidance to evaluate whether implementing the recommendation is appropriate for you, given the nature of your IT services and the business needs of your organization.

## Use assessment focus area recommendations

Before you can use an assessment solution in OMS, you must have the solution installed. To read more about installing solutions, see [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md). After it is installed, you can view the summary of recommendations by using the SQL Assessment tile on the Overview page in OMS.

View the summarized compliance assessments for your infrastructure and then drill-into recommendations.

### To view recommendations for a focus area and take corrective action

1. On the **Overview** page, click the **SQL Assessment** tile.
2. On the **SQL Assessment** page, review the summary information in one of the focus area blades and then click one to view recommendations for that focus area.
3. On any of the focus area pages, you can view the prioritized recommendations made for your environment. Click a recommendation under **Affected Objects** to view details about why the recommendation is made.  
    ![image of SQL Assessment recommendations](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. You can take corrective actions suggested in **Suggested Actions**. When the item has been addressed, later assessments will record that recommended actions were taken and your compliance score will increase. Corrected items appear as **Passed Objects**.

## Ignore recommendations

If you have recommendations that you want to ignore, you can create a text file that OMS will use to prevent recommendations from appearing in your assessment results.

### To identify recommendations that you will ignore

1.	Sign in to your workspace and open Log Search. Use the following query to list recommendations that have failed for computers in your environment.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Here's a screen shot showing the Log Search query:
    ![failed recommendations](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)

2.	Choisissez les recommandations que vous souhaitez ignorer. Vous utiliserez les valeurs RecommendationId dans la procédure suivante.


### Pour créer et utiliser un fichier texte IgnoreRecommendations.txt

1.	Créez un fichier nommé IgnoreRecommendations.txt.
2.	Collez ou saisissez chaque valeur RecommendationId de chaque recommandation qu’OMS doit ignorer sur une ligne distincte, puis enregistrez et fermez le fichier.
3.	Placez le fichier dans le dossier suivant sur chaque ordinateur sur lequel OMS doit ignorer les recommandations.
    - Sur les ordinateurs avec Microsoft Monitoring Agent (connectés directement ou via Operations Manager) : *lecteur\_système*:\\Program Files\\Microsoft Monitoring Agent\\Agent
    - Sur le serveur d'administration Operations Manager : *lecteur\_système*:\\Program Files\\Microsoft System Center 2012 R2\\Operations Manager\\Server

### Pour vérifier que les recommandations sont ignorées

1.	Une fois les prochaines évaluations planifiées exécutées, par défaut tous les 7 jours, les recommandations spécifiées sont marquées Ignored (ignorées) et n'apparaissent pas dans le tableau de bord d'évaluation.
2.	Vous pouvez utiliser les requêtes Recherche de journal suivantes pour répertorier toutes les recommandations ignorées.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
3.	Si vous décidez ultérieurement d’afficher les recommandations ignorées, supprimez tous les fichiers IgnoreRecommendations.txt, ou supprimez les valeurs RecommendationID de ces fichiers.

## FAQ sur les solutions d'évaluation de SQL

*Quelle est la fréquence d'exécution des évaluations ?*
- L'évaluation s'exécute tous les 7 jours.

*Est-il possible de configurer la fréquence d'exécution de l'évaluation ?*
- Pas pour l'instant.

*Si un autre serveur est découvert après l'ajout d'une solution d'évaluation de SQL, ce serveur sera-t-il évalué ?*
- Oui, une fois découverts, les nouveaux serveurs sont évalués tous les 7 jours.

*Si un serveur est désactivé, est-il retiré du processus d'évaluation ?*
- Si un serveur n'envoie pas de données pendant 3 semaines, il est supprimé.

*Quel est le nom du processus qui effectue la collecte de données ?*
- AdvisorAssessment.exe

*Combien de temps la collecte de données prend-elle ?*
- La collecte de données sur le serveur prend environ 1 heure. Cela peut prendre plus de temps sur les serveurs dotés d'un grand nombre d'instances SQL ou de bases de données.

*Quels types de données sont collectés ?*
- Les types de données suivants sont collectés :
    - WMI
    - Registre
    - Compteurs de performances
    - Vues de gestion dynamique (DMV) SQL

*Est-il possible de configurer les périodes de collecte de données ?*
- Pas pour l'instant.

*Pourquoi faut-il configurer un compte d'identification ?*
- Pour SQL Server, un petit nombre de requêtes SQL est exécuté. Pour permettre cette exécution, vous devez utiliser un compte d'identification avec les autorisations AFFICHER L'ÉTAT DU SERVEUR. En outre, pour les requêtes WMI, des informations d'identification d'administrateur local sont requises.

*Pourquoi afficher uniquement les 10 premières recommandations ?*
- Au lieu d'exploiter une liste de tâches trop importante, nous vous recommandons de vous concentrer sur les recommandations prioritaires. Une fois que vous les aurez suivies, de nouvelles recommandations apparaîtront. Si vous préférez afficher la liste détaillée des recommandations, utilisez la recherche de journal d'OMS.

*Est-il possible d'ignorer une recommandation ?*
- Oui, consultez la section [Ignorer les recommandations](#ignore-recommendations) ci-dessus.



## Étapes suivantes

- [Recherche des journaux](log-analytics-log-searches.md) pour afficher les données d'évaluation de SQL détaillées et des recommandations.

<!---HONumber=AcomDC_0504_2016-->