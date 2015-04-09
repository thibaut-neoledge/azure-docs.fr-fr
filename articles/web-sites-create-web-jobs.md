<properties 
	pageTitle="Exécuter des tâches en arrière-plan avec les tâches web" 
	description="Découvrez comment exécuter des tâches en arrière-plan dans les applications web Azure." 
	services="app-service\web" 
	documentationCenter="" 
	authors="tdykstra" 
	writer="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Exécuter des tâches en arrière-plan avec les tâches web

## Vue d'ensemble

Vous pouvez exécuter des programmes ou des scripts dans des tâches web de votre application web [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) de trois façons : à la demande, de façon continue, ou de façon planifiée. L'utilisation des tâches web n'entraîne aucun coût supplémentaire.

Cet article explique comment déployer des tâches web à l'aide du [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Pour plus d'informations sur le déploiement à l'aide de Visual Studio ou d'un processus de livraison continue, consultez la rubrique [Déploiement de tâches web Azure dans Web Apps](websites-dotnet-deploy-webjobs.md).

Le Kit de développement logiciel (SDK) Azure WebJobs simplifie de nombreuses tâches de programmation. Pour plus d'informations, consultez [Présentation du Kit de développement logiciel (SDK) WebJobs](websites-dotnet-webjobs-sdk.md).

## <a name="acceptablefiles"></a>Types de fichier acceptables pour des scripts ou des programmes

Les types de fichier suivants sont acceptés :

* .cmd, .bat, .exe (en utilisant une commande Windows)
* .ps1 (en utilisant Powershell)
* .sh (en utilisant un interpréteur de commandes)
* .php (en utilisant PHP)
* .py (en utilisant Python)
* .js (en utilisant Node)

## <a name="CreateOnDemand"></a>Créer une tâche web à la demande dans le portail

1. Dans le panneau **Web App** du [portail Azure](http://portal.azure.com), cliquez sur **Tous les paramètres > Tâches web** pour afficher le panneau **Tâches web**.
	
	![WebJob blade](./media/web-sites-create-web-jobs/wjblade.png)
	
5. Cliquez sur **Ajouter**. La boîte de dialogue d'ajout de tâche web **** s'affiche.
	
	![Add WebJob blade](./media/web-sites-create-web-jobs/addwjblade.png)
	
2. Sous **Nom**, indiquez le nom de la tâche web. Le nom doit commencer par une lettre ou un chiffre et ne peut pas contenir de caractères spéciaux, à part les tirets et les traits de soulignement (" - " et " _ ").
	
4. Dans la zone **Mode d'exécution**, sélectionnez **Exécuter à la demande**.
	
3. Dans la zone **Téléchargement de fichier**, cliquez sur l'icône du dossier et recherchez le fichier .zip qui contient votre script. Ce fichier .zip doit contenir votre exécutable (.exe .cmd .bat .sh .php .py .js) ainsi que les fichiers de prise en charge requis pour exécuter le programme ou le script.
	
5. Cochez la case **Créer** pour télécharger le script dans votre application web. 
	
	Le nom que vous avez indiqué pour la tâche web apparaît dans la liste, dans le panneau **Tâches web**
	
6. Pour exécuter la tâche web, cliquez sur son nom dans la liste avec le bouton droit, puis cliquez sur **Exécuter**.
	
	![Run WebJob](./media/web-sites-create-web-jobs/runondemand.png)
	
## <a name="CreateContinuous"></a>Créer une tâche web exécutée en continu

1. Pour créer une tâche web exécutée en continu, procédez comme pour la création d'une tâche web à exécution unique, mais dans la zone **Mode d'exécution**, sélectionnez **Exécuter en continu**.

2. Pour démarrer ou arrêter une tâche web exécutée en continu, cliquez avec le bouton droit sur la tâche web dans la liste, puis cliquez sur **Démarrer** ou **Arrêter**.
	
> [AZURE.NOTE] Si votre application web est exécutée sur plusieurs instances, une tâche web exécutée en continu le sera sur toutes vos instances. Les tâches web à la demande et planifiées sont exécutées sur une seule instance sélectionnée pour l'équilibrage de charge par Microsoft Azure.
	
> [AZURE.NOTE] Pour les tâches web en continu, nous vous recommandons d'activer la fonctionnalité **Toujours actif** pour votre application web. Cette fonctionnalité, disponible en mode Basique et Standard, empêche le déchargement des applications web, même si elles sont inactives depuis longtemps. Si votre application web est toujours chargée, votre tâche web exécutée en continu sera plus fiable. 

## <a name="CreateScheduled"></a>Créer une tâche web planifiée

Le portail de gestion Azure ne permet pas encore de créer une tâche web planifiée. Pour le moment, vous pouvez utiliser l'[ancien portail](http://manage.windowsazure.com).

1. Dans l'[ancien portail](http://manage.windowsazure.com), accédez à la page des tâches web, puis cliquez sur **Ajouter**.

1. Dans la zone **Mode d'exécution**, sélectionnez **Exécuter selon une planification**.
	
	![New Scheduled Job][NewScheduledJob]
	
2. Sélectionnez la **région Scheduler** pour votre tâche, puis cliquez sur la flèche située dans le coin inférieur droit de la boîte de dialogue pour passer à l'écran suivant.

3. Dans la boîte de dialogue **Créer une tâche**, sélectionnez un type de **périodicité** : **Tâche ponctuelle** ou **Tâche périodique**.
	
	![Schedule Recurrence][SchdRecurrence]
	
4. Sélectionnez également une date/heure de **début** : **Maintenant** ou **À une heure spécifique**.
	
	![Schedule Start Time][SchdStart]
	
5. Si vous voulez démarrer votre tâche à un moment précis, sélectionnez la date/heure de début sous **Heure de début**.
	
	![Schedule Start at a Specific Time][SchdStartOn]
	
6. Si vous sélectionnez une tâche récurrente, l'option **Survenir chaque** vous permet d'indiquer la fréquence d'occurrence, tandis que l'option **Heure de fin** vous permet d'indiquer une date/heure de fin.
	
	![Schedule Recurrence][SchdRecurEvery]
	
7. Si vous choisissez **Semaines**, vous pouvez sélectionner la zone **Dans une planification donnée** et indiquer les jours de la semaine où vous voulez exécuter la tâche.
	
	![Schedule Days of the Week][SchdWeeksOnParticular]
	
8. Si vous sélectionnez **Mois** et la zone **Dans une planification donnée**, vous pouvez configurer la tâche pour qu'elle démarre certains **jours** du mois. 
	
	![Schedule Particular Dates in the Month][SchdMonthsOnPartDays]
	
9. Si vous sélectionnez **Jours de la semaine**, vous pouvez choisir les jours de la semaine durant lesquels exécuter la tâche.
	
	![Schedule Particular Week Days in a Month][SchdMonthsOnPartWeekDays]
	
10. Enfin, vous pouvez également utiliser l'option **Occurrences** pour choisir la semaine du mois (première, deuxième, troisième, etc.) durant laquelle exécuter la tâche, les jours de votre choix.
	
	![Schedule Particular Week Days on Particular Weeks in a Month][SchdMonthsOnPartWeekDaysOccurences]
	
11. Lorsque vous avez créé une ou plusieurs tâches, leurs noms sont affichés sous l'onglet WebJobs avec leur statut, leur type de planification et d'autres informations. L'historique répertorie les 30 dernières tâches web.
	
	![Jobs list][WebJobsListWithSeveralJobs]
	
### <a name="Scheduler"></a>Tâches planifiées et Azure Scheduler

Les tâches planifiées peuvent être configurées davantage dans les pages Azure Scheduler de l'[ancien portail](http://manage.windowsazure.com).

1.	Sur la page Tâches web, cliquez sur le lien **schedule** de la tâche pour accéder à la page du portail d'Azure Scheduler. 
	
	![Link to Azure Scheduler][LinkToScheduler]
	
2. Sur la page Scheduler, cliquez sur la tâche.
	
	![Job on the Scheduler portal page][SchedulerPortal]
	
3. La page **Action de tâche** s'ouvre : vous pouvez y configurer les tâches plus en détail. 
	
	![Job Action PageInScheduler][JobActionPageInScheduler]
	
## <a name="ViewJobHistory"></a>Afficher l'historique des tâches

1. Pour afficher l'historique d'exécution d'une tâche, notamment celui des tâches créées avec le Kit de développement logiciel (SDK) WebJobs, cliquez sur le lien correspondant sous la colonne **Journaux** du panneau Tâches web. Vous pouvez utiliser l'icône de Presse-papiers pour copier l'URL de la page du fichier journal dans le Presse-papiers.
	
	![Logs link](./media/web-sites-create-web-jobs/wjbladelogslink.png)
		
2. En cliquant sur le lien, vous ouvrez la page de détail pour la tâche web. Cette page permet de savoir le nom de la commande exécutée, la date/heure de sa dernière exécution et si elle a réussi ou échoué. Sous **Exécutions de tâche récentes**, cliquez une fois pour afficher les détails supplémentaires.
	
	![WebJobDetails][WebJobDetails]
	
3. La page **Détails d'exécution de tâche web** apparaît. Cliquez sur l'option d'activation/désactivation de sortie **** pour afficher le texte du contenu du journal. Le journal de sortie est au format texte. 
	
	![Web job run details][WebJobRunDetails]
	
4. Pour afficher le texte de sortie dans une nouvelle fenêtre de navigateur, cliquez sur le lien **télécharger**. Pour télécharger le texte, cliquez avec le bouton droit sur le lien et utilisez les options de votre navigateur pour enregistrer le contenu du fichier.
	
	![Download log output][DownloadLogOutput]
	
5. Le lien **Tâches web** situé en haut de la page permet d'obtenir une liste des tâches web sur le tableau de bord d'historique.
	
	![Link to WebJobs list][WebJobsLinkToDashboardList]
	
	![List of WebJobs in history dashboard][WebJobsListInJobsDashboard]
	
	En cliquant sur l'un de ces liens, vous pouvez accéder à la page Détails de WebJob pour la tâche sélectionnée.


## <a name="WHPNotes"></a>Remarques
	
- Depuis mars 2014, les applications web en mode Gratuit peuvent expirer si, pendant 20 minutes, aucune requête à destination du site (de déploiement) SCM et du portail de l'application web n'est ouverte dans Azure. Cette situation ne sera pas annulée par les requêtes à destination du site actif.
- Le code d'une tâche en continu doit être écrit pour s'exécuter dans une boucle infinie.
- Les tâches en continu sont uniquement exécutées comme tel lorsque l'application web est opérationnelle.
- Les modes Basique et Standard proposent la fonctionnalité Toujours actif qui, lorsqu'elle est activée, empêche les applications web de devenir inactives.

>[AZURE.NOTE] Si vous souhaitez commencer à utiliser Azure App Service avant d'ouvrir un compte Azure, accédez à la page d'[essai d'App Service](http://go.microsoft.com/fwlink/?LinkId=523751) (en anglais), qui vous permet de créer immédiatement une application web de départ de courte durée dans App Service. Aucune carte de crédit n'est requise, et vous ne prenez aucun engagement.

## <a name="NextSteps"></a>Étapes suivantes
 
Pour plus d'informations, consultez la page [Ressources recommandées pour Azure WebJobs][WebJobsRecommendedResources].

## Modifications
* Pour plus d'informations sur les modifications entre Sites Web et App Service, consultez la page : [Azure App Service et son impact sur les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714) (en anglais)
* Pour plus d'informations sur les modifications entre l'ancien portail et le nouveau, consultez la page : [Référence pour la navigation sur la version préliminaire du portail](http://go.microsoft.com/fwlink/?LinkId=529715) (en anglais)

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

<!--HONumber=49-->