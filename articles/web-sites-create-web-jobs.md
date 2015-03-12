<properties 
	pageTitle="Utilisation de WebJobs pour exécuter des tâches en arrière-plan dans Sites Web Microsoft Azure" 
	description="Découvrez comment exécuter des tâches en arrière-plan dans Sites Web Microsoft Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="timamm" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/12/2014" 
	ms.author="cephalin"/>

#Utilisation de WebJobs pour exécuter des tâches en arrière-plan dans Sites Web Azure

Le service Sites Web Azure propose trois méthodes pour exécuter des programmes ou des scripts dans votre site Web : à la demande, en continu ou selon un programme. L'utilisation de Microsoft Azure WebJobs n'entraîne aucun coût supplémentaire.

Cet article explique comment déployer WebJobs à l'aide du portail de gestion Azure. Pour plus d'informations sur le déploiement à l'aide de Visual Studio ou d'un processus de livraison continue, consultez la page [Déploiement d'Azure WebJobs sur des sites Web Azure](http://azure.microsoft.com/documentation/articles/websites-dotnet-deploy-webjobs).

Le Kit de développement logiciel (SDK) Azure WebJobs simplifie de nombreuses tâches de programmation. Pour plus d'informations, consultez [Présentation du Kit de développement logiciel (SDK) WebJobs](../websites-dotnet-webjobs-sdk).

## Sommaire ##
- [Types de fichier acceptables pour les Scripts](#acceptablefiles)
- [Création d'une tâche à la demande](#CreateOnDemand)
- [Création d'une tâche exécutée en continu](#CreateContinuous)
- [Création d'une tâche planifiée](#CreateScheduled)
	- [Tâches planifiées et Azure Scheduler](#Scheduler)
- [Affichage de l'historique des tâches](#ViewJobHistory)
- [Remarques](#WHPNotes)
- [Étapes suivantes](#NextSteps)

## <a name="acceptablefiles"></a>Types de fichier acceptables pour les scripts ou les programmes

Les types de fichier suivants sont acceptés :

* .cmd, .bat, .exe (en utilisant une commande Windows)
* .ps1 (en utilisant Powershell)
* .sh (en utilisant un interpréteur de commandes)
* .php (en utilisant PHP)
* .py (en utilisant Python)
* .js (en utilisant Node)

## <a name="CreateOnDemand"></a>Création d'une tâche à la demande

1. Dans la barre de commandes de la page **WebJobs**, cliquez sur **Ajouter**. La boîte de dialogue **Nouvelle tâche** s'affiche.
	
	![On Demand Task][OnDemandWebJob]
	
2. Sous **Nom**, entrez un nom pour la tâche. Le nom doit commencer par une lettre ou un chiffre et ne peut pas contenir de caractères spéciaux, à part les tirets et les traits de soulignement ("-" et "_").
	
3. Dans la zone **Contenu (fichiers .zip - 100 Mo Max.)**, accédez au fichier .zip contenant votre script. Ce fichier .zip doit contenir votre exécutable (.exe .cmd .bat .sh .php .py .js) ainsi que les fichiers de prise en charge requis pour exécuter le programme ou le script.
	
4. Dans la zone **Mode d'exécution**, sélectionnez **Exécuter à la demande**.
	
5. Pour télécharger le script vers votre site Web, activez la case à cocher située dans le coin inférieur droit de la boîte de dialogue. Le nom que vous avez indiqué pour cette tâche apparaît dans la liste :
	
	![Task List][WebJobsList]
	
6. Pour exécuter le script, sélectionnez son nom dans la liste, puis cliquez sur **Exécuter une fois** dans la barre de commandes située en bas de la page du portail.
	
	![Run Once][RunOnce]

## <a name="CreateContinuous"></a>Création d'une tâche exécutée en continu

1. Pour créer une tâche exécutée en continu, procédez comme pour la création d'une tâche à exécution unique, mais dans la zone **Mode d'exécution**, sélectionnez **Exécuter en continu**.
	
	![New Continuous Task][NewContinuousJob]
	
2. Pour démarrer ou arrêter une tâche exécutée en continu, sélectionnez-la dans la liste, puis cliquez sur **Démarrer** ou **Arrêter** dans la barre de commandes.

> [AZURE.NOTE] Si votre site Web est exécuté sur plusieurs instances, une tâche exécutée en continu le sera sur toutes vos instances. Les tâches à la demande et planifiées sont exécutées sur une seule instance sélectionnée pour l'équilibrage de charge par Microsoft Azure.

> [AZURE.NOTE]
> Pour les tâches en continu, il est recommandé d'activer **Toujours actif** sur la page Configurer pour votre site Web. La fonctionnalité Toujours actif, disponible en mode Basique et Standard, empêche le déchargement des sites Web, même s'ils sont inactifs depuis longtemps. Si votre site Web est toujours chargé, votre tâche exécutée en continu sera plus fiable. 

## <a name="CreateScheduled"></a>Création d'une tâche planifiée

1. Pour créer une tâche planifiée, procédez comme pour les tâches précédentes, mais dans la zone **Mode d'exécution**, sélectionnez **Exécuter selon une planification**.
	
	![New Scheduled Job][NewScheduledJob]
	
2. Sélectionnez la **région Scheduler** pour votre tâche, puis cliquez sur la flèche située dans le coin inférieur droit de la boîte de dialogue pour passer à l'écran suivant.

3. Dans la boîte de dialogue **Créer une tâche**, sélectionnez un type de **périodicité** : **Tâche ponctuelle** ou **Tâche périodique**.
	
	![Schedule Recurrence][SchdRecurrence]
	
4. Sélectionnez également une date/heure de **début** : **Maintenant** ou **À une heure spécifique**.
	
	![Schedule Start Time][SchdStart]
	
5. Si vous voulez démarrer votre tâche à un moment précis, sélectionnez la date/heure de début sous **Heure de début**.
	
	![Schedule Start at a Specific Time][SchdStartOn]
	
6. Si vous sélectionnez une tâche récurrente, l'option **Tou(te)s les** vous permet d'indiquer la fréquence d'occurrence, tandis que l'option **Heure de fin** vous permet d'indiquer une date/heure de fin.
	
	![Schedule Recurrence][SchdRecurEvery]
	
7. Si vous sélectionnez **Semaines**, vous pouvez sélectionner la zone **Dans une planification donnée** et indiquer les jours de la semaine où vous voulez exécuter la tâche.
	
	![Schedule Days of the Week][SchdWeeksOnParticular]
	
8. Si vous sélectionnez **Mois** et la zone **Dans une planification donnée**, vous pouvez configurer la tâche pour qu'elle démarre certains **jours** du mois. 
	
	![Schedule Particular Dates in the Month][SchdMonthsOnPartDays]
	
9. Si vous sélectionnez **Jours de la semaine**, vous pouvez choisir les jours de la semaine durant lesquels exécuter la tâche.
	
	![Schedule Particular Week Days in a Month][SchdMonthsOnPartWeekDays]
	
10. Enfin, vous pouvez également utiliser l'option **Occurrences** pour choisir la semaine du mois (première, deuxième, troisième, etc.) durant laquelle exécuter la tâche, les jours de votre choix.
	
	![Schedule Particular Week Days on Particular Weeks in a Month][SchdMonthsOnPartWeekDaysOccurences]
	
11. Lorsque vous avez créé une ou plusieurs tâches, leurs noms sont affichés sous l'onglet WebJobs avec leur statut, leur type de planification et d'autres informations. L'historique répertorie les 30 dernières tâches.
	
	![Jobs list][WebJobsListWithSeveralJobs]
	
### <a name="Scheduler"></a>Tâches planifiées et Azure Scheduler

Les tâches planifiées peuvent être configurées davantage dans le portail d'Azure Scheduler.

1.	Sur la page WebJobs, cliquez sur le lien **planifier** de la tâche pour accéder à la page du portail d'Azure Scheduler. 
	
	![Link to Azure Scheduler][LinkToScheduler]
	
2. Sur la page Scheduler, cliquez sur la tâche.
	
	![Job on the Scheduler portal page][SchedulerPortal]
	
3. La page **Action de tâche** s'ouvre : vous pouvez y configurer les tâches plus en détail. 
	
	![Job Action PageInScheduler][JobActionPageInScheduler]
	
## <a name="ViewJobHistory"></a>Affichage de l'historique des tâches

1. Pour afficher l'historique d'exécution d'une tâche, notamment celui des tâches créées avec le Kit de développement logiciel (SDK) WebJobs, cliquez sur le lien correspondant sous la colonne **Journaux**. Vous pouvez utiliser l'icône de Presse-papiers pour copier l'URL de la page du fichier journal dans le Presse-papiers.
	
	![Logs Link][WebJobLogs]
		
2. En cliquant sur le lien, vous ouvrez la page de détail WebJobs pour la tâche. Cette page permet de savoir le nom de la commande exécutée, la date/heure de sa dernière exécution et si elle a réussi ou échoué. Sous **Exécutions de tâche récentes**, cliquez une fois pour afficher les détails supplémentaires.
	
	![WebJobDetails][WebJobDetails]
	
3. La page **Détails d'exécution de tâche Web** apparaît. Cliquez sur **Activer/désactiver la sortie** pour afficher le texte du contenu du journal. Le journal de sortie est au format texte. 
	
	![Web job run details][WebJobRunDetails]
	
4. Pour afficher le texte de sortie dans une nouvelle fenêtre de navigateur, cliquez sur le lien **télécharger**. Pour télécharger le texte, cliquez avec le bouton droit sur le lien et utilisez les options de votre navigateur pour enregistrer le contenu du fichier.
	
	![Download log output][DownloadLogOutput]
	
5. Le lien **Tâches Web** situé en haut de la page permet d'obtenir une liste des tâches Web sur le tableau de bord d'historique.
	
	![Link to web jobs list][WebJobsLinkToDashboardList]
	
	![List of jobs in history dashboard][WebJobsListInJobsDashboard]
	
	En cliquant sur l'un de ces liens, vous pouvez accéder à la page Détails de WebJob pour la tâche sélectionnée.


## <a name="WHPNotes"></a>Remarques
	
- Depuis mars 2014, les sites Web en mode Gratuit peuvent expirer si, pendant 20 minutes, aucune requête à destination du site (de déploiement) SCM et de son portail n'est ouverte dans Azure. Cette situation ne sera pas annulée par les requêtes à destination du site actif.
- Le code d'une tâche en continu doit être écrit pour s'exécuter dans une boucle infinie.
- Les tâches en continu sont uniquement exécutées comme telles lorsque le site est opérationnel.
- Les modes Basique et Standard proposent la fonctionnalité Toujours actif qui, lorsqu'elle est activée, empêche les sites de devenir inactifs.

## <a name="NextSteps"></a>Étapes suivantes
 
Pour plus d'informations, consultez la page [Ressources recommandées pour Azure WebJobs][WebJobsRecommendedResources].

[PSonWebJobs]:http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx
[WebJobsRecommendedResources]:http://go.microsoft.com/fwlink/?LinkId=390226

[OnDemandWebJob]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
[WebJobsList]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
[NewContinuousJob]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
[NewScheduledJob]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
[SchdRecurrence]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
[SchdStart]: ./media/web-sites-create-web-jobs/06SchdStart.png
[SchdStartOn]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
[SchdRecurEvery]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
[SchdWeeksOnParticular]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
[SchdMonthsOnPartDays]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
[SchdMonthsOnPartWeekDays]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
[SchdMonthsOnPartWeekDaysOccurences]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
[RunOnce]: ./media/web-sites-create-web-jobs/13RunOnce.png
[WebJobsListWithSeveralJobs]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
[WebJobLogs]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
[WebJobDetails]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
[WebJobRunDetails]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
[DownloadLogOutput]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
[WebJobsLinkToDashboardList]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
[WebJobsListInJobsDashboard]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
[LinkToScheduler]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
[SchedulerPortal]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
[JobActionPageInScheduler]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png


<!--HONumber=42-->
