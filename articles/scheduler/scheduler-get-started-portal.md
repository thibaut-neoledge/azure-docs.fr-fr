<properties
 pageTitle="Prise en main d’Azure Scheduler dans le portail Azure | Microsoft Azure"
 description="Prise en main d’Azure Scheduler dans le portail Azure"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="08/10/2016"
 ms.author="deli"/>

# Prise en main d’Azure Scheduler dans le portail Azure

Vous pouvez facilement créer des tâches planifiées dans Azure Scheduler. Ce didacticiel vous guide dans la création d’un travail. Vous y découvrirez également les fonctionnalités de gestion et de surveillance de Scheduler.

## Création d’un travail

1.  Connectez-vous au [portail Azure](https://portal.azure.com/).

2.  Cliquez sur **+Nouveau**, entrez _Scheduler_ dans la zone de recherche, sélectionnez **Scheduler** dans la liste de résultats, puis cliquez sur **Créer**.

     ![][marketplace-create]

3.  Nous allons créer un travail qui accède simplement à http://www.microsoft.com/ avec une demande GET. Dans l’écran **Tâche du planificateur**, entrez les informations suivantes :

    1.  **Nom :** `getmicrosoft`

    2.  **Abonnement :** votre abonnement Azure.

    3.  **Collection de tâches :** sélectionnez une collection de tâches existante, ou cliquez sur **Créer** et entrez un nom.

4.  Ensuite, dans **Paramètres d’action**, définissez les valeurs suivantes :

    1.  **Type d’action :** ` HTTP`

    2.  **Méthode :** `GET`

    3.  **URL :** ` http://www.microsoft.com`

      ![][action-settings]

5.  Pour finir, nous allons définir une planification. Il est possible de définir un travail ponctuel, mais nous allons ici sélectionner une planification périodique :

    1. **Périodicité** : `Recurring`

    2. **Début** : date du jour

    3. **Répéter toutes les** : `12 Hours`

    4. **Fin** : deux jours à compter de la date du jour

      ![][recurrence-schedule]

6.  Cliquez sur **Créer**

## Gestion et surveillance des travaux

Une fois créé, le travail apparaît dans le tableau de bord principal d’Azure. Cliquez sur le travail pour ouvrir une nouvelle fenêtre avec les onglets suivants :

1.  Propriétés

2.  Paramètres d’action

3.  Planification

4.  Historique

5.  Utilisateurs

    ![][job-overview]

### Propriétés

Ces propriétés en lecture seule décrivent les métadonnées de gestion du travail Scheduler.

   ![][job-properties]


### Paramètres d'action

Cliquez sur un travail dans l’écran **Travaux** pour configurer ce travail. Cela vous permet de configurer les paramètres avancés, si vous ne les avez pas encore configurés dans l’Assistant de création rapide.

Pour tous les types d’action, vous pouvez modifier la stratégie de nouvelle tentative et l’action d’erreur.

Pour les types d'action de travail HTTP et HTTPS, vous pouvez modifier la méthode de tout verbe HTTP autorisé. Vous pouvez également ajouter, supprimer ou modifier les en-têtes et les informations d'authentification de base.

Pour les types d'action de file d'attente de stockage, vous pouvez modifier le compte de stockage, le nom de file d'attente, le jeton SAS et le corps.

Pour les types d’action Service Bus, vous pouvez modifier l’espace de noms, chemin d’accès de la rubrique/file d’attente, les paramètres d’authentification, le type de transport, les propriétés du message et le corps du message.

   ![][job-action-settings]

### Planification

Cet onglet vous permet de reconfigurer la planification, si vous souhaitez modifier la planification que vous avez créée dans l’Assistant de création rapide.

Vous pouvez en profiter pour [créer des planifications complexes et une périodicité avancée dans votre tâche](scheduler-advanced-complexity.md).

Vous pouvez modifier la date et l'heure de début, la planification de périodicité et la date et l'heure de fin (si le travail est périodique).

   ![][job-schedule]


### Historique

L’onglet **Historique** affiche les mesures sélectionnées pour chaque exécution de la tâche dans le système pour la tâche sélectionnée. Ces mesures fournissent des valeurs en temps réel concernant l’intégrité de votre Scheduler :

1.  État

2.  Détails

3.  Nouvelles tentatives

4.  Occurrence : 1er, 2e, 3e, etc..

5.  Heure de début de l’exécution

6.  Heure de fin de l’exécution

   ![][job-history]

Vous pouvez cliquer sur une exécution pour afficher les **détails de l’historique**, et notamment l’ensemble de la réponse obtenue pour chaque exécution. Cette boîte de dialogue vous permet également de copier la réponse dans le Presse-papiers.

   ![][job-history-details]

### Utilisateurs

Le contrôle d’accès en fonction du rôle (RBAC) Azure permet une gestion précise de l’accès pour Azure Scheduler. Pour savoir comment utiliser l’onglet Utilisateurs, reportez-vous à [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md).


## Voir aussi

 [Présentation d'Azure Scheduler](scheduler-intro.md)

 [Concepts, terminologie et hiérarchie d’entités de Scheduler](scheduler-concepts-terms.md)

 [Plans et facturation dans Azure Scheduler](scheduler-plans-billing.md)

 [Comment créer des planifications complexes et une périodicité avancée avec Azure Scheluler](scheduler-advanced-complexity.md)

 [Informations de référence sur l’API REST de Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Informations de référence sur les applets de commande PowerShell de Scheduler](scheduler-powershell-reference.md)

 [Haute disponibilité et fiabilité de Scheduler](scheduler-high-availability-reliability.md)

 [Limites, valeurs par défaut et codes d’erreur de Scheduler](scheduler-limits-defaults-errors.md)

 [Authentification sortante de Scheduler](scheduler-outbound-authentication.md)


[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png

<!---HONumber=AcomDC_1005_2016-->