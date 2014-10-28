<properties linkid="web-sites-create-web-jobs" urlDisplayName="Use WebJobs to run background tasks in Microsoft Azure Websites" pageTitle="Use WebJobs to run background tasks in Microsoft Azure Websites" metaKeywords="Microsoft Azure Web Sites, Web Jobs, background tasks" description="Learn how to run background tasks in Microsoft Azure Websites." metaCanonical="" services="web-sites" documentationCenter="" title="Use WebJobs to run background tasks in Microsoft Azure Websites" authors="cephalin"  solutions="" writer="timamm" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Utilisation de WebJobs pour exécuter des tâches en arrière-plan dans les sites Web Azure

Microsoft Azure WebSites propose trois méthodes pour exécuter des programmes ou des scripts dans votre site Web Azure : à la demande, en continu ou par planification. L'utilisation de Microsoft Azure WebJobs n'entraine pas de frais supplémentaires, sauf si vous souhaitez activer la fonctionnalité Always On décrite plus bas dans cet article.

## Sommaire

-   [Types de fichier acceptables pour les scripts][]
-   [Création d'une tâche à la demande][]
-   [Création d'une tâche exécutée en continu][]
-   [Création d'une tâche planifiée][]
    -   [Tâches planifiées et Azure Scheduler][]
-   [Affichage de l'historique des tâches][]
-   [Remarques][]
-   [Étapes suivantes][]
    -   [Amélioration des résultats avec le Kit de développement logiciel (SDK) Azure WebJobs][]
    -   [Autres méthodes de déploiement][]
    -   [Ressources supplémentaires][]

<a name="acceptablefiles"></a>

## Types de fichier acceptables pour les scripts

Les types de fichier suivants sont acceptés en tant que scripts exécutables :

.cmd, .bat, .exe (en utilisant une commande Windows)

.ps1 (en utilisant Powershell)

.sh (en utilisant un interpréteur de commandes)

.php (en utilisant PHP)

.py (en utilisant Python)

.js (en utilisant Node)

<a name="CreateOnDemand"></a>

## Création d'une tâche à la demande

1.  Dans la barre de commandes de la page **WebJobs**, cliquez sur **Ajouter**. La boîte de dialogue **New Job** s'affiche.

    ![Tâche à la demande][]

2.  Sous **Nom**, entrez un nom pour la tâche. Le nom doit commencer par une lettre ou un chiffre et ne peut pas contenir de caractères spéciaux, à part les tirets et les traits de soulignement (« - » et « \_ »).

3.  Dans la zone **Content (Zip Files - 100MB Max)**, accédez au fichier .zip contenant votre script. Ce fichier .zip doit contenir votre exécutable (.exe .cmd .bat .sh .php .py .js) ainsi que les fichiers de prise en charge requis pour exécuter le programme ou le script.

4.  Dans la zone **How to Run**, sélectionnez **Run on Demand**.

5.  Pour télécharger le script vers votre site Web, activez la case à cocher située dans le coin inférieur droit de la boîte de dialogue. Le nom que vous avez indiqué pour cette tâche apparaît dans la liste :

    ![Liste des tâches][]

6.  Pour exécuter le script, sélectionnez son nom dans la liste, puis cliquez sur **Exécuter une fois** dans la barre de commandes située en bas de la page du portail.

    ![Exécuter une fois][]

<a name="CreateContinuous"></a>

## Création d'une tâche exécutée en continu

1.  Pour créer une tâche exécutée en continu, procédez comme pour la création d'une tâche à exécution unique, mais dans la zone **How to Run**, sélectionnez **Run continuously**.

    ![Nouvelle tâche en continu][]

2.  Pour démarrer ou arrêter une tâche exécutée en continu, sélectionnez-la dans la liste, puis cliquez sur **Démarrer** ou **Arrêter** dans la barre de commandes.

> [WACOM.NOTE] Si votre site Web est exécuté sur plusieurs instances, une tâche exécutée en continu le sera sur toutes vos instances. Les tâches à la demande et planifiées sont exécutées sur une seule instance sélectionnée pour l'équilibrage de charge par Microsoft Azure.

> [WACOM.NOTE]
> Pour les tâches en continu, nous vous recommandons d'activer **Always On** sur la page Configurer pour votre site Web. La fonctionnalité Always On, disponible en mode Basique et Standard, empêche le déchargement des sites Web, même s'ils sont inactifs depuis longtemps. Si votre site Web est toujours chargé, votre tâche exécutée en continu sera plus fiable.

<a name="CreateScheduled"></a>

## Création d'une tâche planifiée

1.  Pour créer une tâche planifiée, procédez comme pour les tâches précédentes, mais dans la zone **How to Run**, sélectionnez **Run on a schedule**.

    ![Nouvelle tâche planifiée][]

2.  Sélectionnez la **région Scheduler** pour votre tâche, puis cliquez sur la flèche située dans le coin inférieur droit de la boîte de dialogue pour passer à l'écran suivant.

3.  Dans la boîte de dialogue **Create Job**, sélectionnez un type de **périodicité** : **One-time job** ou **Recurring job**.

    ![Périodicité de la planificiation][]

4.  Sélectionnez également une date/heure de **début** : **Now** ou **At a specific time**.

    ![Heure de début de la planification][]

5.  Si vous voulez démarrer votre tâche à un moment précis, sélectionnez la date/heure de début sous **Starting On**.

    ![Début de la planification à une heure spécifique][]

6.  Si vous sélectionnez une tâche récurrente, l'option **Recur Every** vous permet d'indiquer la fréquence d'occurrence, tandis que l'option **Ending On** vous permet d'indiquer une date/heure de fin.

    ![Périodicité de la planificiation][1]

7.  Si vous sélectionnez **Semaines**, vous pouvez sélectionner la zone **On a Particular Schedule** et indiquer les jours de la semaine où vous voulez exécuter la tâche.

    ![Planifier les jours de la semaine][]

8.  Si vous sélectionnez **Mois** et la zone **On a Particular Schedule**, vous pouvez configurer la tâche pour qu'elle démarre certains **jours** du mois.

    ![Planifier à des dates spécifiques dans le mois][]

9.  Si vous sélectionnez **Jours de la semaine**, vous pouvez choisir les jours de la semaine durant lesquels exécuter la tâche.

    ![Planifier des jours de semaine spécifiques dans le mois][]

10. Enfin, vous pouvez également utiliser l'option **Occurrences** pour choisir la semaine du mois (première, deuxième, troisième, etc.) durant laquelle exécuter la tâche, les jours de votre choix.

    ![Planifier des jours de semaine spécifiques certaines semaines du mois][]

11. Lorsque vous avez créé une ou plusieurs tâches, leurs noms sont affichés sous l'onglet WebJobs avec leur statut, leur type de planification et d'autres informations. L'historique répertorie les 30 dernières tâches.

    ![Liste des tâches][2]

<a name="Scheduler"></a>

### Tâches planifiées et Azure Scheduler

Les tâches planifiées peuvent être configurées davantage dans le portail d'Azure Scheduler.

1.  Sur la page WebJobs, cliquez sur le lien **schedule** de la tâche pour accéder à la page du portail d'Azure Scheduler.

    ![Lien vers Azure Scheduler][]

2.  Sur la page Scheduler, cliquez sur la tâche.

    ![Tâches sur la page du portail Scheduler][]

3.  La page **Job Action** s'ouvre : vous pouvez y configurer les tâches plus en détail.

    ![Action de tâche PageInScheduler][]

<!-- ================ ViewJobHistory ================= -->

<a name="ViewJobHistory"></a>

## Affichage de l'historique des tâches

1.  Pour afficher l'historique d'exécution d'une tâche, notamment celui des tâches créées avec le Kit de développement logiciel (SDK) WebJobs, cliquez sur le lien correspondant sous la colonne **Logs**. Vous pouvez utiliser l'icône de Presse-papiers pour copier l'URL de la page du fichier journal dans le Presse-papiers.

    ![Lien vers les journaux][]

2.  En cliquant sur le lien, vous ouvrez la page de détail WebJobs pour la tâche. Cette page permet de savoir le nom de la commande exécutée, la date/heure de sa dernière exécution et si elle a réussi ou échoué. Sous **Recent job runs**, cliquez une fois pour afficher les détails supplémentaires.

    ![WebJobDetails][]

3.  La page **WebJob Run Details** apparaît. Cliquez sur **Toggle Output** pour afficher le texte du contenu du journal. Le journal de sortie est au format texte.

    ![Détails d'exécution de la tâche WebJob][]

4.  Pour afficher le texte de sortie dans une nouvelle fenêtre de navigateur, cliquez sur le lien **télécharger**. Pour télécharger le texte, cliquez avec le bouton droit sur le lien et utilisez les options de votre navigateur pour enregistrer le contenu du fichier.

    ![Télécharger la sortie du journal][]

5.  Le lien **WebJobs** situé en haut de la page permet d'obtenir une liste des tâches Web sur le tableau de bord d'historique.

    ![Lien vers la liste des tâches Web][]

    ![Liste des tâches dans le tableau de bord d'historique][]

    En cliquant sur l'un de ces liens, vous pouvez accéder à la page Détails de WebJob pour la tâche sélectionnée.

<a name="WHPNotes"></a>

## Remarques

-   Depuis mars 2014, les sites Web en mode Gratuit peuvent expirer si, pendant 20 minutes, aucune requête à destination du site (de déploiement) SCM et de son portail n'est ouverte dans Azure. Cette situation ne sera pas annulée par les requêtes à destination du site actif.

-   Le code d'une tâche en continu doit être écrit pour s'exécuter dans une boucle infinie.

-   Les tâches en continu sont uniquement exécutées comme tel lorsque le site est opérationnel.

-   Les modes Basique et Standard proposent la fonctionnalité Always On qui, lorsqu'elle est activée, empêche les sites de devenir inactifs.

<a name="NextSteps"></a>

## Étapes suivantes

<!-- ======= Do More with the Microsoft Azure WebJobs SDK ======== -->

<a name="WebJobsSDK"></a>

### Amélioration des résultats avec le Kit de développement logiciel (SDK) Azure WebJobs

Le Kit de développement logiciel (SDK) Azure WebJobs simplifie la tâche de programmation d'un WebJob fonctionnant avec les files d'attente, objets blob et tables de stockage Azure ou les files d'attente Azure Service Bus. Le tableau de bord, qui est maintenant intégré au portail Azure, permet d'améliorer la surveillance et les diagnostics des programmes que vous écrivez en utilisant le Kit de développement logiciel (SDK). Les fonctionnalités de surveillance et de diagnostics sont basées sur le Kit de développement logiciel (SDK) et ne nécessitent pas l'ajout de code spécifique à votre programme.

Pour plus d'informations, consultez le didacticiel [Mise en route du Kit de développement logiciel (SDK) WebJobs Azure][].

<!-- =========== Alternative Methods of Deployment ============= -->

<a name="AlternateDeployments"></a>

### Autres méthodes de déploiement

Visual Studio 2013 comprend des fonctionnalités permettant d'automatiser le déploiement de projets d'application console sous forme de WebJobs. Pour plus d'informations, consultez [Déploiement d'Azure WebJobs vers les sites Web Azure][].

Vous pouvez également utiliser FTP, Git ou Web Deploy pour le déploiement des WebJobs. Pour plus d’informations, consultez les pages [Déploiement Git d'une application de console .NET vers Azure en utilisant WebJobs][] et [Déploiement d'Azure WebJobs][].

<a name="AdditionalResources"></a>

### Ressources supplémentaires

-   Pour obtenir une liste annotée des liens vers la fonctionnalité WebJobs, consultez la page [Azure WebJobs - Ressources recommandées][].

-   Vidéos sur WebJobs :

    [Azure WebJobs 101 : notions de base sur WebJobs avec Jamie Espinosa][]

    [Azure WebJobs 102 : tâches WebJobs planifiées et tableau de bord WebJobs avec Jamie Espinosa][]

    [Azure Scheduler 101 : Kevin Lam vous explique comment planifier vos tâches][]

### Prise en main

Pour la prise en main d'Azure, consultez la page [Version d'évaluation gratuite de Microsoft Azure][].

<!-- LINKS --> 
<!-- IMAGES -->

  [Types de fichier acceptables pour les scripts]: #acceptablefiles
  [Création d'une tâche à la demande]: #CreateOnDemand
  [Création d'une tâche exécutée en continu]: #CreateContinuous
  [Création d'une tâche planifiée]: #CreateScheduled
  [Tâches planifiées et Azure Scheduler]: #Scheduler
  [Affichage de l'historique des tâches]: #ViewJobHistory
  [Remarques]: #WHPNotes
  [Étapes suivantes]: #NextSteps
  [Amélioration des résultats avec le Kit de développement logiciel (SDK) Azure WebJobs]: #WebJobsSDK
  [Autres méthodes de déploiement]: #AlternateDeployments
  [Ressources supplémentaires]: #AdditionalResources
  [Tâche à la demande]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
  [Liste des tâches]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
  [Exécuter une fois]: ./media/web-sites-create-web-jobs/13RunOnce.png
  [Nouvelle tâche en continu]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
  [Nouvelle tâche planifiée]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
  [Périodicité de la planificiation]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
  [Heure de début de la planification]: ./media/web-sites-create-web-jobs/06SchdStart.png
  [Début de la planification à une heure spécifique]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
  [1]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
  [Planifier les jours de la semaine]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
  [Planifier à des dates spécifiques dans le mois]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
  [Planifier des jours de semaine spécifiques dans le mois]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
  [Planifier des jours de semaine spécifiques certaines semaines du mois]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
  [2]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
  [Lien vers Azure Scheduler]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
  [Tâches sur la page du portail Scheduler]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
  [Action de tâche PageInScheduler]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png
  [Lien vers les journaux]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
  [WebJobDetails]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
  [Détails d'exécution de la tâche WebJob]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
  [Télécharger la sortie du journal]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
  [Lien vers la liste des tâches Web]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
  [Liste des tâches dans le tableau de bord d'historique]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
  [Mise en route du Kit de développement logiciel (SDK) WebJobs Azure]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [Déploiement d'Azure WebJobs vers les sites Web Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/websites-dotnet-deploy-webjobs
  [Déploiement Git d'une application de console .NET vers Azure en utilisant WebJobs]: http://blog.amitapple.com/post/73574681678/git-deploy-console-app
  [Déploiement d'Azure WebJobs]: http://blog.amitapple.com/post/74215124623/deploy-azure-webjobs
  [Azure WebJobs - Ressources recommandées]: http://go.microsoft.com/fwlink/?LinkId=390226
  [Azure WebJobs 101 : notions de base sur WebJobs avec Jamie Espinosa]: http://www.windowsazure.com/fr-fr/documentation/videos/azure-webjobs-basics/
  [Azure WebJobs 102 : tâches WebJobs planifiées et tableau de bord WebJobs avec Jamie Espinosa]: http://www.windowsazure.com/fr-fr/documentation/videos/azure-webjobs-schedule-and-dashboard/
  [Azure Scheduler 101 : Kevin Lam vous explique comment planifier vos tâches]: http://www.windowsazure.com/fr-fr/documentation/videos/azure-scheduler-how-to/
  [Version d'évaluation gratuite de Microsoft Azure]: http://azure.microsoft.com/fr-fr/pricing/free-trial/
