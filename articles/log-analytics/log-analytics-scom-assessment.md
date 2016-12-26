---
title: Optimisation de votre environnement avec la solution System Center Operations Manager Assessment dans Log Analytics | Microsoft Docs
description: "La solution System Center Operations Manager Assessment permet d’évaluer les risques et l’intégrité de vos environnements de serveurs à intervalles réguliers."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/18/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 04eebbe19354ab1e927ee2bb41f25dc6676a7c93
ms.openlocfilehash: c28e73f0a6833793561d8702eb6c6480b9fbc0a4


---

# <a name="optimize-your-environment-with-the-system-center-operations-manager-assessment-preview-solution-in-log-analytics"></a>Optimisation de votre environnement avec la solution System Center Operations Manager Assessment (version préliminaire) dans Log Analytics

La solution System Center Operations Manager Assessment permet d’évaluer les risques et l’intégrité de vos environnements de serveurs System Center Operations Manager à intervalles réguliers. Cet article vous aide à installer, à configurer et à utiliser la solution pour vous permettre de prendre les mesures correctives en vue de régler des problèmes potentiels.

Cette solution fournit une liste hiérarchisée de recommandations propres à votre infrastructure de serveurs déployée. Les recommandations sont classées en quatre domaines pour vous aider à rapidement mesurer les risques et prendre les mesures correctives appropriées.

Les recommandations proposées sont basées sur les connaissances et l'expérience que les ingénieurs de Microsoft ont acquises au contact de milliers de visiteurs. Chaque recommandation explique pourquoi un problème peut vous concerner et comment implémenter les modifications suggérées.

Vous pouvez choisir les domaines les plus importants pour votre organisation et suivre votre progression vers un environnement sans risque et intègre.

Une fois la solution ajoutée et l’évaluation terminée, le résumé des informations de domaines s’affiche sur le tableau de bord **System Center Operations Manager Assessment** de votre infrastructure. Les sections suivantes expliquent comment utiliser les informations du tableau de bord **System Center Operations Manager Assessment**, dans lequel vous pouvez afficher et exécuter les actions recommandées pour votre infrastructure SCOM.

![Mosaïque de la solution System Center Operations Manager](./media/log-analytics-scom-assessment/scom-tile.png)

![Tableau de bord System Center Operations Manager Assessment](./media/log-analytics-scom-assessment/scom-dashboard01.png)

## <a name="installing-and-configuring-the-solution"></a>Installation et configuration de la solution

La solution fonctionne avec Microsoft System Operations Manager 2012 R2 et 2012 SP1.

Utilisez les informations suivantes pour installer et configurer la solution.

- Un serveur de gestion Operations Manager d’un groupe Management doit être configuré pour se connecter à OMS. Pour connecter Operations Manager Management Server à OMS, consultez [Connexion d’Operations Manager à Log Analytics](log-analytics-om-agents.md#connecting-operations-manager-to-oms).
    - Si vous surveillez plusieurs serveurs de gestion dans un groupe de gestion à l’aide du groupe d’ordinateurs gérés par OMS, assurez-vous que l’évaluation est configurée pour s’exécuter sur un serveur de gestion. Pour plus d’informations, consultez [Configuration de la règle d’évaluation](#configure-the-assessment-rule).
- Avant de pouvoir utiliser une solution d’évaluation dans OMS, vous devez avoir installé cette solution. Pour plus d’informations sur l’installation des solutions, consultez la rubrique [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).
- Lorsque vous utilisez l’agent Operations Manager avec System Center Operations Manager Assessment, vous devez utiliser un compte d’identification Operations Manager. Pour plus d’informations, consultez [Comptes d’identification Operations Manager pour OMS](#operations-manager-run-as-accounts-for-oms).
    >[!NOTE]
    Une fois que vous avez ajouté la solution, le fichier AdvisorAssessment.exe est ajouté au serveur SCOM. Les données de configuration sont lues puis envoyées au service OMS dans le cloud pour traitement. La logique est appliquée aux données reçues et le service cloud enregistre les données.

# <a name="system-center-operations-manager-assessment-data-collection-details"></a>Détails de l’évaluation de la collecte de données System Center Operations Manager

System Center Operations Manager Assessment collecte des données WMI, des données de registre, des données EventLog, des données Operations Manager via Windows PowerShell, les Requêtes SQL, le Collecteur d’informations sur le fichier à l’aide du serveur que vous avez activé.

Le tableau suivant présente les méthodes de collecte de données pour System Center Operations Manager Assessment, et précise la fréquence à laquelle les données sont collectées par un agent.

| plateforme | Agent direct | Agent SCOM | Azure Storage | SCOM requis ? | Données de l’agent SCOM envoyées via un groupe d’administration | fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |  ![Non](./media/log-analytics-scom-assessment/oms-bullet-red.png) | ![Oui](./media/log-analytics-scom-assessment/oms-bullet-green.png)  | ![Non](./media/log-analytics-scom-assessment/oms-bullet-red.png)  |  ![Oui](./media/log-analytics-scom-assessment/oms-bullet-green.png) | ![Non](./media/log-analytics-scom-assessment/oms-bullet-red.png)  | sept jours |

## <a name="operations-manager-run-as-accounts-for-oms"></a>Comptes d’identification Operations Manager pour OMS

OMS utilise les packs d’administration pour les charges de travail afin de fournir des services à valeur ajoutée. Chaque charge de travail nécessite des privilèges spécifiques à la charge de travail pour exécuter les packs d’administration dans un contexte de sécurité différent, comme un compte de domaine. Configurez un compte d’identification Operations Manager pour fournir les informations d’identification.

Utilisez les informations suivantes pour définir le compte d'identification Operations Manager pour System Center Operations Manager Assessment.

### <a name="set-the-run-as-account"></a>Configuration du compte d’identification

1. Dans la console Operations Manager, accédez à l’onglet **Administration**.
2. Sous **Configuration d’identification**, cliquez sur **Comptes**.
3. Créez le compte d’identification, en suivant les étapes de l’Assistant et en créant un compte Windows. Le compte à utiliser est celui identifié et qui dispose de toutes les conditions requises ci-dessous :

    >[!NOTE]
    Le compte d’identification doit respecter les conditions suivantes : - un compte de domaine membre du groupe Administrateurs local sur tous les serveurs dans l’environnement (tous les rôles Operations Manager - Serveur de gestion, Base de données OpsMgr, Entrepôt de données, Création de rapports, Console Web, Passerelle) - Rôle Administrateur Operations Manager pour le groupe de gestion en cours d’évaluation - Rôle SysAdmin sur tous les serveurs SQL ou instances utilisés par Operations Manager

4. Sous **Sécurité de la distribution**, sélectionnez **Plus sécurisé**.
5. Spécifiez le serveur de gestion sur lequel le compte est distribué.
3. Revenez à la Configuration d’identification et cliquez sur **Profils**.
4. Recherchez *Profil d’évaluation SCOM*.
5. Le nom du profil doit être : *Microsoft System Center Advisor SCOM Assessment Run As Profile*.
6. Avec le bouton droit, mettez à jour ses propriétés et ajoutez le nouveau compte d’identification créé à l’étape 3.

### <a name="sql-script-granting-permissions-to-the-run-as-account"></a>Script SQL octroyant des autorisations au compte d’identification

Exécutez le script SQL suivant pour accorder les autorisations requises au compte d’identification sur l’instance SQL utilisée par Operations Manager.

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```


### <a name="configure-the-assessment-rule"></a>Configuration de la règle d’évaluation

Le pack de gestion de la solution System Center Operations Manager Assessment inclut une règle nommée *Microsoft System Center Advisor SCOM Assessment Run Assessment Rule*. Cette règle est responsable de l’exécution de l’évaluation. Pour activer la règle et configurer la fréquence, utilisez les procédures ci-dessous.

Par défaut, Microsoft System Center Advisor SCOM Assessment Run Assessment Rule est désactivée. Pour exécuter l’évaluation, vous devez activer la règle sur un serveur de gestion. Utilisez les étapes suivantes.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Activation de la règle pour un serveur de gestion spécifique

1. Dans l’espace de travail **Création** de la console Operations Manager, recherchez la règle *Microsoft System Center Advisor SCOM Assessment Run Assessment Rule* dans le panneau **Règles**.
2. Dans les résultats de la recherche, sélectionnez le résultat qui inclut le texte *Type : serveur de gestion*.
3. Cliquez sur la règle avec le bouton droit, puis cliquez sur **Substitutions** > **Pour un objet spécifique de la classe : serveur de gestion**.
4.  Dans la liste de serveurs de gestion disponibles, sélectionnez le serveur de gestion où la règle doit être exécutée.
5.  Veillez à modifier la valeur de substitution sur **True** pour la valeur du paramètre **Activé**.  
    ![paramètre de substitution](./media/log-analytics-scom-assessment/rule.png)

Toujours dans cette fenêtre, configurez la fréquence de l’exécution à l’aide de la procédure suivante.

#### <a name="configure-the-run-frequency"></a>Configuration de la fréquence d’exécution

L’évaluation est configurée pour s’exécuter toutes les 10 080 minutes (ou sept jours), l’intervalle par défaut. Vous pouvez substituer la valeur par la valeur minimale de 1 440 minutes (ou un jour). La valeur représente l’intervalle de temps minimal requis entre les exécutions d’évaluation successives. Pour substituer l’intervalle, utilisez les étapes ci-dessous.

1. Dans l’espace de travail **Création** de la console Operations Manager, recherchez la règle *Microsoft System Center Advisor SCOM Assessment Run Assessment Rule* dans le panneau **Règles**.
2. Dans les résultats de la recherche, sélectionnez le résultat qui inclut le texte *Type : serveur de gestion*.
3. Cliquez sur la règle avec le bouton droit, puis cliquez sur **Remplacer la règle** > **Pour tous les objets de la classe : serveur de gestion**.
4. Modifiez la valeur du paramètre **Intervalle** sur la valeur de l’intervalle souhaité. Dans l’exemple ci-dessous, la valeur est définie sur 1 440 minutes (1 jour).  
    ![paramètre d’intervalle](./media/log-analytics-scom-assessment/interval.png)  
    Si la valeur est inférieure à 1 440 minutes, la règle s’exécute selon un intervalle d’une journée. Dans cet exemple, la règle ignore la valeur de l’intervalle et s’exécute à la fréquence d’un jour.


## <a name="understanding-how-recommendations-are-prioritized"></a>Hiérarchisation des recommandations

Une valeur de pondération déterminant l'importance relative de la recommandation est attribuée à chaque recommandation. Seules les 10 recommandations les plus importantes sont affichées.

### <a name="how-weights-are-calculated"></a>Calcul des pondérations

Les pondérations sont des agrégations de valeurs basées sur trois facteurs clés :

- La *probabilité* qu'une anomalie identifiée cause des problèmes. Une plus grande probabilité attribue un score global supérieur à la recommandation.
- L' *impact* de l'anomalie sur votre organisation si elle devait causer des problèmes. Un plus grand impact attribue un score global supérieur à la recommandation.
- L' *effort* requis pour implémenter la recommandation. Un plus grand effort attribue un score global inférieur à la recommandation.

La pondération de chaque recommandation est exprimée en pourcentage du score total disponible pour chaque domaine. Par exemple, si une recommandation dans le domaine Disponibilité et continuité d’activité a un score de 5 %, l’implémentation de cette recommandation augmente votre score Disponibilité et continuité d’activité global de 5 %.

### <a name="focus-areas"></a>Domaines

**Disponibilité et continuité d’activité** : ce domaine présente les recommandations relatives à la disponibilité du service, la résilience de votre infrastructure et la protection de votre activité.

**Performances et évolutivité** : ce domaine présente les recommandations pour aider votre organisation à développer son infrastructure informatique, s’assurer que votre environnement informatique répond aux besoins de performances actuels et est en mesure de répondre à l’évolution des besoins d’infrastructure.

**Mise à niveau, migration et déploiement** : ce domaine présente les recommandations pour vous aider à mettre à niveau, migrer et déployer SQL Server dans votre infrastructure existante.

**Opérations et surveillance** : ce domaine présente des recommandations relatives à la rationalisation de vos opérations informatiques, la mise en place une maintenance préventive et l’optimisation des performances.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Faut-il viser un score de 100 % dans chaque domaine ?

Pas nécessairement. Les recommandations sont basées sur les connaissances et l'expérience que les ingénieurs de Microsoft ont acquises au contact de milliers de visiteurs. Toutefois, chaque infrastructure de serveur étant différente, certaines recommandations peuvent être plus ou moins adaptées à votre système. Par exemple, il se peut que certaines recommandations de sécurité soient moins appropriées si vos ordinateurs virtuels ne sont pas connectés à Internet. Certaines recommandations de disponibilité peuvent être moins pertinentes pour les services qui fournissent des rapports et des données ad hoc de faible priorité. Les problèmes importants pour une entreprise bien établie peuvent l'être moins pour une start-up. Nous vous conseillons donc d'identifier tout d'abord vos domaines prioritaires, puis d'observer l'évolution de vos résultats au fil du temps.

Chaque recommandation inclut une justification de son importance. Utilisez cette explication pour évaluer si la mise en œuvre de la recommandation est importante pour vous, en fonction de la nature de vos services informatiques et des besoins de votre organisation.

## <a name="use-assessment-focus-area-recommendations"></a>Utilisation des recommandations des domaines d'évaluation

Avant de pouvoir utiliser une solution d’évaluation dans OMS, vous devez avoir installé cette solution. Pour plus d’informations sur l’installation des solutions, consultez la rubrique [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md). Une fois le pack installé, vous pouvez afficher un résumé des recommandations à l’aide de la mosaïque System Center Operations Manager Assessment de la page Vue d’ensemble dans OMS.

Consultez le résumé des évaluations de conformité pour votre infrastructure, puis explorez les recommandations.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Pour afficher les recommandations relatives à un domaine et prendre des mesures correctives

1. Dans la page **Vue d’ensemble**, cliquez sur la mosaïque **System Center Operations Manager Assessment**.
2. Dans la page **System Center Operations Manager Assessment**, passez en revue les informations de résumé dans l’un des panneaux relatifs à un domaine, puis cliquez sur l’un d’entre eux pour afficher les recommandations afférentes.
3. Les pages relatives au domaine répertorient les recommandations prioritaires pour votre environnement. Cliquez sur une recommandation sous **Objets affectés** pour en afficher les détails et comprendre pourquoi elle apparaît.  
    ![domaine](./media/log-analytics-scom-assessment/focus-area.png)
4. Vous pouvez effectuer les actions correctives suggérées dans **Actions suggérées**. Une fois l'élément traité, les évaluations ultérieures indiqueront que des mesures ont été prises et votre score de conformité augmentera. Les éléments corrigés apparaissent comme **objets passés**.

## <a name="ignore-recommendations"></a>Ignorer les recommandations

Si vous souhaitez ignorer des recommandations, vous pouvez créer un fichier texte qu’OMS utilise pour empêcher les recommandations d’apparaître dans les résultats de votre évaluation.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Pour identifier les recommandations que vous voulez ignorer

1. Connectez-vous à votre espace de travail et ouvrez Recherche de journal. Utilisez la requête suivante pour répertorier les recommandations qui ont échoué pour les ordinateurs de votre environnement.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Voici une capture d’écran montrant la requête Recherche de journal :   
    ![recherche de journal](./media/log-analytics-scom-assessment/scom-log-search.png)

2. Choisissez les recommandations que vous souhaitez ignorer. Vous utiliserez les valeurs RecommendationId dans la procédure suivante.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Pour créer et utiliser un fichier texte IgnoreRecommendations.txt

1. Créez un fichier nommé IgnoreRecommendations.txt.
2. Collez ou saisissez chaque valeur RecommendationId de chaque recommandation qu’OMS doit ignorer sur une ligne distincte, puis enregistrez et fermez le fichier.
3. Placez le fichier dans le dossier suivant sur chaque ordinateur sur lequel OMS doit ignorer les recommandations.
4. Sur le serveur de gestion Operations Manager : *lecteur_système*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Pour vérifier que les recommandations sont ignorées

1. Une fois la prochaine évaluation planifiée exécutée, par défaut tous les sept jours, les recommandations spécifiées sont marquées comme Ignorées et n’apparaissent pas dans le tableau de bord d’évaluation.
2. Vous pouvez utiliser les requêtes Recherche de journal suivantes pour répertorier toutes les recommandations ignorées.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

3. Si vous décidez ultérieurement d’afficher les recommandations ignorées, supprimez tous les fichiers IgnoreRecommendations.txt, ou supprimez les valeurs RecommendationID de ces fichiers.

## <a name="system-center-operations-manager-assessment-solution-faq"></a>FAQ sur la solution System Center Operations Manager Assessment

*Est-il possible de configurer la fréquence d'exécution de l'évaluation ?* Oui. Consultez [Configuration de la fréquence d’exécution](#configure-the-run-frequency).

*Si un autre serveur est découvert après l’ajout de la solution System Center Operations Manager Assessment, ce serveur sera-t-il évalué ?* Oui, après la découverte, il sera évalué ; par défaut, tous les sept jours.

*Quel est le nom du processus qui effectue la collecte de données ?* AdvisorAssessment.exe

*Où est exécuté le processus AdvisorAssessment.exe ?* AdvisorAssessment.exe s’exécute sous le service de contrôle d’intégrité du serveur de gestion où la règle d’évaluation est activée. À l’aide de ce processus, la découverte de l’ensemble de votre environnement s’effectue par le biais de la collecte de données distante.

*Combien de temps la collecte de données prend-elle ?* La collecte de données sur le serveur prend environ une heure. Elle peut prendre plus de temps dans les environnements comportant plusieurs bases de données ou instances d’Operations Manager.

*Que se passe-t-il si je définis l’intervalle d’évaluation sur une valeur inférieure à 1 440 minutes ?* L’évaluation est préconfigurée pour s’exécuter au maximum une fois par jour. Si vous remplacez la valeur de l’intervalle par une valeur inférieure à 1 440 minutes, l’évaluation utilise alors la valeur de 1 440 minutes en tant que valeur d’intervalle.

*Comment savoir s’il existe des échecs liés aux conditions préalables ?* Si l’évaluation s’est exécutée et qu’aucun résultat ne s’affiche, il est probable que certaines des conditions préalables pour l’évaluation ont échoué. Vous pouvez exécuter les requêtes : `Type=Operation Solution=SCOMAssessment` et `Type=SCOMAssessmentRecommendation FocusArea=Prerequisites` dans la Recherche de journal pour afficher les conditions préalables qui ont échoué.

*Il y a un message `Failed to connect to the SQL Instance (….).` dans les échecs liés aux conditions préalables. Quel est le problème ?* AdvisorAssessment.exe, le processus qui collecte les données, s’exécute sous le service de contrôle d’intégrité du serveur de gestion. Dans le cadre de l’évaluation, le processus tente de se connecter au serveur SQL Server où se trouve la base de données Operations Manager. Cette erreur peut se produire lorsque les règles du pare-feu bloquent la connexion à l’instance SQL Server.

*Quels types de données sont collectés ?* Les types de données suivants sont collectés : - Données WMI - Données de registre - Données EventLog - Données Operations Manager via Windows PowerShell, Requêtes SQL et Collecteur d’informations sur le fichier.

*Pourquoi faut-il configurer un compte d'identification ?* Plusieurs requêtes SQL sont exécutées pour un serveur Operations Manager. Pour permettre leur exécution, vous devez utiliser un compte d’identification avec les autorisations nécessaires. En outre, pour les requêtes WMI, des informations d'identification d'administrateur local sont requises.

*Pourquoi afficher uniquement les 10 premières recommandations ?* Au lieu de vous communiquer une liste de tâches trop importante, nous vous recommandons de vous concentrer sur les recommandations prioritaires. Une fois que vous les aurez suivies, de nouvelles recommandations apparaîtront. Si vous préférez afficher la liste détaillée de toutes les recommandations, utilisez Recherche de journal.

*Est-il possible d'ignorer une recommandation ?* Oui, consultez [Ignorer les recommandations](#Ignore-recommendations).


## <a name="next-steps"></a>Étapes suivantes

- [Lancez une recherche dans les journaux](log-analytics-log-searches.md) pour afficher les données System Center Operations Manager Assessment détaillées et des recommandations.



<!--HONumber=Nov16_HO3-->


